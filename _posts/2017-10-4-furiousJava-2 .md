---
layout: post
title:  "[Java web] 2. JDBC"
date:   2017-10-4 22:00:13 +0800
categories: backend
tags:  javaWeb
comments: 1
img: django-4-1.png
---

**JDBC의 전반적인 내용**

---

#### 데이터베이스 다루기

##### project02.servlets.MemberListServlet.java

* 직원 정보 리스트

{% highlight python linenos %}
package project02.servlets;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

import java.io.IOException;
import java.io.PrintWriter;

import java.util.Enumeration;
import java.util.Map;

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

#### HttpServlet을 이용한 get, post 처리

##### project02.servlets.MemberAddServlet.java

* 직원정보 입력폼

폼은 일단 임시 형태로.... GenericServlet 을 상속한 HttpServlet 이 service를 호출해 요청에 따라 doXXX 형태로 제공된다.(오버라이드해야한다)

{% highlight python linenos %}
package project02.servlets;

import java.io.IOException;
import java.io.PrintWriter;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

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
        out.println("< body>< h1>회원 등록</ h1>");
        out.println("< form action='add' method='post'>");
        out.println("이름 : < input type='text' name='name'>< br>");
        out.println("이메일 : < input type='text' name='email'>< br>");
        out.println("암호 : < input type='text' name='password'>< br>");
        out.println("< input type='submit' value='추가'>< br>");
        out.println("< input type='reset' value='취소'>< br>");
        out.println("</ form>");
        out.println("</ body></ html>");
    }
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        Connection con = null;
        PreparedStatement stmt = null;
        String jdbcUrl = "jdbc:oracle:thin:@localhost:1521:myoracle";
        String userID = "hr";
        String userPass = "hr";
        
        try {
            con = DriverManager.getConnection(jdbcUrl, userID, userPass);
            System.out.println("connection success");
            stmt = con.prepareStatement("insert into employees(employee_id, "
                    + "first_name, last_name, email, hire_date, job_id)"
                    + " vlaues (?,?,?,?,TO_DATE(SYS_DATE,'yyyy/mm/dd hh24:mi:ss'),?)");
            stmt.setString(1, request.getParameter(""));
            stmt.setString(2, request.getParameter(""));
            stmt.setString(3, request.getParameter(""));
            stmt.setString(4, request.getParameter(""));
            stmt.setString(5, request.getParameter(""));
            stmt.executeUpdate();
            
            response.setContentType("text/html;charset=UTF-8");

        } catch (Exception e) {
            throw new ServletException(e);
        }
    }
}


{% endhighlight %}

<br>

### 필터 사용

setEncoding 을 매번 설정하지 말고 필터를 통해 서블릿 요청이 들어왔을때 필터에서 처리하도록 한다.

#### javaweb/WebContent/WEB-INF/web.xml 필터 관련 배치 정보 

{% highlight python linenos %}
  
  <!-- 필터 선언 -->
  <filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>project02.filters.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  
{% endhighlight %}

<br>

#### javaweb/project02.filters/CharacterEncodingFilter.java


{% highlight python linenos %}
  
package project02.filters;

import java.io.IOException;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

public class CharacterEncodingFilter implements Filter {
    FilterConfig config;
    
    @Override
    public void init(FilterConfig config) throws ServletException {
        this.config = config;
    }
    
    @Override
    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain nextFilter) throws IOException, ServletException {
        request.setCharacterEncoding(config.getInitParameter("encoding"));
        nextFilter.doFilter(request, response);
    }
    
    @Override
    public void destroy() {}
}

  
{% endhighlight %}

<br>

* 직원정보 등록 테스트

{% highlight python linenos %}
--------------임시
{% endhighlight %}

<br>


### 서블릿 초기화 매개변수
<br>
#### javaweb/WebContent/WEB-INF/web.xml

* 직원정보 입력폼

![img]({{baseurl}}/assets/res/backend/furious-2-1.png)

<br>

* 직원정보 입력 성공 

![img]({{baseurl}}/assets/res/backend/furious-2-2.png)

<br>

* db에서  입력 확인 

![img]({{baseurl}}/assets/res/backend/furious-2-3.png)

<br>

* 회원 상세 정보 출력하는 서블릿

{% highlight python linenos %}
--------------임시
{% endhighlight %}

<br>

{% highlight python linenos %}


{% endhighlight %}



