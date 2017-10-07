---
layout: post
title:  "[Java web] 3. MVC 아키텍쳐(진행중)"
date:   2017-10-5 22:00:13 +0800
categories: backend
tags:  java
comments: 1
img: django-4-1.png
---

**MVC아키텍쳐의 전반적인 내용**

---

### jsp를 이용한 계산기

##### javaweb/webContent/calc/Calculator.jsp?v1=30&op=-&v2=20

*  http://localhost:8888/javaweb/calc/Calculator.jsp?v1=30&op=-&v2=20 get을 이용했으므로 테스트시 url에서 확인가능

{% highlight python linenos %}
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%
String v1 = "";
String v2 = "";
String result = "";
String[] selected = {"", "", "", ""};

if (request.getParameter("v1") != null) {
    v1 = request.getParameter("v1");
    v2 = request.getParameter("v2");
    String op = request.getParameter("op");
    
    result = calculate(
                Integer.parseInt(v1),
                Integer.parseInt(v2),
                op
            );
}
%>
< DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
< html>
< head>
< meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
< title>계산기</ title>
</ head>
< body>
< h2>JSP로 구현한 계산기</ h2>
< form action="Calculator.jsp" target="get">
    < input type="text" name="v1" size="4" value=<%=v1 %>>
    < select name="op">
        < option value="+" <%=selected[0] %>>+</ option>
        < option value="-" <%=selected[1] %>>-< /option>
        < option value="*" <%=selected[2] %>>*< /option>
        < option value="/" <%=selected[3] %>>/< /option>
    </ select>
    < input type="text" name="v2" size="4" value=<%=v2 %>>
    < input type="submit" value="-">
    < input type="text" size="8" value=<%=result %>>
</ form>
</ body>
</ html>
<%!
private String calculate(int a, int b, String op) {
    int r = 0;
    
    if ("+".equals(op)) {
        r = a + b;
    } else if ("-".equals(op)) {
        r = a - b;
    } else if ("*".equals(op)) {
        r = a * b;
    } else if ("/".equals(op)) {
        r = a / b;
    }
    
    return Integer.toString(r);
}
%>
{% endhighlight %}


<br>


### 서블릿에서 뷰 분리하기

##### javaweb/src/project02/vo/Member.java

*  vo를 생성하여 MemberListServlet이 생성하고 MemberList.jsp가 사용 가능하게 한다. 

{% highlight python linenos %}
package project02.vo;

public class Member {
        private int employee_id;
        private String first_name;
        private String last_name;
        private String email;
        //private String [] job_id = ;
        private String job_id;
        private String hire_date;
    
    public int getId() {
        return employee_id;
    }
    
    public Member setEmployeeId(int employee_id) {
        this.employee_id = employee_id;
        return this;
    }
    
    public String getFirstName() {
        return this.first_name;
    }
    
    public Member setFirstName(String first_name) {
        this.first_name = first_name;
        return this;
    }
    
    public Member setLastName(String last_name) {
        this.last_name = last_name;
        return this;
    }
    
    public String getLastName() {
        return this.last_name;
    }
    
    public String getJobId() {
        return this.job_id;
    }
    
    public Member setJobId(String job_id) {
        this.job_id = job_id;
        return this;
    }

    public String getEmail() {
        return email;
    }
    
    public Member setEmail(String email) {
        this.email = email;
        return this;
    }
    
    public String getHireDate() {
        return hire_date;
    }
    
    public Member setHireDate(String hire_date) {
        this.hire_date = hire_date;
        return this;
    }
}

{% endhighlight %}

<br>

### 서블릿에 뷰 관련 코드 제거

##### javaweb/src/project02/servlets/MemberListServlet.java

*  멤버를 vo로 분리하여 MemberListServlet이 생성 MemberList.jsp가 사용 가능하게 한다. 

{% highlight python linenos %}
package project02.servlets;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

import java.io.IOException;
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
            out.println("<p><a href='add'>신규 회원</a></p>");
            
            while(rs.next()) {
                out.println("<a href='update?no="+rs.getInt("employee_id")+"'>"
                +rs.getInt("employee_id")+", "+rs.getString("first_name")+" "
                + rs.getString("last_name")+"</a><br>");
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

### 서블릿에서 뷰 분리하기

##### javaweb/src/project02/vo/Member.java

*  멤버를 vo로 분리하여 MemberListServlet이 생성 MemberList.jsp가 사용 가능하게 한다. 

{% highlight python linenos %}
{% endhighlight %}

