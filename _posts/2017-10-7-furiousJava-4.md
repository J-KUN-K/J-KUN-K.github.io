---
layout: post
title:  "[Java web] 4. MVC 아키텍쳐 - 2"
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


@WebServlet("/member/add")
public class MemberListServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;
    
    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response) 
    throws IOException, ServletException {      
        try {
           
            
        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
{% endhighlight %}



<br>

### 리스너 ServletContextListener와 객체 공유

서블릿 컨테이너는 웹 애플리케이션의 상태를 모니터링 할 수 있도록 웹 어플리케이션의 시작에서 종료까지 주요한 사건에 대해 알림 기능을 제공합니다. 이런 알림 기능을 이용하고 싶다면, 규칙에 따라 객체를 만들어 DD파일에 등록하면 됩니다. 이렇게 사건이 발생했을 때 알림을 받는 객체를 리스너라고 부릅니다.
현재 저의 프로그래밍 구조에서는 리스트 페이지 요청시마다 DAO를 호출하여 가비지가 생성되고 실행시간이 길어집니다. 이는 요청이 많아 진다면 문제가 될 수 있습니다. 이처럼 여러 서블릿이 공유하는 객체는 ServletContext에 저장하는것이 좋습니다.

#### 리스너 ServletContextListener 만들기

웹 어플리케이션이 시작되면 javax.sevlet - ServletContextListener.contextInitialized() 메서드 호출하므로 공유객체를 이 메서드에 작성

*  project02.javaweb.listeners.ContextLoaderListener.java -> 스프링에서도 같은 역할 가진 클래스 존재

{% highlight python linenos %}
package project02.listeners;

import java.sql.Connection;
import java.sql.DriverManager;

import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

import project02.dao.MemberDao;

@WebListener
public class ContextLoadListener implements ServletContextListener {
    Connection conn;
    
    @Override
    public void contextInitialized(ServletContextEvent event) {
        try {
            ServletContext sc = event.getServletContext();
            
            Class.forName("driver");
            conn = DriverManager.getConnection(
                    sc.getInitParameter("oracleUrl"), "hr", "hr");
            
            MemberDao memberDao = new MemberDao();
            memberDao.setConnection(conn);
        } catch(Throwable e) {
            e.printStackTrace();
        }
    }
    @Override
    public void contextDestroyed(ServletContextEvent event) {
        try {
            conn.close();
        } catch (Exception e) {}
    }
}
{% endhighlight %}


<br>


### 기존 서블릿 변경하기

- web.xml 에 AppInit 부분 서블릿 설정 삭제
- ListServlet conn불러오는 부분과 Dao 설정하는 부분 삭제

<br>


### DB커넥션 풀

DB 커넥션풀은 여러개의 DB 커넥션 객체를 생성하여 풀에 담아 놓고 필요할 떄 꺼내 쓰는 방식입니다. 즉, 자주 쓰는 객체를 미리 만들어두고 빌려 사용하고 반납하는 방식입니다. 지금까지는 하나의 DB커넥션 객체만 이용했지만 하나의 DAO에 여러 Statement가 접속해 하나라도 커넥션 객체를 통해 롤백을 한다면 그 커넥션을 다루는 다른 객체들도 영향을 받게 됩니다. 따라서 각 요청에 대해 개별 db 커넥션을 사용해야 합니다. 

#### DB 커넥션 풀 만들기

*  project02.javaweb.utils.DBConnectionPool.java

{% highlight python linenos %}
package project02.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.util.ArrayList;

public class DBConnectionPool {
    String url;
    String username;
    String password;
    ArrayList<Connection> connList = new ArrayList<Connection>();
    
    public DBConnectionPool(String driver, String url, String username, String password)
    throws Exception {
        this.url = url;
        this.url = username;
        this.url = password;
        
        Class.forName(driver);
    }
    
    public Connection getConnection() throws Exception {
        if (connList.size() > 0) {
            Connection conn = connList.get(0);
            if (conn.isValid(10)) {
                return conn;
            }
        }
        return DriverManager.getConnection(url, username, password);
    }
    
    public void returnConnection(Connection conn) throws Exception {
        connList.add(conn);
    }
    
    public void closeAll() {
        for(Connection conn : connList) {
            try {conn.close();} catch (Exception e) {}
        }
    }
}

{% endhighlight %}


<br>

*  project02.javaweb.listeners.ContextLoadListener.java

{% highlight python linenos %}
package project02.listeners;

import java.sql.Connection;
import java.sql.DriverManager;

import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

import project02.dao.MemberDao;
import project02.util.DBConnectionPool;

@WebListener
public class ContextLoadListener implements ServletContextListener {

    DBConnectionPool connPool;
    
    @Override
    public void contextInitialized(ServletContextEvent event) {
        try {
            ServletContext sc = event.getServletContext();
            

            connPool = new DBConnectionPool(
                    sc.getInitParameter("draiver"),
                    sc.getInitParameter("oracleUrl"),
                    "hr", "hr"
                    );
            
            MemberDao memberDao = new MemberDao();
            memberDao.setConnection(connPool);
            
        } catch(Throwable e) {
            e.printStackTrace();
        }
    }
    @Override
    public void contextDestroyed(ServletContextEvent event) {
        try {
            connPool.closeAll();
        } catch (Exception e) {}
    }
}
{% endhighlight %}


<br>

*  project02.javaweb.dao.MemberDao.java

{% highlight python linenos %}
package project02.listeners;

import java.sql.Connection;
import java.sql.DriverManager;

import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

import project02.dao.MemberDao;
import project02.util.DBConnectionPool;

@WebListener
public class ContextLoadListener implements ServletContextListener {

    DBConnectionPool connPool;
    
    @Override
    public void contextInitialized(ServletContextEvent event) {
        try {
            ServletContext sc = event.getServletContext();
            

            connPool = new DBConnectionPool(
                    sc.getInitParameter("draiver"),
                    sc.getInitParameter("oracleUrl"),
                    "hr", "hr"
                    );
            
            MemberDao memberDao = new MemberDao();
            memberDao.setConnection(connPool);
            
        } catch(Throwable e) {
            e.printStackTrace();
        }
    }
    @Override
    public void contextDestroyed(ServletContextEvent event) {
        try {
            connPool.closeAll();
        } catch (Exception e) {}
    }
}
{% endhighlight %}


<br>

DAO 추가 함수들을 아직 마무리하지 못했습니다. 또한 updateServlet.java jsp 페이지도 만들어야하고 add 기능도 고치치 않았네요....