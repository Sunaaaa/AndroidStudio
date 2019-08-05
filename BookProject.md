Book Project

1. MySQL Database Setting (도서정보)

   - 제공된 Script File을 이용해서 Database 세팅

   - Database : 데이터의 논리적인 집합체 

     - DBMS (Database Management System)

       : 데이터 베이스를 관리, 사용하기 위한 Software의 집합

       => Oracle, DB2, informix, sybase, MySQL, ...

       

     - 초창기

       - 계층형 데이터 베이스 : 비슷한 분류 끼리 계층형으로 관리 

         => 데이터의 연관성을 표현, 규정하고 사용하는데 적절하지 않다.

       - 네트워크 데이터 베이스 

         => 이론적인 측면은 좋았지만, 실제 사용되는 경우는 거의 없었다.

         

     - 수학자가 논문을 하나 발표 about 관계형 데이터 ( : 데이터를 테이블 형태로 저장 )

       - 관계해석, 관계 대수

       - 데이터가 정형화 

       - IBM이 해당 논문을 근간으로 실제 DBMS을 구축 ===> DB2

       - 이를 시작으로 모든 DBMS는 관계형 DBMS로 전환

         

     - 1990 년대 후반까지 잘 사용하다가 객체지향의 패러다임이 시작

       - DB 분야에서는 프로그램 언어와는 다르게 객체 지향의 중요한 특성만 받아들여서 객체-관계형 데이터 베이스로 발전을 시켜요.

       - 빅데이터 시대로 접어들고 있어요 (3V)

         - 비정형 데이터를 저장, 관리할 때는 관계형 데이터 베이스가 효율적이지 않아요.

           그래서 NoSQL 계열의 DB가 사용되기 시작

           => Mongo DB

           

   - MySQL DBMS의 기동

     ```
     >> mysqld
     ```

     - C:\Users\student\Desktop\mysql-5.6.44-winx64\mysql-5.6.44-winx64\bin 에서 명령창을 띄어 명령어 mysqld(mysql 데몬)를 통해 myslq dbms를 기동한다. 

     - 3306 : 기본적으로 MySQL 서버에 할당되어 있는 port 번호 

       

   - MySQL Daemon Process가 실행이 되면 MySQL에 접속을 할 수 있어요.

     - 우리는 MySQL console에 root 사용자로 접속

       ```
       >> mysql -u root
       ```

     

   - MySQL에 접속한 후 사용자 계정 생성

     ```
     mysql > create user android identified by 'android';
     
     # JDBC로 네트워크 접속시 아래의 계정으로 들어간다.
     mysql > create user android@localhost identified by "android";
     ```

     

   - MySQL DBMS안에 여러 개의 Database를 생성하고 관리할 수 있어요.

     - 우리가 사용할 Database (데이터가 저장될 공간) 를 생성

       ```
       mysql > create database library;
       ```

     

   - 3번 단계에서 만든 새로운 사용자 (android)에게 Database (library) 에게 사용 권한을 부여 

     ```
     # android 계정에 library에 대한 모든 권한을 부여함 
     mysql > grant all privileges on library.* to android;
     
     # android@loaclhost 계정에 library에 대한 모든 권한을 부여함 
     mysql > grant all privileges on library.* to android@localhost;
     ```

     

   - 권한 flush

     - 권한이 잘 적용되도록 

       ```
       mysql > flush privileges;
       ```

     

   - MySQL console 종료

     ```
     mysql > exit
     ```

     

   - 사용할 데이터를 Database에 구축하는 작업을 진행 

     - 관계형 데이터 베이스 이기 때문에 테이블 형태로 진행

     - 제공된 Script File을 이용해서 library의 데이터 구축 진행 

       ```
       >> mysql -u android -p library < _BookTableDump.sql
       ```

     

   - 강사용 PC ip  : 70.12.115.80

     

   - Transaction 

     - 작업/일의 최소 단위 

     - 특정한 단위 작업(CRUD)의 묶음을 하나의 Transaction으로 설정할 수 있어요.

       - 예 > 은행의 이체업무는 Transaction으로 설정할 수 있어요.
       - << 이체 Transaction >>
         1. A라는 사람의 계좌에 돈이 충분한지 확인해요. selection 수행
         
         2. A라는 사람의 계좌에서 2000원을 빼요. update 수행
         
       3. B라는 사람의 계좌에서 잔액을 알아내요. selection 수행
         
       4. 알아낸 잔액 + 2000원 한 금액을 저장해요. update 수행
         
          
     
   - Why Transaction을 설정할까요?
     
     - DBMS에서 ACID라고 불리는 기능을 제공받기 위해서 설정
     
       - Atomicity (원자성) : Transaction으로 지정된 작업은 모두 성공하거나 하나도 하지 않은 상태로 관리되어야 한다.
     
       - Consistency (일치성) : Transaction이 종료된 후에 데이터의 일관성이 유지되어야 한다. 
     
       - Isolation (독립성) : Transaction이 걸려있는 Resource에 대해서 Transaction이 종료될 때까지 데이터에 대한 접근을 제한
     
         - 데이터를 read하는 것은 가능, CRUD는 불가 
     
           - A와 B의 Transaction이 끝날 때까지 A와 B의 Transaction을 막겠다.
     
         - Durability (영속성) : Transaction의 처리 결과는 2차 저장소에 안전하게 저장되는 것을 보장하는 기능 
     
     

   

2. Java Servlet 으로 Database Access Program 작성

   - 입력 :  책 제목의 Keyword

     출력 :  책 제목 리스트  (JSON)
     
     

   1. Eclipse의 설정

      - Workspace에 대한 text file Encoding => utf-8로 변경 

        ![123](C:\Users\student\Desktop\123.PNG)

      

   2. Tomcat Was를 이클립스와 연동

      - 이클립스에 Tomcat 서버를 연결한다. 

        ![1564638169409](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564638169409.png)

        

      - 클라이언트 (Web Server)가 Tomcat을 통해서 서버프로그램을 호출할 때 데이터를 전달할 수 있어요. 기본적으로 이 데이터 연결 통로가 ISO9958_1이라는 영문 Encoding으로 되어 있어요. 

        => 한글이 전달될 경우 문제가 발생 

        ==> UTF-8로 해당 데이터 연결 통로를 Encoding으로 변경 : 클라이언트가 보내는 한글 데이터를 제대로 받을 수 있다. 

        ![1564638419166](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564638419166.png)

      

   3. Dynamic Web Project 생성 

      - Project Name : BookSearchForAndroid

        ![1564639777141](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564639777141.png)

        

      - Context Root : bookSearch

        ![1564639826329](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564639826329.png)

        

      - MVC Pattern으로 파일을 생성

        - http://localhost/bookSearch/searchTitle?USER_KEYWORD=java

        - Servlet  == controller

          : Client의 입력을 받고 출력을 내보내는 작업  (처리는 다른 곳에서)

          : 로직을 처리할 때는 Service를 호출
      
     1. Servlet 생성
             - URL Mapping : /searchTitle

          ![1564640589460](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564640589460.png)

          ![1564640687359](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564640687359.png)

          

        - Service

          : 실제 Business Logic을 담당 (Transaction을 처리, )

          : 입력으로 프로그램의 로직을 처리한다. 

          ![1564641061777](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564641061777.png)

          

        - DAO (Data Access Object)

          : 데이터 베이스 관련 작업을 담당 

          ![1564641819856](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564641819856.png)

          

        - 데이터 전달에 대한 객체
        
          - DTO(Data Transfer Object), VO(Value Object), DO(Domain Object), Entity






## JavaServletJDBCTemplate

- BookDAO 

  - 기존의 DAO 코드는 데이터 베이스 Connection에 로드가 많이 걸리는 작업이고 부화가 많이 걸린다.  

    ```java
    	
    // DBTemplate가 모든 DB연결을 하게 되면 
    // -- 아래의 코드 모두 필요 없음
    
    Class.forName("com.mysql.jdbc.Driver");
    System.out.println("로딩성공");
    
    // Connection 단계 
    
    String id = "android";
    String pw = "android";
    String jdbcurl = "jdbc:mysql://localhost:3306/" + "library?characterEncoding=UTF8";
    
    /*
    
    상당히 로드가 많이 걸리는 작업 
    Connection pool 을 사용하는 코드로 재작성
    -> 미리 conn을 만들어 놓고 필요할 때 마다 가져다 쓴다. 
    Apache Tomcat DBCP라는 Connection pool 기능을 제공
    DBCP는 JNDI을 이용
    
    */
    
    Connection con = DriverManager.getConnection(jdbcurl, id, pw);
    System.out.println("연결 성공");
    ```

   

  ### Connection Pool

  - 데이터 베이스와 Application을 효율적으로 연결할 수 있다. 

  - 웹 Application의 요청을 대부분 DBMS로 연결되기 때문에 Connection Pool Library의 설정은 전체 Application의 성능과 안정성에 큰 영향을 미친다.

    - 장점

      : 코드가 간결하고 복잡하지 않다.

      : 데이터베이스와 Application의 일부분에서 발생하는 문제가 전체로 전파되지 않게 할 수 있다.

      : 일시적인 문제가 긴 시간 이어지지 않게 할 수 있다. 

    - 단점 

      : 적절하지 않은 값을 설정하여 Connection Pool 이 Application에서 병목 지점이 되는 경우가 발생할 수 있다. 

  - Connection Pool을 사용하는 코드로 재작성한다. 

    - 미리 일정수의 Connection을 만들어 놓고, 필요할 때마다 가져가 쓴다. 

    - Container 구동 시 일정 수의 Connection 객체를 생성

      - Client의 요청에 의해 Application이 DBMS 작업을 수행해야 하면 Connection Pool 에서 Connection 객체를 받아와 작업을 진행한다.
      - DBMS 작업이 끝나면 다시 Connection Pool에 반납한다. 

    - Apache Tomcat이 Commons DBCP라는 Connection Pool 기능을 제공한다. 

      - Commons DBCP 는 JNDL을 이용한다. 

      - context.xml 파일을 추가한다. 

        - 프로젝트명 -> WebContent -> META-INF -> context.xml

          ![1564931856744](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564931856744.png)

          - type : 웹에서 해당 Resource를 사용할 때, "javax.sql.DataSource"로 리턴된다.

          - name : JDBC 이름으로 변경이 가능하다.

          - driverClassName : JDBC 드라이버 ( ex > Oracle, MySQL

          - username : 접속 계정

          - password : 접속한 계정 비밀번호 

          - url : 

            

      -  DBTemplate Class를 구현 

        - Connection Pool을 만들고, con을 가져온다. 또 Transaction을 시작한다. 
      
        ![1565001397904](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1565001397904.png)
      
        
    
      - BookDAO ( Java 코드 )
    
        ```java
      // 2. Connection 단계 
        Connection con = common.DBTemplate.getConnection();
        System.out.println("연결 성공");
        ```
  
    
  
- DAO 코드 내 용어 
  
  - #### Connection
  
    - 네트워크 상의 연결 자체
      - 자바 프로그램과 데이터베이스 사이의 길
    - 보통 Connection 하나당 Transaction 하나를 관리한다. 
      - DriverManager.getConnection() : 실제 자바 프로그램과 데이터 베이스를 네트워크 상에서 연결
        - 연결에 성공하면 데이터 베이스와 연결 상태를 Connection 객체로 표현하여 반환한다.
      - close() 에 의해 Connection을 반납한다.
  
      
  
    - #### Statement
  
      - SQL 문을 보내고, 결과 값을 받는 역할을 수행하는 객체
  
      
    
    - #### DataSource
    
      - javax.sql.DataSource 인터페이스
      - ConnectioPool을 관리하는 객체로, 이 객체를 통해 Connection을 얻고 반납하는 등의 작업을 구현해야한다.



### Transaction 처리하기 

- Transaction : 작업의 최소 단위 

- Java에서 1개의 Serivice Method가 1개의 Transaction이다. 

  그래서 무조건 Connection에 Transaction을 잡는 것이 보통이다. 

  ```java
  public class BookDAO_OLD {
  
  	public List<String> selectTitle(String keyword) {
  		
  		List<String> list = new ArrayList<String>();
  		try {
  			
  			
  			Connection con = common.DBTemplate.getConnection();
  			System.out.println("연결 성공");
  			
  			con.setAutoCommit(false); // Transaction 시작
  			
  			String sql = "select btitle from books where btitle like ?";
  			PreparedStatement pstmt = con.prepareStatement(sql);
  			pstmt.setString(1, "%" + keyword + "%");
  
              ResultSet rs = pstmt.executeQuery();
  
              while (rs.next()) {
  				list.add(rs.getString("btitle"));
  				
  			}
  
              rs.close();
  			pstmt.close();
  
              // Transaction 처리 수행 : DB 처리에 대한 데이터를 적용
              con.commit();		 
  			
              // Transaction 처리 안함 : DB 처리에 대한 데이터를 적용하지 않음
  			//con.rollback();		 
              
  			con.close();
  		}catch (Exception e) {
  			// TODO: handle exception
  		}
  		return list;
  	}
  
  }
  ```

  

- 일반적으로 Service 에서 로직을 처리하고, DAO에서 DB 연결 및 검색을 수행한다. 

- 그러나, 하나의 Transaction 내부에는 다수의 DAO 처리 Method를 수행한다. 

  - 만약, 각각의 Method 에서 con을 열고, Transaction을 열고 닫으면, 각각의 Method가 단일 Transaction으로 잡히게 된다.

    => BAD!

- 하나의 con을 만들고, 이 con을 세개의 Method가 공용해서 사용한다. 또 세개의 작업이 마무리 되면, commit()/ rollback() 을 수행한다. 

- **DAO가 아닌 Service에서 Transaction을 처리한다.**

  ```java
  public List<BookDTO> getBooks(String keyword) {
  		// 로직처리 ( DB 처리를 포함해서 )
  		/*
  		 * Transaction : 작업의 최소 단위
  		 * 				=> Service Method 하나가 Transaction 
  		 * ====> 무조건 Connection에 Transaction을 잡는다. 
  		 * 		con.setAutoCommit(false); // Transaction 시작
  		 * 
  	  			con.rollback(); // Transaction 처리 안함 : DB 처리에 대한 데이터를 적용안함
  				con.commit();   // Transaction 처리 : DB 처리에 대한 데이터를 적용
  
   * 기존에 DAO에서 Con을 얻어 SQL 작성후 수행
   * ==> Service에서 Con을 얻어 DAO에 해당 con을 전달한다.
  		  
  	 		첫번 째 DB 처리 
  	 		dao.firstMethod(con);
  
  	 		두번 째 DB 처리 
  	 		dao.secondMethod(con);
  	 		
  	 		세번 째 DB 처리 
  	 		dao.thirdMethod(con);	
  		  
  		 */
  		// Transaction을 처리하기 위한 Con을 Service에서 작성한다. 
  		Connection con = null;
  		List<BookDTO> list = null;
  		
  		try {
  			con = common.DBTemplate.getConnection();
  			// getConnection() 하면서 conn.setAutoCommit(false);를 수행하기 때문에 Transaction 은 이미 시작
  			
  			System.out.println("연결 성공");
  			
  			BookDAO dao = new BookDAO(con);
  			list = dao.select(keyword);
  		
  			
  			// 얻어온 결과를 이용해서 Transaction의 Commit과 Rollback을 판단
  			if (list != null) {
  				// Transaction 이 정상적으로 처리 되었을 경우
  				con.commit();			
  			}else {
  				// Transaction 처리에 오류가 있는 경우
  				con.rollback();
  			}
  
  			
  		}catch (Exception e) {
  			// TODO: handle exception
  			System.out.println(e);
  		}finally {			
  			try {
  				con.close();
  			} catch (SQLException e) {
  				// TODO Auto-generated catch block
  				e.printStackTrace();
  			}
  		}
  				
  		return list;
  	}
  ```

  





### Jackson

#### Android에서 JSON 객체 -> String/ String Array

- Java Object를 JSON으로 변환 또는 JSON을 Java Object로 변환하는데 사용하는 Java Library

- 특징 

  1. Stream API : 스트림 형식으로 데이터를 분석하고 생성하기 때문에 성능이 좋다.

  2. Tree Model : XML의 DOM처럼 Node 형태로 데이터를 다룰 수 있기 때문에 유연성이 좋다. 

  3. Data Binding : POJO 기반의 자바 객체들을 JSON 으로 변환시킬 수 있다. 

     - POJO

        :  순수하게 Setter, Getter Method로 이뤄진 Value Object 성의 Bean

       ```java
       public class Student{
       	private String name;
           private String age;
           
           public void setName(String name){
               this.name = name;
           }
           public void setAge(String age){
               this.age = age;
           }
           public void getName(String name){
               return this.name;
           }
           public void getAge(String age){
               return this.age;
           }
       }
       ```

        

- 3가지 라이브러리 선택 -> 우클릭 -> Add as Library ( : Library로 포함)

  - jackson-annotations-2.8.1.jar

  - jackson-core-2.8.1.jar

  - jackson-databind-2.8.1.jar



![1564712067713](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564712067713.png)



![1564712091607](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1564712091607.png)



- Jackson Library를 이용하여 JSON 객체 생성 (Java Object --> JSON)

  ```java
  package com.test.controller;
  
  import java.io.IOException;
  import java.io.PrintWriter;
  import java.util.List;
  
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  
  import com.fasterxml.jackson.databind.ObjectMapper;
  import com.test.service.BookService;
  
  /**
   * Servlet implementation class BookSearchTitleServlet
   */
  @WebServlet("/searchTitle")
  public class BookSearchTitleServlet extends HttpServlet {
  	private static final long serialVersionUID = 1L;
         
      /**
       * @see HttpServlet#HttpServlet()
       */
      public BookSearchTitleServlet() {
          super();
          // TODO Auto-generated constructor stub
      }
  
  	/**
  	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
  	 */
  	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		// TODO Auto-generated method stub
  
  		// 입력 받고
  		String keyword = request.getParameter("USER_KEYWORD");
  		
  		// 로직처리 => Service에게 위임
  		// Service를 생성해서 Service에게 일을 시킨후 경과를 받아와요!
  		// service : 일을 할 수 있는 객체 
  		BookService service = new BookService();
  		List<String> list = service.getBooksTitle(keyword);
  		
  		
  		// 출력처리  ( : 결과를 JSON 형태 )
  		response.setContentType("text/plain; charset=UTF8");
  		PrintWriter out = response.getWriter();
  		
          // Jackson Library 
  		ObjectMapper mapper = new ObjectMapper();
          
          // 객체를 JSON 타입의 String으로 변환
  		String json = mapper.writeValueAsString(list);
  		out.print(json);
  
          // 객체를 JSON 타입의 String으로 변환
  		// String json2 = mapper.writeWithDefaultPrettyPrinter().WriteValueAsString(list);
  		// out.print(json2);
  
  		out.flush();
  		out.close();
  	}
  
  	/**
  	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
  	 */
  	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		// TODO Auto-generated method stub
  		doGet(request, response);
  	}
  
  }
  
  ```

- Jackson Library를 이용하여 Java Object로 변환 (JSON --> Java Object)

  ```java
  public class JacksonExam{
      public static void main (String[] args){
          ObjectMapper mapper = new ObjectMapper();
          try{
              // JSON 타입의 파일을 객체로 변환
              String[] str = mapper.readValue
          }
      }
  }
  ```

  ```java
  public class BookSearchActivity extends AppCompatActivity {
      class SearchTitleRunnable implements Runnable{
  
          private String keyword;
          private Handler handler;
  
          public SearchTitleRunnable(Handler handler, String keyword) {
              this.handler = handler;
              this.keyword = keyword;
          }
  
          @Override
          public void run() {
              String url = "http://[나의 IP 주소]:80/bookSearch/searchTitle?USER_KEYWORD=" + keyword;
  
              try{
                  URL urlObject = new URL(url);
                  HttpURLConnection con = (HttpURLConnection)urlObject.openConnection();
  
                  BufferedReader br = new BufferedReader(new InputStreamReader(con.getInputStream()));
                  String input = "";
                  StringBuffer sb = new StringBuffer();
  
                  while ((input=br.readLine())!= null){
                      sb.append(input);
                  }
                  Log.i("DATA", sb.toString());
                  
                  // Jackson Library를 이용하여 JSON을 원래 형태 (String[])로 변환
                  ObjectMapper mapper = new ObjectMapper();
  
                  // sb를 읽어서 Stringp[] 의 형태로 변형되어 resultArr로 떨어짐
                  String[] resultArr = mapper.readValue(sb.toString(), String[].class);
  
                  Bundle bundle = new Bundle();
                  bundle.putStringArray("BOOK_ARRAY", resultArr);
  
                  Message msg = new Message();
                  msg.setData(bundle);
  
                  handler.sendMessage(msg);
  
              } catch (Exception e){
                  Log.i("DATA_ERROR", e.toString());
              }
  
          }
      }
   ....
  }
  
  ```
  
  