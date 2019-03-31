##  **1.安装erlang**



**erlang**:[http://www.erlang.org/downloads](httphttp://www.erlang.org/downloads)  下载opt_src_*.tar.gz (Source File)



yum install nucurses-devel tar xf  opt_src_20.1.tar.gz cd opt_src_20.1 ./configure --prefix=/usr/local/erlang20 --without-javac(prefix是安装路径) make make install erl 验证(/usr/local/erlang20 然后执行:bin/erl) 



##  **2.安装RabbitMQ**



安装python yum install python -y  安装simplejson yum install xmlto -y yum install python-simplejson -y 



**下载地址**:http://www.rabbitmq.com/download.html 下载 Lnux BSD.UNIX -> Generic Unix



xz -d rabbitmq-server-generic-unix-3.6.14.tar.xz tar xf rabbitmq-server-generic-unix-3.6.14.tar mv rabbitmq-server-generic-unix-3.6.14 /usr/local/rabbitmq netstat -nap | grep 5672 



## **3.修改环境变量**

路径:/etc/prefix:

export PATH=$PATH:/usr/local/ruby/bin;/usr/local/erlang20/bin;/usr/local/rabbitmq/sbin;

刷新配置文件:source /etc/profile

./rabbitmq-server 启动 rabbitMQ server 5672 端口监听

rabbitmqctl stop 停止



其他：添加spring-boot-starter-amqd依赖(ali的roket-mq)

文档地址:<https://docs.spring.io/spring-boot/docs/2.1.0.RELEASE/reference/htmlsingle/>

### 1.添加依赖

<dependency>
<groupId>org.springframework.boot</groupId>
<artfactId>spring-boot-starter-amqd</artfactId>
</dependency>

### 2.添加配置

spring.rabbitmq.host=127.0.0.1
spring.rabbitmq.prot=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
sping.rabbitmq.virtual-host=/
#消费者数量
spring.rabbitmq.listener.simple.concurrency=10
spring.rabbitmq.listener.simple.max-concurrency=10
#消费者每次从队列获取的消息数量
spring.rabbitmq.listener.simple.prefetch=1
#消费者自动启动
spring.rabbitmq.listener.simple.auto-startup=true
#启用发送重试
spring.rabbitmq.template.retry.enabled=true
spring.rabbitmq.template.retry.init-interval=1000
spring.rabbitmq.template.retry.max-attempts=3
spring.rabbitmq.template.retry.multiplier=1.0

### 3.创建MQServer创建发送者

### 4.MQReceiver:创建消费者



## 2.为了让guest用户能远程连接，修改rabbitmq的配置

简约[的RabbitMQ配置文件，](https://www.rabbitmq.com/configure.html) 它允许远程连接客人看起来像这样：

```ini
＃ 危险区！
## 
允许默认用户的远程连接
＃＃
非常不鼓励＃，因为它会大大降低系统的安全性。删除用户＃，然后使用生成的安全凭据创建一个新
用户。loopback_users = none
```

或者，在[经典的配置文件格式](https://www.rabbitmq.com/configure.html#erlang-term-config-file)（rabbitmq.config）中：

```erlang
％％ 危险区！
%% 
%%强烈建议不要为默认用户提供远程连接，
因为它会大大降低系统的安全性。请删除用户
%%，然后使用生成的安全凭据创建一个新用户。
[{rabbit，[{loopback_users，[]}]}]。
```

重新启动

<http://www.rabbitmq.com/access-control.html

## **4这种交换机Exchange**

**FanoutExchange**：将将消息绑定队列,无routingkey的概念

**HeadersExchange**：通过添加key-value配置

**DirectExchange**：按照routingkey分发指定队列

**TopicExchange**：多关键字配置 （key可以携带通配符)

