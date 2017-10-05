---
layout: post
title:  "[Java web] 2. JDBC(진행중)"
date:   2017-10-4 22:00:13 +0800
categories: backend
tags:  java
comments: 1
img: django-4-1.png
---

**JDBC의 전반적인 내용**

---

##### 데이터베이스 다루기

###### project02.servlets.MemberListServlet.java

* 직원 정보 리스트

{% highlight python linenos %}
package project02.servlets;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

import java.io.PrintWriter;

import javax.servlet.GenericServlet;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebServlet;

@WebServlet("/member/list")
public class MemberListService extends GenericServlet {
    
    @Override
    public void service(ServletRequest request, ServletResponse response) {
        Connection con = null;
        Statement stmt = null;
        ResultSet rs = null;
        
        try {
            Class.forName("oracle.jdbc.driver.OracleDriver");
            System.out.println("drive load success");
        }
        catch(ClassNotFoundException e) {
            System.out.println("에러1");
            e.printStackTrace();
        }
        
        String jdbcUrl = "jdbc:oracle:thin:@localhost:1521:myoracle";
        String userID = "hr";
        String userPass = "hr";

        try {
            con = DriverManager.getConnection(jdbcUrl, userID, userPass);
            System.out.println("connection success");
            stmt = con.createStatement();
            rs = stmt.executeQuery("select employee_id, first_name, last_name from employees");
                       
            response.setCharacterEncoding("UTF-8");
            response.setContentType("text/html");
            PrintWriter out = response.getWriter(); 
            out.println("<h1>사원목록</h1>");
            while(rs.next()) {
                out.println(rs.getInt("employee_id")+", "+
                rs.getString("first_name")+" "+ rs.getString("last_name")+"<br>");
            }
        }
        catch(Exception e) {
            System.out.println("에러2");
            e.printStackTrace();
        }
        finally {
            try {if(con!=null) con.close();} catch(Exception e){e.printStackTrace();};
            try {if(stmt!=null) stmt.close();} catch(Exception e){e.printStackTrace();};
            try {if(rs!=null) rs.close();} catch(Exception e){e.printStackTrace();};
        }   
    }
}
{% endhighlight %}


<br>

#### HttpServlet을 이용한 get

##### project02.servlets.MemberAddServlet.java

* 직원정보 입력폼

폼은 일단 임시 형태로....

{% highlight python linenos %}
package project02.servlets;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;


@WebServlet("/member/add")
public class MemberAddServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        out.println("< html>< head>< title>test</ title></ head></ html>");
        out.println("< body>< h1>직원 등록</ h1>");
        out.println("이름 : < input type='text' name='name'>< br>");
        out.println("이메일 : < input type='text' name='email'>< br>");
        out.println("암호 : < input type='text' name='password'> < br>");
        out.println("< input type='submit' value='추가'>< r>");
        out.println("< input type='reset' value='취소'>< br>");
        out.println("</ form>");
        out.println("</ body></ html>");
    }
}

{% endhighlight %}

<br>

#### HttpServlet을 이용한 get

##### project02.servlets.MemberAddServlet.java

* 직원정보 입력폼

{% highlight python linenos %}


{% endhighlight %}

{% highlight python linenos %}


{% endhighlight %}

