---
layout: post
title:  "[Java web develop] 1. 서블릿 프로그래밍"
date:   2017-10-4 20:00:13 +0800
categories: backend
tags: java
comments: 1
---
**자바 웹 개발 워크북을 따라해보는 예제 코드**
---

<br>

### 서블릿 테스트와 배치 테스트

<br>

#### src/projects01.servelets/HelloWorld.java 구현

> src/projects01.servelets 패키지 추가

javax.servlet.Servlet 인터페이스를 구현한 파일 생성

{% highlight python linenos %}
package project01.servlets;

import java.io.IOException;
import javax.servlet.Servlet;
import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

public class HelloWorld implements Servlet{
    ServletConfig config;
    
    @Override
    public void init(ServletConfig config) throws ServletException {
        System.out.println("init() 호출");
        this.config = config;
    }
    
    @Override
    public void destroy() {
        System.out.println("destroy() 호출");
    }
    
    @Override
    public void service(ServletRequest request, ServletResponse response) 
    throws ServletException, IOException {
        System.out.println("destroy() 호출");
    }
    
    @Override
    public ServletConfig getServletConfig() {
        System.out.println("getServletConfig() 호출");
        return this.config;
    }
    
    @Override
    public String getServletInfo() {
        System.out.println("getServiceInfo() 호출");
        return "version=1.0;author=jkkim;copyright=jkkim 2017";
    }
}
{% endhighlight %}

<br>

서블릿 인터페이스는 init, service, destory, getServeltInfo, getServletConfig 다섯가지 추상메소드를 구현해야 한다. 이중
init, service, destory가 생명주기 메소드 이다.

<br>

### 배치정보 작성

<br>

#### src/WebContent/WEB-INF/web.xml

{% highlight python linenos %}
<? xml version="1.0" encoding="UTF-8"?>
< web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
  < display-name>javaweb</ display-name>
  
  <!-- 서블릿 선언 -->
  <servlet>
    <servlet-name>Hello</servlet-name>
    <servlet-class>project01.servlets.HelloWorld</servlet-class>
  </servlet>
  <!-- 서블릿과 URL 연결 -->
  <servlet-mapping>
    <servlet-name>Hello</servlet-name>
    <url-pattern>/Hello</url-pattern>
  </servlet-mapping>
  
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
< /web-app>
{% endhighlight %}

몇번의 에러를 거치고나서...

<br>
init() 호출
destroy() 호출

<br>

#### 이클립스를 이용한 자동배치

> servers view -> open -> modules 웹어플리케이션 등록

C:\javaDB\javaWorkspace\.metadata\.plugins\org.eclipse.wst.server.core\tmp0\wtpwebapps\javaweb 에 임시폴더로 실제 톰캣 서버가 설치된 폴더가 아니라 WTP 관리하는 임시폴더에 빌드한 자바파과 메타파일 등이 저장된다.

>  javaweb 오른쪽클릭 -> export -> war file을 통해 war파일 생성 -> apach tomcat 설치 폴더 -> webapps에 war파일 복사

> cmd -> C:\javaDB\server\apache-tomcat-7.0.81\bin\startup.bat 실행

> C:\javaDB\server\apache-tomcat-7.0.81\webapps 에 war파일 바탕의 javaweb 폴더생성 확인

톰캣 배치 사용 이미지 

톰캣 정상 작동 이미지 

{% highlight python linenos %}

{% endhighlight %}

<br>


#### GenericServlet 사용한 간단한 계산기

제네릭서블릿이 없던 시절엔 init부터 destroy, getserveleinfo, getserveletconfig 까지 모두 오버라이드 해주어야 했다. 하지만 실 필요한 서비스만 이용하는 경우에 사용 할 경우에는 genericservlet 만으로 프로그램 작성이 가능합니다.

> project explorer -> servers -> tomcat -> server.xml -> URIEncoding="UTF-8"

{% highlight python linenos %}
package project01.servlets;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;
import java.util.Map;

import javax.servlet.GenericServlet;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebServlet;

@WebServlet("/calc")
public class CaculartorServelet extends GenericServlet {
    @Override
    public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
        int a = Integer.parseInt(request.getParameter("a"));
        int b = Integer.parseInt(request.getParameter("b"));
        String schem = request.getScheme();
        String proto = request.getProtocol();
        Enumeration<String> names = request.getParameterNames();
        Map<String, String[]> ttt = request.getParameterMap();
        String[] aaa = request.getParameterValues("a");
        
        response.setContentType("text/plain");
        response.setCharacterEncoding("UTF-8");
        PrintWriter writer = response.getWriter();
        
        writer.println("a + b = " + (a+b));
        writer.println("a * b = " + (a*b));
        writer.println("a - b = " + (a-b));
        writer.println(schem);
        writer.println(proto);
        writer.println(ttt);
        writer.println(aaa);
        
        for (String aa : aaa) {
            writer.println(aa);
        }

        while (names.hasMoreElements()) {
            writer.println(names.nextElement());
        }
    }
}

{% endhighlight %}

<br>

이걸로 서블릿 기초는 닦았습니다(?)
