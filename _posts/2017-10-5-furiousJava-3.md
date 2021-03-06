---
layout: post
title:  "[Java web] 3. MVC 아키텍쳐"
date:   2017-10-5 22:00:13 +0800
categories: backend
tags:  javaWeb
comments: 1
img: django-4-1.png
---

**MVC아키텍쳐의 전반적인 내용 - servlet에서 순차적으로 mvc 패턴을 적용하며 dao 전까지 내용 **

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

#### javaweb/src/project02/servlets/MemberListServlet.java

* 뷰를 jsp가 하게 되면서 서블릿에서 뷰 관련 코드를 제거한다.

{% highlight python linenos %}
package project02.servlets;

package project02.servlets;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

import java.util.ArrayList;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import project02.vo.*;

@WebServlet("/member/list")
public class MemberListServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;
    
    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response) 
    throws IOException, ServletException {
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
            
            ArrayList<Member> members = new ArrayList<Member>();
            
            stmt = con.createStatement();
            rs = stmt.executeQuery("select employee_id, first_name, last_name, email, hire_date from employees");
            
            response.setContentType("text/html; charset=UTF-8");
            
    
            while(rs.next()) {
                members.add(new Member()
                        .setEmployeeId(rs.getInt("employee_id"))
                        .setFirstName(rs.getString("first_name"))
                        .setLastName(rs.getString("last_name"))
                        .setEmail(rs.getString("email"))
                        .setHireDate(rs.getString("hire_date"))
                    );
                }
            
                request.setAttribute("members", members);
                RequestDispatcher rd = request.getRequestDispatcher("/member/MemberList.jsp");
                rd.include(request, response);
        }
        catch(Exception e) {
            throw new ServletException(e);
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

### ServletContext를 이용한 공유 자원 세팅 서블릿 

#### javaweb/src/project02/servlets/AppInitServlet.java

*  ServletContext 범위 안에 init 함수를 이용해 db접속 코드를 공유

세팅 후에 javaweb/src/project02/servlets/MemberListServlet.java에 db 접속 관련 코드 모두 삭제

{% highlight python linenos %}

package project02.servlets;

import java.sql.Connection;
import java.sql.DriverManager;

import javax.servlet.ServletConfig;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;


public class AppInitServlet extends HttpServlet {
    
    @Override 
    public void init(ServletConfig config) throws ServletException {
        System.out.println("AppInitiation start");
        super.init(config);
        
        try {
            ServletContext sc = this.getServletContext();
            Class.forName(sc.getInitParameter("driver"));
            Connection conn = DriverManager.getConnection(
                    sc.getInitParameter("oracleUrl"), "hr", "hr"
                    );
            sc.setAttribute("conn", conn);
        } catch (Exception e) {
            throw new ServletException(e);
        }
    }
    
    @Override
    public void destroy() {
        System.out.println("AppInitiation end");
        super.destroy();
        Connection conn = (Connection)this.getServletContext().getAttribute("conn");
        
        try {
            if (conn != null && conn.isClosed() == false) {
                    conn.close();
            }
        } catch (Exception e) {}
    }
}
{% endhighlight %}

<br>


### HttpSession을 이용해 로그인 처리 

세션을 이용해 로그인 및 로그아웃 처리를 한다.

#### javaweb/src/project02/servlets/LoginForm.java

여기서 AppInint에서 conn 처리를 맡겨놓고 다시 클로즈하는 바람에 sql 에러가 발생했다. 수정완료

{% highlight python linenos %}
package project02.servlets;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

import java.util.ArrayList;

import java.io.IOException;
import java.io.PrintWriter;


import javax.servlet.RequestDispatcher;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import project02.vo.*;

@WebServlet("/member/list")
public class MemberListServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;
    
    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response) 
    throws IOException, ServletException {
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;
        ServletContext sc = this.getServletContext();
        
        try {
            conn = (Connection) sc.getAttribute("conn");
        }
        catch(Exception e) {
            System.out.println("에러1");
            e.printStackTrace();
        }
        

        try {

            ArrayList<Member> members = new ArrayList<Member>();
            System.out.println("에러2");
            stmt = conn.createStatement();
            rs = stmt.executeQuery("select employee_id, first_name, last_name, email, hire_date from employees");
            System.out.println("에러3");
            response.setContentType("text/html; charset=UTF-8");
            System.out.println("에러4");
    
            while(rs.next()) {
                members.add(new Member()
                        .setEmployeeId(rs.getInt("employee_id"))
                        .setFirstName(rs.getString("first_name"))
                        .setLastName(rs.getString("last_name"))
                        .setEmail(rs.getString("email"))
                        .setHireDate(rs.getString("hire_date"))
                    );
                }
            
                request.setAttribute("members", members);
                RequestDispatcher rd = request.getRequestDispatcher("/member/MemberList.jsp");
                rd.include(request, response);
        }
        catch(Exception e) {
            throw new ServletException(e);
        }
        finally {
            try {if(stmt!=null) stmt.close();} catch(Exception e){};
            try {if(rs!=null) rs.close();} catch(Exception e) {};
        }   
    }
}
{% endhighlight %}

<br>


#### javaweb/WebContent/auth/LoginForm.jsp

{% highlight python linenos %}
<%@page import="project02.vo.Member" %>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<! DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
< jsp:useBean id="member" scope="session"  type="project02.vo.Member"/>
<%
if (member == null ) {
%>
< div style="background-color:black;color:#ffffff;height:20px;padding:5px;">
Header
< span style="float:right;">로그인 Required</ span>
</ div>
<% } else { %>
< div style="background-color:black;color:#ffffff;height:20px;padding:5px;">
Header
< span style="float:right;">세션 로그인 메일 : <%=member.getEmail() %></span>
</ div>
<% } %>
{% endhighlight %}

<br>

#### javaweb/WebContent/member/MemberList.jsp

{% highlight python linenos %}
<%@ page import="project02.vo.Member" %>
<%@ page import="java.util.ArrayList" %>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<! DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
< html>
< head>
< meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
< title>Insert title here</title>
</ head>
< body>
< jsp:include page="../Header.jsp" />
< h1>직원 목록</ h1>
< jsp:useBean id="members" scope="request" 
class="java.util.ArrayList" type="java.util.ArrayList< project02.vo.Member>"/>
<% 
for (Member member : members ) {
%>
    < a href='update?no=<%=member.getEmployeeId() %>'><%=member.getEmployeeId() %>
    <%=member.getFirstName() %>,
    <%=member.getLastName() %>
    </ a>
    <%=member.getEmail() %>
    <%=member.getHireDate() %>
    <br>
<%}%>


</ body>
</ html>
{% endhighlight %}

<br>