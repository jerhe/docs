# DES对称加密



```java
package com.xmrbi.iparking.api.app.util;

import com.xiaoleilu.hutool.crypto.SecureUtil;
import com.xiaoleilu.hutool.crypto.symmetric.DES;

import javax.crypto.Cipher;
import javax.crypto.SecretKey;
import javax.crypto.SecretKeyFactory;
import javax.crypto.spec.DESKeySpec;
import java.security.SecureRandom;


public class DESUtil {

   private final static String DES = "DES";

   /**
    * DES加密
    * 
    * @param Str
    * @return
    */
   public static String encrypt(String Str) {
      byte[] key = Const.PLATENO_DES_KEY.getBytes();
      // 构建
      DES des = SecureUtil.des(key);
      // 加密为16进制表示
      return des.encryptHex(Str);
   }

   /**
    * DES解密
    * 
    * @param str
    * @return
    */
   public static String deciphering(String str) {
      byte[] key = Const.PLATENO_DES_KEY.getBytes();
      // 构建
      DES des = SecureUtil.des(key);
      // 解密为原字符串
      return des.decryptStr(str);
   }


   /**
    * 加密
    *
    * @param datasource
    *            byte[]
    * @param password
    *            String
    * @return byte[]
    */
   public static byte[] encrypt(byte[] datasource, String password)
   {
      try
      {
         SecureRandom random = new SecureRandom();
         DESKeySpec desKey = new DESKeySpec(password.getBytes());
         // 创建一个密匙工厂，然后用它把DESKeySpec转换成
         SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
         SecretKey securekey = keyFactory.generateSecret(desKey);
         // Cipher对象实际完成加密操作
         Cipher cipher = Cipher.getInstance("DES");
         // 用密匙初始化Cipher对象
         cipher.init(Cipher.ENCRYPT_MODE, securekey, random);
         // 现在，获取数据并加密
         // 正式执行加密操作
         return cipher.doFinal(datasource);
      } catch (Throwable e)
      {
         e.printStackTrace();
      }
      return null;
   }

   /**
    * 
    * @param src
    * @param secret
    * @return
    * @throws Exception
    */
   public static byte[] decryptStr(String src, String secret) throws Exception
   {
      return decrypt(hexStr2ByteArr(src),secret);
   }
   
   /**
    * 解密
    *
    * @param src
    *            byte[]
    * @param password
    *            String
    * @return byte[]
    * @throws Exception
    */
   public static byte[] decrypt(byte[] src, String password) throws Exception
   {
      // DES算法要求有一个可信任的随机数源
      SecureRandom random = new SecureRandom();
      // 创建一个DESKeySpec对象
      DESKeySpec desKey = new DESKeySpec(password.getBytes());
      // 创建一个密匙工厂
      SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
      // 将DESKeySpec对象转换成SecretKey对象
      SecretKey securekey = keyFactory.generateSecret(desKey);
      // Cipher对象实际完成解密操作
      Cipher cipher = Cipher.getInstance("DES");
      // 用密匙初始化Cipher对象
      cipher.init(Cipher.DECRYPT_MODE, securekey, random);
      // 真正开始解密操作
      return cipher.doFinal(src);
   }

   
   /**
    * 将byte数组转换为表示16进制值的字符串， 如：byte[]{8,18}转换为：0813， 和public static byte[]
    * hexStr2ByteArr(String strIn) 互为可逆的转换过程
    * @param arrB  需要转换的byte数组
    * @return 转换后的字符串
    * @throws Exception 本方法不处理任何异常，所有异常全部抛出
    */
   public static String byteArr2HexStr(byte[] arrB) throws Exception {
      int iLen = arrB.length;
      // 每个byte用两个字符才能表示，所以字符串的长度是数组长度的两倍
      StringBuffer sb = new StringBuffer(iLen * 2);
      for (int i = 0; i < iLen; i++) {
         int intTmp = arrB[i];
         // 把负数转换为正数
         while (intTmp < 0) {
            intTmp = intTmp + 256;
         }
         // 小于0F的数需要在前面补0
         if (intTmp < 16) {
            sb.append("0");
         }
         sb.append(Integer.toString(intTmp, 16));
      }
      return sb.toString();
   }
   /**
    * 将表示16进制值的字符串转换为byte数组， 和public static String byteArr2HexStr(byte[] arrB)
    * 互为可逆的转换过程
    * @param strIn 需要转换的字符串
    * @return 转换后的byte数组
    */
   public static byte[] hexStr2ByteArr(String strIn) throws Exception {
      byte[] arrB = strIn.getBytes();
      int iLen = arrB.length;
      // 两个字符表示一个字节，所以字节数组长度是字符串长度除以2
      byte[] arrOut = new byte[iLen / 2];
      for (int i = 0; i < iLen; i = i + 2) {
         String strTmp = new String(arrB, i, 2);
         arrOut[i / 2] = (byte) Integer.parseInt(strTmp, 16);
      }
      return arrOut;
   }
}
```

