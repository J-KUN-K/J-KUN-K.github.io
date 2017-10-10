---
layout: post
title:  "[Java web] 4. MVC 아키텍쳐 - 2(진행중)"
date:   2017-10-7 20:00:13 +0800
categories: backend
tags:  javaWeb
comments: 1
img: django-4-1.png
---

**servlet에서 순차적으로 mvc 패턴을 적용하며 dao 부터 db connection pool 까지 내용 **

---

### DAO 사용하기

#### DAO를 이용하게끔 LIST, ADD 수정 및 INSERT,DELETE, UPDATE 추가 & DAO 생성

코드진행중......

* DAO
{% highlight python linenos %}
package project02.dao;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

import project02.vo.Member;

public class MemberDao {
    Connection connection;
    
        public void setConnection(Connection connection) {
            this.connection = connection;
        }
        
        // 추가 예정 함수들
        /*
        public void insert(Member member) {
            
        }
        
        public void delete(int no) {
            
        }
        
        public Member selectOne(int no) throws Exception {
            
        }
        
        public int update(Member member) throws Exception {
            
        }
        
        public Member exist (String email, String password) 
        throws Exception {
            
        }
        */
        
        public List<Member> selectList() throws Exception {
            Statement stmt = null;
            ResultSet rs = null;
            
            try {
                stmt = connection.createStatement();
                rs = stmt.executeQuery("select employee_id, "
                        + "first_name, last_name, email, hire_date from employees");
                
                ArrayList<Member> members = new ArrayList<Member>();
                
                while(rs.next()) {
                    members.add(new Member()
                            .setEmployeeId(rs.getInt("employee_id"))
                            .setFirstName(rs.getString("first_name"))
                            .setLastName(rs.getString("last_name"))
                            .setEmail(rs.getString("email"))
                            .setHireDate(rs.getString("hire_date"))
                    );
                }
                return members;
            } catch (Exception e) {
                throw e;
            } finally {
                try {if(stmt!=null) stmt.close();} catch(Exception e){};
                try {if(rs!=null) rs.close();} catch(Exception e) {};
            }
        }
}

{% endhighlight %}

*  LIST
{% highlight python linenos %}
package project02.servlets;

import java.sql.Connection;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import project02.dao.MemberDao;


@WebServlet("/member/list")
public class MemberListServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;
    
    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response) 
    throws IOException, ServletException {      
        try {
            ServletContext sc = this.getServletContext();
            Connection conn = (Connection) sc.getAttribute("conn");
            
            MemberDao memberDao = new MemberDao();
            memberDao.setConnection(conn);
            
            request.setAttribute("members", memberDao.selectList());
            response.setContentType("text/html; charset=UTF-8");
            RequestDispatcher rd = request.
                getRequestDispatcher("/member/MemberList.jsp");
            rd.include(request, response);
            
        }
        catch(Exception e) {
            System.out.println("에러1");
            e.printStackTrace();
        }
    }
}
{% endhighlight %}

<br>

*  add 수정중
{% highlight python linenos %}
package project02.servlets;

import java.sql.Connection;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import project02.dao.MemberDao;


@WebServlet("/member/list")
public class MemberListServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;
    
    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response) 
    throws IOException, ServletException {      
        try {
            ServletContext sc = this.getServletContext();
            Connection conn = (Connection) sc.getAttribute("conn");
            
            MemberDao memberDao = new MemberDao();
            memberDao.setConnection(conn);
            
            request.setAttribute("members", memberDao.selectList());
            response.setContentType("text/html; charset=UTF-8");
            RequestDispatcher rd = request.
                getRequestDispatcher("/member/MemberList.jsp");
            rd.include(request, response);
            
        }
        catch(Exception e) {
            System.out.println("에러1");
            e.printStackTrace();
        }
    }
}
{% endhighlight %}



<br>

### 리스너 ServletContextListener 만들기

#### 

*  
{% highlight python linenos %}

{% endhighlight %}


<br>


### 기존 서블릿 변경하기

#### 

*  
{% highlight python linenos %}

{% endhighlight %}


<br>


### DB커넥션 풀

#### 

*  
{% highlight python linenos %}

{% endhighlight %}


<br>