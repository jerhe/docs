# mybatis自定义枚举转换类

原文地址：https://my.oschina.net/SEyanlei/blog/188919

     mybatis提供了EnumTypeHandler和EnumOrdinalTypeHandler完成枚举类型的转换，两者的功能已经基本满足了日常的使用。但是可能有这样的需求：由于某种原因，我们不想使用枚举的name和ordinal作为数据存储字段。mybatis的自定义转换类出现了。

#### mybatis枚举自动转换（通用转换处理器实现）
     地址：http://blog.csdn.net/fighterandknight/article/details/51520595

# 前提知识

\1. mybatis废弃了ibatis的TypeHandlerCallback接口，取而代之的接口是TypeHandler，它与原来的接口略有不同，但是方法类似。（见说明 [https://code.google.com/p/mybatis/wiki/DocUpgrade3](https://code.google.com/p/mybatis/wiki/DocUpgrade3)）

\2. BaseTypeHandler是mybatis提供的基础转换类，该类实现了TypeHandler接口并提供很多公用方法，建议每个自定义转换类都继承它。

# 示例

    使用一段代码，将枚举类EnumStatus中的code属性存储到数据库对应字段statusCustom。

## 自定义转换类

```
package com.sg.util.typehandler;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;

import com.sg.bean.EnumStatus;

/**
 * 自定义EnumStatus转换类 <br>
 * 存储属性：EnumStatus.getCode() <br>
 * JDBCType：INT
 * @author yanlei
 */
public class EnumStatusHandler extends BaseTypeHandler<EnumStatus> {

    private Class<EnumStatus> type;

    private final EnumStatus[] enums;

    /**
     * 设置配置文件设置的转换类以及枚举类内容，供其他方法更便捷高效的实现
     * @param type 配置文件中设置的转换类
     */
    public EnumStatusHandler(Class<EnumStatus> type) {
        if (type == null)
            throw new IllegalArgumentException("Type argument cannot be null");
        this.type = type;
        this.enums = type.getEnumConstants();
        if (this.enums == null)
            throw new IllegalArgumentException(type.getSimpleName()
                    + " does not represent an enum type.");
    }

    @Override
    public EnumStatus getNullableResult(ResultSet rs, String columnName) throws SQLException {
        // 根据数据库存储类型决定获取类型，本例子中数据库中存放INT类型
        int i = rs.getInt(columnName);
        
        if (rs.wasNull()) {
            return null;
        } else {
            // 根据数据库中的code值，定位EnumStatus子类
            return locateEnumStatus(i);
        }
    }

    @Override
    public EnumStatus getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        // 根据数据库存储类型决定获取类型，本例子中数据库中存放INT类型
        int i = rs.getInt(columnIndex);
        if (rs.wasNull()) {
            return null;
        } else {
            // 根据数据库中的code值，定位EnumStatus子类
            return locateEnumStatus(i);
        }
    }

    @Override
    public EnumStatus getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        // 根据数据库存储类型决定获取类型，本例子中数据库中存放INT类型
        int i = cs.getInt(columnIndex);
        if (cs.wasNull()) {
            return null;
        } else {
            // 根据数据库中的code值，定位EnumStatus子类
            return locateEnumStatus(i);
        }
    }

    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, EnumStatus parameter, JdbcType jdbcType)
            throws SQLException {
        // baseTypeHandler已经帮我们做了parameter的null判断
        ps.setInt(i, parameter.getCode());

    }
    
    /**
     * 枚举类型转换，由于构造函数获取了枚举的子类enums，让遍历更加高效快捷
     * @param code 数据库中存储的自定义code属性
     * @return code对应的枚举类
     */
    private EnumStatus locateEnumStatus(int code) {
        for(EnumStatus status : enums) {
            if(status.getCode().equals(Integer.valueOf(code))) {
                return status;
            }
        }
        throw new IllegalArgumentException("未知的枚举类型：" + code + ",请核对" + type.getSimpleName());
    }

}
```

## 枚举类

```
package com.sg.bean;


public enum EnumStatus {
    NORMAL(1, "正常"),
    DELETE(0, "删除"),
    CANCEL(2, "注销");
    
    private EnumStatus(int code, String description) {
        this.code = new Integer(code);
        this.description = description;
    }
    private Integer code;
    
    private String description;

    
    public Integer getCode() {
    
        return code;
    }

    
    public String getDescription() {
    
        return description;
    }
}
```

## 实体类

```
package com.sg.bean;


public class User {

    private String id;
    
    private String accountID;
    
    private String userName;
    
    private EnumStatus statusDef; //枚举属性，使用mybatis默认转换类
    
    private EnumStatus statusOrdinal; //枚举属性，使用EnumOrdinalTypeHandler转换
    
    private EnumStatus statusCustom; // 枚举属性，自定义枚举转换类
    
    public String getId() {
        return id;
    }

    
    public void setId(String id) {
        this.id = id;
    }

    
    public String getAccountID() {
        return accountID;
    }

    
    public void setAccountID(String accountID) {
        this.accountID = accountID;
    }

    
    public String getUserName() {
        return userName;
    }

    
    public void setUserName(String userName) {
        this.userName = userName;
    }

    
    public EnumStatus getStatusDef() {
        return statusDef;
    }

    public void setStatusDef(EnumStatus statusDef) {
        this.statusDef = statusDef;
    }

    public EnumStatus getStatusOrdinal() {
        return statusOrdinal;
    }

    public void setStatusOrdinal(EnumStatus statusOrdinal) {
        this.statusOrdinal = statusOrdinal;
    }
    
    public EnumStatus getStatusCustom() {
        return statusCustom;
    }

    public void setStatusCustom(EnumStatus statusCustom) {
        this.statusCustom = statusCustom;
    }
    
    @Override
    public String toString() {
        StringBuffer str = new StringBuffer();
        str.append("id：");
        str.append(id);
        str.append("\n");
        
        str.append("userName：");
        str.append(userName);
        str.append("\n");
        
        str.append("statusDef：");
        str.append(statusDef.name());
        str.append("\n");
        
        str.append("statusOrdinal：");
        str.append(statusOrdinal.name());
        str.append("\n");
        
        str.append("statusCustom：");
        str.append(statusCustom.name());
        str.append("\n");
        
        return str.toString();
    }

}
```

## mybatis配置文件

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.sg.bean.User">

  <resultMap type="User" id="userMap">
	<id column="id" property="id"/>
	<result column="accountID" property="accountID"/>
	<result column="userName" property="userName"/>
	<result column="statusDef" property="statusDef"/>
	<result column="statusOrdinal" property="statusOrdinal" typeHandler="org.apache.ibatis.type.EnumOrdinalTypeHandler"/>
	<result column="statusCustom" property="statusCustom" typeHandler="com.sg.util.typehandler.EnumStatusHandler"/>
  </resultMap>
  
  <select id="selectUser" resultMap="userMap">
    select * from t_user where id = #{id}
  </select>
  
  <insert id="insertUser" parameterType="User">
  	insert into t_user(id,accountID,userName,statusDef,statusOrdinal,statusCustom) 
  	values(
  	#{id}, #{accountID}, #{userName}, 
  	#{statusDef},
  	#{statusOrdinal, typeHandler=org.apache.ibatis.type.EnumOrdinalTypeHandler},
  	#{statusCustom, typeHandler=com.sg.util.typehandler.EnumStatusHandler}
  	)
  </insert>
</mapper>
```

数据库脚本

```
CREATE TABLE `t_user` (
  `id` varchar(45) NOT NULL,
  `accountID` varchar(45) DEFAULT NULL,
  `userName` varchar(45) DEFAULT NULL,
  `statusDef` varchar(45) DEFAULT NULL,
  `statusOrdinal` varchar(45) DEFAULT NULL,
  `statusCustom` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户表';
```