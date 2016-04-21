---
title: Json是什么？
date: 2016-04-05 19:24:40
tags: Json
---


![json](http://7xsgf8.com1.z0.glb.clouddn.com/image/json.png)

    JSON, JavaScript Object Notation, 它是一种轻量级的数据交换格式，非常适合于服务器与JavaScript的交互。


21世纪初，Douglas Crockford寻找一种简便的数据交换格式，能够在服务器之间交换数据。当时通用的数据交换语言是XML，但是Douglas Crockford觉得XML的生成和解析都太麻烦，所以他提出了一种简化格式，也就是JSON。

JSON, JavaScript Object Notation, 它是一种轻量级的数据交换格式，非常适合于服务器与JavaScript的交互。对消息通信而言，JSON的优势是比XML更简单，因而可以大大提高开发效率，代码也更为简单。

<!--more-->

## 基本语法
JSON的语法格式非常简单，可以简单的概括如下：

- 并列的数据之间用	","
- 映射用				":"
- 并列数据的集合用	"[]"
- 映射的集合用		"{}"

例如我们可以用如下的JSON文件描述一张图的点集和边集：

	{
		"nodes": [
			{ "name": "NodeName"},
			{ "name": "NodeName"}
		],

		"links": [
			{
				"source": INDEX,
				"target": INDEX,
				"value": WEIGHT
			},
			{
				"source": INDEX，
				"target": INDEX，
				"value": WEIGHT
			}
		]
	}

解析该文件我们可以发现，这个JSON文件包含了两个数组，分别是 nodes 和 links 。而每个数组中又包含了不同的映射对。到此，JSON的语法基本就结束了。下面我们讨论JSON在Java中的应用。

Java对象/JSON之间的映射非常简单，只有两种数据结构：

1. Map  用 { } 表示
2. Array 用 [ ] 表示

### Map

简单的说就是Java中的Map，名称-值对的形式给出，名称和值之间用“：”隔开，两个Map之间用“，”隔开，一般表示形式如下：

	{'key1':'value1','key2':'value2'}

### Array

就是普通意义上的数组，一般形式如下：

	['arr1','arr2','arr3'];

### 要点：

1. JSON的数据结构只有两种；
2. 可以嵌套表示，比如Array中可以嵌套Object等；
3. 记住：Object是以｛｝表示，Array是以[  ]表示；

## Java with JSON

### JSR 353
JSR 353为处理JSON提供了官方的解决方案，并作为JEE 7 API的一部分被实现。因此，使用在Java中使用JSON的最合适的方法是使用JSR 353.其在包`javax.json`中，[点击查看API](http://docs.oracle.com/javaee/7/api/javax/json/package-summary.html)。这方面的内容请参考：

1. [Creating JSON Data Using the Java JSON API (JSR 353)](http://www.javabeat.net/java-json-api-jsr-353/)
2. [Java JSON Tutorial](http://www.java2s.com/Tutorials/Java/JSON/0100__JSON_Java.htm)
2. [JSON Processing in Java EE 7 - Using Object Model API](http://www.javabeat.net/2013/10/json-processing-javaee-7-object-model-api/)
3. [JSON Processing in Java EE 7 - Using Straeming API](http://www.javabeat.net/2013/10/json-processing-javaee-7-streaming-api/)

### Others
另外还有有许多的Java库可以用来处理（解析或生成）JSON。Java中最基本的结构是对象，这些库将Java对象映射为JSON，并提供用于序列化和反序列化JSON到对象的方法。Jackson和Gson是建立JSON\对象映射的两个最流行的库。实际开发时可以选择一个你喜欢的，下面将介绍这两个库的具体使用方法。

GSON的项目主页为：
> https://code.google.com/p/google-gson/

在POM中加入GSON的相关依赖

	<dependency>
	    <groupId>com.google.code.gson</groupId>
	    <artifactId>gson</artifactId>
	    <version>2.3</version>
	</dependency>

Jaackson的项目主页为：
> https://github.com/FasterXML/jackson

在POM中加入Jackson的相关依赖

	<dependency>
        <groupId>org.codehaus.jackson</groupId>
        <artifactId>jackson-mapper-asl</artifactId>
        <version>1.9.12</version>
    </dependency>

<del>
JSON在Java上的实现最常用的为Json lib， 其最新版即相关的依赖包的下载地址如下：

	http://download.csdn.net/detail/dqsweet/5246564
</del>

### POJO

定义一个POJO类用来序列化和反序列化JSON。

	public class MyPojo {
	    private String thing1;
	    private String thing2;

	    public MyPojo(){
	        System.out.println("*** Constructor MyPojo() called");
	    }

	    public String getThing1() {
	        return thing1;
	    }

	    public void setThing1(String thing1) {
	        this.thing1 = thing1;
	    }

	    public String getThing2() {
	        return thing2;
	    }

	    public void setThing2(String thing2) {
	        this.thing2 = thing2;
	    }

	    @Override
	    public boolean equals(Object obj) {
	        if(this == obj) return true;
	        if(obj==null || getClass() != obj.getClass()) return false;

	        MyPojo myPojo = (MyPojo) obj;

	        if(thing1 !=null ? !thing1.equals(myPojo.thing1) : myPojo.thing1 !=null)
	            return false;
	        if(thing2 !=null ? !thing2.equals(myPojo.thing2) : myPojo.thing2 !=null)
	            return false;

	        return true;
	    }

	    @Override
	    public int hashCode() {
	        int result = thing1 != null ? thing1.hashCode() : 0;
	        result = 31 * result + (thing2 != null ? thing2.hashCode() : 0);
	        return result;
	    }
	}

### GSON Demo

    MyPojo pojo = new MyPojo();
    pojo.setThing1("Hello");
    pojo.setThing2("Gson");

    Gson gson = new Gson();
    String json = gson.toJson(pojo);//序列化pojo为JSON对象
    System.out.println(json); //{"thing1":"Hello","thing2":"Gson"}

    System.out.println("\nNow create object and populate it with the JSON string.");
    MyPojo pojo2 = gson.fromJson(json, MyPojo.class);
    System.out.println("Serialized and deserialized object are equal: " + pojo.equals(pojo2));

### Jackson Demo

    MyPojo pojo = new MyPojo();
    pojo.setThing1("Hello");
    pojo.setThing2("Jackson");

    ObjectMapper mapper = new ObjectMapper();
    String json = mapper.writeValueAsString(pojo);
    System.out.println(json);

    System.out.println("\nNow create object and populate it with the JSON string.");
    MyPojo pojo2 = mapper.readValue(json, MyPojo.class);
    System.out.println("Serialized and deserialized object are equal: " + pojo.equals(pojo2));


更多有关两个库的高级实用功能，请参考相关的官方文档。

> Modified at 11/3/2014, via Weiwei SUN


#### JSON Lib
<del>

利用JSON lib可以构建和解析json文件。具体的使用方法可以参考其官方api，这里以构建JSON对象为例，介绍JSON lib的基本使用方法：

	http://www.json.org/java/

创建JSON对象

	JSONObject json = new JSONObject();	//创建空白

或

	JSONObject json = JSONObject.fromObject(object);//从已有字符串构建

其输出为：

	{
		//...
	}

创建JSON数组

	JSONArray array = new JSONArray();

其输出为：

	[
		//...
	]

一个JSON数组中实际是包含了若干的JSON对象，而一个JSON对象其实包含了若干的JSON键值对。

例如：

	JSONObject json = new JSONObject();//the whole document
	JSONArray array = new JSONArray();//a json object array
	JSONObject element = new JSONObject();
	element.put("name","Allen")
	element.put("school","seu");
	array.add(element);
	json.put("student", array);

输出结果为

	{
		"student": [
			{
				"name": "Allen",
				"school": "seu"
			}
		]
	}
</del>

### References

1. http://www.ibm.com/developerworks/cn/web/wa-lo-json/
2. http://www.ruanyifeng.com/blog/2009/05/data_types_and_json.html
3. http://www.json.org/java/
4. http://blog.csdn.net/xiazdong/article/details/7059573
5. Client-Server Web Apps with JavaScript and Java, 2014
