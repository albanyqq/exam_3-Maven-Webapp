# springMVC
基于MyEclipse的maven+springMVC项目创建及测试
一．准备工作：安装MyEclipse10（已经集成了maven工具）
二．项目建立步骤
1.File->New->Other出现如下对话框
 
选择Maven Project项目，Next
 
默认不变，Next
 
选择如上图，Next
 
Group Id和Artifact Id自定义，Package可写可不写，项目完成可再加。Finish完成。
项目目录如图：
 
2.完善项目
首先，完善目录，增加重要的source Folder，这个不是简单的Floder,这些文件夹是会参与编译的。增加src/main/java，src/test/resources，src/test/java目录。让目录变成标准的maven结构。右击项目->Bulid Path->New Source Folder,如下图：
 
Finish完成
3.配置Maven工程
  右击项目->Properties->Resource,让项目的字符集为UTF-8，如图
 
3.将工程变为Web工程
选择Dynamic Web Module,版本选择3.0，如图：
 
要配置web项目的发布目录，就是Deployment Assembly，如图：
 
test目录不要发布，所以删掉以上两项。并且增加src/main/webapp目录，和Maven Dependenices,完成之后如下图：
 
4.赋予工程的springmvc特性（添加配置文件）
项目的配置文件目录如下图：
 
(1).配置web.xml，使其具有springmvc特性，主要配置两处，一个是
ContextLoaderListener，一个是DispatcherServlet。代码如下：
web.xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
    http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
<listener><listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<servlet>
<servlet-name>exam</servlet-name><servlet-class>org.springframework.web.servlet.DispatcherServlet
</servlet-class>
</servlet>
<servlet-mapping>
<servlet-name>exam</servlet-name>
<url-pattern>/</url-pattern>
</servlet-mapping>
<welcome-file-list>
<welcome-file>index.jsp</welcome-file>
</welcome-file-list>
</web-app>
(2).配置ContextLoaderListener表示，该工程要以spring的方式启动。启动
时会默认在/WEB-INF目录下查找applicationContext.xml作为spring容
器的配置文件，这里可以初始化一些bean，如DataSource。我们这里什么
也不做。代码如下：
applicationContext.xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN//EN""http://www.springframework.org/dtd/spring-beans.dtd">
<beans>
</beans>
(3).配置DispatcherServlet表示，该工程将采用springmvc的方式。启动时也
会默认在/WEB-INF目录下查找XXX-servlet.xml作为配置文件，XXX就是
DispatcherServlet的名字，该文件中将配置两项重要的mvc特性：
HandlerMapping,负责为DispatcherServlet这个前端控制器的请求查找
Controller；
ViewResolver,负责为DispatcherServlet查找ModelAndView的视图解析器。
exam-servlet.xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- Bean头部 -->
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
    xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:context="http://www.springframework.org/schema/context"
    xmlns:util="http://www.springframework.org/schema/util"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd  
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd  
            http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd              
            http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-3.0.xsd">

<!-- 激活@Controller模式 -->
<mvc:annotation-driven />
<!-- 对包中的所有类进行扫描，以完成Bean创建和自动依赖注入的功能 需要更改 
-->
<context:component-scan base-package="cc.monggo.web.controller" />

<bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter" />

<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
<property name="prefix">
<value>/WEB-INF/jsp/</value>
</property>
<property name="suffix">
<value>.jsp</value>
</property>
</bean>
</beans>
5.让maven自动配置jar包(可以根据需要手动添加jar包)
pow.xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>exam</groupId>
<artifactId>exam_3</artifactId>
<packaging>war</packaging>
<version>0.0.1-SNAPSHOT</version>
<name>exam_3 Maven Webapp</name>
<url>http://maven.apache.org</url>
<dependencies>
<dependency>
<groupId>junit</groupId>
<artifactId>junit</artifactId>
<version>3.8.1</version>
<scope>test</scope>
</dependency>
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-web</artifactId>
<version>3.0.5.RELEASE</version>
</dependency>

<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-webmvc</artifactId>
<version>3.0.5.RELEASE</version>
</dependency>

<dependency>
<groupId>org.apache.geronimo.specs</groupId>
<artifactId>geronimo-servlet_2.5_spec</artifactId>
<version>1.2</version>
</dependency>

</dependencies>
<build>
<finalName>exam_3</finalName>
</build>
</project>

6.测试
文件目录如下图：
 
(1).先写Controller。编写两个类，LoginControler.java，LoginForm.java。
代码如下

LoginController.java
package cc.monggo.web.controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import cc.monggo.domain.LoginForm;

@Controller
public class LoginController {
    @RequestMapping(value="login")
    public ModelAndView login(HttpServletRequest request,HttpServletResponse response,LoginForm command ){
        String username = command.getUsername();
        ModelAndView mv = new ModelAndView("/index/index","command","LOGIN SUCCESS, " + username);
        return mv;
    }
}
LoginForm.java
package cc.monggo.domain;
    public class LoginForm {
    private String username;
    private String password;
    public String getUsername() {
        return username;
    }
    public void setUsername(String username) {
        this.username = username;
    }
    public String getPassword() {
        return password;
    }
    public void setPassword(String password) {
        this.password = password;
    }
}

(2).再增加一些jsp,首页的index.jsp,主要是做跳转，代码如下：

<%  request.getRequestDispatcher("/WEB-INF/jsp/login/login.jsp").forward(request,response);
 %>

(3).还有两个jsp，做些简单的功能，一个表单login.jsp,一个表单提交的返回
index.jsp，代码如下：

login.jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN""http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Insert title here</title>
</head>
<body>
<div>
<form action="login" methed="get">
<input type="text" name="username">
<input type="submit" value="SUBMIT">
</form>
</div>
</body>
</html>

index.jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
 pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN""http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Insert title here</title>
</head>
<body>
    ${command}
</body>
</html>

(4).运行：首先右击项目->Run As->maven install
      其次点击工作空间下面的Servers，启动Tomcat服务器，如下图：
 
最后右击项目->Run As->MyEclipse Server Application测试，结果如下图：
 
输入Fangjinsong点击SUBMIT，跳转如下界面：
 
