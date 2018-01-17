# 整改
> 最近在对DzFilter支持分布式功能上进行整改。。。。

# DzFilter
使用DFA算法实现的敏感词过滤。主要用于实现数据文本的内容安全,反垃圾,智能鉴黄,敏感词过滤,不良信息检测，携带文本的关键词获取。
+ 过滤SQL脚本
+ 过滤中文字符
+ 过滤英文字符
+ 过滤script标签
+ 过滤html标签
+ 过滤数字
+ 过滤字母
+ 过滤汉字
+ 自定义过滤，可由后台自动删除添加。提供完善的API。


# 如何使用
## 导入以下仓库
```
<repositories>
	<repository>
	    <id>jitpack.io</id>
	    <url>https://jitpack.io</url>
	</repository>
</repositories>
```
## 添加Dependency
```
<dependency>
    <groupId>com.github.fanhua1994</groupId>
    <artifactId>DzFilter</artifactId>
    <version>1.0.6</version>
</dependency>
```

## TextUtils使用请看源码

## 过滤日志
> 过滤库仅仅加载一次，会全部加载到内存，所以要注意不能大于5000个。
```
加载时间 : 229233221ns
加载时间 : 229ms
我是***，我为自己代言
```

## 配置数据库
> 如果```DzFilter```您需要使用在分布式的系统或同时在多个程序中使用```DzFilter```并且需要保持一致性，那么我们建议您使用mysql数据库，如果是单击应用我们建议您使用sqlite数据库。数据库文件在项目目录下的database下。放到对应目录  配置好就行了。如果想使用mysql的话，请自行导入mysql驱动，并将database目录下的java_filter.sql导入mysql然后在dzfilter_config.properties配置 用户名密码等信息即可。
```
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.45</version>
</dependency>
```
Mysql配置文件如下
```
dzfilter.db.driver=org.sqlite.JDBC
dzfilter.db.dburl=jdbc:mysql://120.77.245.101:3306/filter_word?useUnicode\=true&characterEncoding\=utf-8&autoReconnect\=true
dzfilter.db.is_mysql=true
#if database is mysql please input user and pass
dzfilter.db.dbuser=root
dzfilter.db.dbpass=*****

# 如果不是集群 请忽略以下配置
dzfilter.cluster.open=true
dzfilter.cluster.activemq=tcp://192.168.0.106:61616
dzfilter.cluster.host=192.168.0.105
dzfilter.cluster.project_name=dzfilter
dzfilter.cluster.username=admin
dzfilter.cluster.password=admin
```
SQLite配置如下
```
dzfilter.db.driver=org.sqlite.JDBC
dzfilter.db.dburl=jdbc:sqlite:/home/fanhua/database/data_filter20171211.db
dzfilter.db.is_mysql=false
#if database is mysql please input user and pass
dzfilter.db.dbuser=root
dzfilter.db.dbpass=***

# 目前在SQLite数据库上暂时不支持集群模式
dzfilter.cluster.open=false
dzfilter.cluster.activemq=tcp://192.168.0.106:61616
dzfilter.cluster.host=192.168.0.105
dzfilter.cluster.project_name=dzfilter
dzfilter.cluster.username=admin
dzfilter.cluster.password=admin
```

## 如何自定义过滤服务

### 添加自定义关键词过滤
```
Boolean res = TextUtils.addFilter(String keywords);
```
### 删除自定义关键词过滤
```
boolean res = TextUtils.delFilter(String keywords);
```
### 分页获取添加的关键词
```
List<Map<String,Object>> rows = TextUtils.getDataPage(int page,int num);//第一个参数页数 1开始 第二个参数每页条数
List<Map<String,Object>> rows = TextUtils.getDataOffset(int offset,int limit);第一个参数偏移量 0开始 第二个参数每页条数
```
### 自定义关键词总数
```
int total = TextUtils.getDataTotal();
```


## 关键词替换方案已找到
以下是与HanLp对比的结果
```
 String key = "威远县隶属四川省内江市，地处内江市西北部，位于四川盆地中南部，地跨北纬29°22′～29°47′，东经104°16′～104°53′之间。东邻内江市市中区，南连自贡市大安区和贡井区，西界自贡市荣县，北衔资中县，西北与眉山市仁寿县、乐山市井研县接壤。";
 List<String> data = HanLP.extractKeyword(key, 10);
 System.out.println("HanKp:" + data.toString());
 try {
	List<String> data2 = KeywordUtils.getKeyWords(key, 10);
	System.out.println("IK:" + data2.toString());
} catch (IOException e) {
	e.printStackTrace();
}
```
输出如下：我们搜索的是威远县  HanLp并没有显示相关内容
```
HanKp:[内江市, 自贡市, 眉山市, 西北, 资中县, 荣县, 四川, 中南部, 之间, 南连]
DzFilter:[内江市, 自贡市, 大安区, 市中区, 四川盆地, 威远县, 资中县, 荣县, 四川省, 中南部]
```

