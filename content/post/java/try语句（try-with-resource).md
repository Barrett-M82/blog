---
title: Java带资源的try语句（try-with-resource）

tags: 
- java
categories:
- java
---

带资源的try语句（try-with-resource）的最简形式为：
```java
try(Resource res = xxx)//可指定多个资源
{
     work with res
}                                                                        
```
try块退出时，会自动调用res.close()方法，关闭资源。



PS:在coreJava第9版的第一卷的486页有解释。


补充一下,在没有这个语法之前,流操作一般是这样写的:
```java

InputStream is = null;
OutputStream os = null;
try {
	//...
} catch (IOException e) {
	//...
}finally{
	try {
		if(os!=null){
			os.close();
		}
		if(is!=null){
			is.close();
		}
	} catch (IOException e2) {
		//...
	}
}	
```
而现在你可以这样写:
```java

try(
	InputStream is = new FileInputStream("...");
	OutputStream os = new FileOutputStream("...");
){
	//...
}catch (IOException e) {
	//...
}
```
生活一下就美好了
