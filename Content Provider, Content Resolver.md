2019년 8월 9일

# Content Provider, Content Resolver

- 우리의 안드로이드 앱은 sandbox model을 기반으로 동작

  - 일반적으로 하나의 앱에서 생성한 데이터는 해당 앱에 종속되고 다른 Application에서 사용할 수 없다.

    - 예> 

      우리가 Application을 제작하여 주소록에서 데이터를 가져와 이용할 수 없다. 



![1565346637980](https://user-images.githubusercontent.com/39547788/62773206-8d6fda00-badc-11e9-8419-776548a069b5.png)



- Content Provider : 데이터를 제공할 때 사용

  - Application 사이에서 Data를 공유하는 통로 역할 

  

- Content Resolver : 제공된 데이터를 사용할 때 사용 

  - Content Provider가 안드로이드 시스템의 각종 설정 값이나 DB에 접근하게 해주면, 쿼리문의 결과를 반환하는 역할

  - Content Provider의 주소 (Uri Authorities) 를 통해 해당 Content Provider의 Method를 공유한다.

    

- 사용자가 만드는 Application은 일반적으로 Content Provider를 만들지 않는다. 

  - Content Resolver를 이용하여 안드로이드가 기본적으로 가지고 있는 Content Provider를 사용한다.





- 안드로이드 앱은 데이터를 관리하는 방법이 몇가지 있다. 
  1. File 처리 - 관리가 어려움
  2. DataBase 처리 
     - 안드로이드 시스템 내부에 있는 SQLite라는 소형 데이터베이스를 이용해서 데이터 처리 
     - SQLite 이용 방법 
     - Content Provider를 통해서 데이터 베이스에 접근하는 Method를 외부에 제공하는 방법
     - 다른 Application이 가지고 있는 데이터 베이스에 접근하는 방법





### SQLite 이용 방법 

- 버튼 클릭 시, 데이터 베이스 (Member) 를 생성하고, 테이블을 생성한다. 

  - MySQLiteHelper.java 

    - SQLite 데이터 베이스를 사용하기 쉽도록 도와주는 Class

    - SQLiteOpenHelper Class를 직접 사용하지 않고, SQLiteOpenHelper Class를 상속받는 사용자 정의 클래스를 작성한 후, 해당 클래스의 객체를 이용한다.  

      ```java
      package com.example.androidsample;
      
      import android.content.Context;
      import android.database.DatabaseErrorHandler;
      import android.database.sqlite.SQLiteDatabase;
      import android.database.sqlite.SQLiteOpenHelper;
      import android.util.Log;
      
      public class MySQLiteHelper extends SQLiteOpenHelper {
      
          // 생성자도 다시 작성해야 한다.
          public MySQLiteHelper(Context context) {
              
              // 상위 클래스의 생성자 호출
              // 데이터 베이스 이름
              super(context, "Member.db", null, 1);
          }
      
          // 오버라이드 Method 구현
          @Override
          public void onCreate(SQLiteDatabase sqLiteDatabase) {
              
              // 초기 데이터 베이스 설정 코드가 들어가요.
              // 테이블 생성하고 초기 데이터 Insert하는 작업
              // execSQL() : ResultSet (결과 데이터 집합)을 가져오지 않는 SQL 구문을 실행
              sqLiteDatabase.execSQL("CREATE TABLE IF NOT EXISTS Member ( userName TEXT, userAge INTEGER);");
              sqLiteDatabase.execSQL("INSERT INTO Member VALUES ('홍길동', 30);");
              sqLiteDatabase.execSQL("INSERT INTO Member VALUES ('박길동', 10);");
              sqLiteDatabase.execSQL("INSERT INTO Member VALUES ('공길동', 50);");
              Log.i("LOG_DATABASE", "Helper의 onCreate() 호출!!");
      
          }
      
          @Override
          public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {
      
          }
      }
      
      ```

      

      #### Member 테이블 생성 & 샘플 데이터 INSERT sql문

      - 컬럼으로 userName과 userAge를 갖는 Member 라는 테이블을 생성한다.

        ```mysql
        CREATE TABLE IF NOT EXISTS Member (
        	userName TEXT,
            userAge INTEGER,
        );
        ```

      - Member라는 테이블에 데이터를 넣는다. 

        ```mysql
        INSERT INTO Member VALUES ('홍길동', 30);
        INSERT INTO Member VALUES ('박길동', 10);
        INSERT INTO Member VALUES ('공길동', 50);
        ```

    

  - 데이터 베이스 접근을 수행하는 Activity에서 Helper 객체를 얻는다.

    - DatabaseTestActivity

    ```java
    MySQLiteHelper helper = new MySQLiteHelper(DatabaseTestActivity.this);
    ```

    

  - helper를 통해서 쓰기 또는 읽기 용도의 데이터 베이스 Handle을 갖는다. 

    - DatabaseTestActivity

    ```java
    private SQLiteDatabase db;
    
    db = helper.getWritableDatabase();
    ```

    

  - helper를 통해 연결된 SQLiteDatabase의 객체 db를 통해 특정 sql문을 수행한다.

    - DatabaseTestActivity

      : 버튼을 클릭하면 "Select * from Member;"를 실행하고, 결과 값을 Cursor에 담는다.

    ```java
    Button selectDB_btn = (Button)findViewById(R.id.selectDB_btn);
    selectDB_btn.setOnClickListener(new View.OnClickListener() {
        @Override	
        public void onClick(View view) {
    
            // rawQuery() : select 계열 (결과 데이터 집합이 있는 쿼리문) 의 SQL문을 실행할 때 사용되요!
    
            // Cursor: JDBC의 ResultSet과 동일하게 생각하면 된다.
            // : 위-> 아래의 방향으로 데이터를 추출하면 된다.
            Cursor c = db.rawQuery("SELECT * FROM Member", null);
            String result="";
    
            // == rs.next()
            while (c.moveToNext()){
                result += c.getString(0);
                result += ",";
                result += c.getInt(1);
                result += "\n";
            }
    
            // 여기에 해당 결과 데이터를 TextView에 출력하는 코드
    
        }
    });
    ```

    

    - TextView에 결과 뿌리기 

      ```java
      // 해당 결과를 TextView에 출력
      TextView textView = (TextView)findViewById(R.id.selectTv);
      textView.setText(result);
      ```

      

    ### 용어 

    #### Cursor 

    - 데이터 베이스 쿼리에서 반환한 결과 집합에 대한 임의의 읽기/쓰기 접근을 제공한다.

    - JDBC의 Result Set과 동일하게 생각하면 된다. 

    - 위 -> 아래의 방향으로 데이터를 추출한다.

    - 주요 Method

      - moveToNext () : 커서를 다음 행으로 이동한다.

      - getColumnCount() : 총 열의 개수를 반환한다.

      - getColumnName (int columnIndex) : 지정된 0부터 시작하는 열 인덱스에서 컬럼 명을 반환한다. 

      - getColumnIndex (String columnName) : 인자로 주어지는 컬럼 명에 대한 인덱스를 반환 (0부터 시작)한다. 만약, 열이 없으면 -1을 반환한다. 

      - getCount () : 커서의 행 수를 반환한다.

      - getPosition () : 행 집합에서 커서의 현재 위치를 반환한다. 

      - getString () : 요청한 컬럼의 값을 문자열로 반환한다.

        

    #### SQLiteDatabase

    - SQLiteDatabase를 관리하는 Method를 제공한다. 
    - SQL 명령을 작성, 삭제, 실행한다. 
    - 주요 Method
      - execSQL (String sql) : Select 문이 아닌 단일 sql문 또는 데이터를 반환하는 다른 sql문을 실행한다.
      - rawQuery (String sql, String[] selectionArgs) : 제공된 sql문을 실행하고, Cursor 결과 세트를 리턴한다. 



### Content Provider를 통해서 데이터 베이스의 데이터를 외부에 제공하는 방법

- 다른 프로젝트에서 Member 테이블에 접근하는 방법

- 실제로 공유하는 것은 데이터 베이스가 아니라 Class의 Method를 공유하는 것이다. 

- Content Provider 사용 

  - Content Provider Class 생성

    - com.test.data : 외부에서 해당 Uri로 접근해야  MyContentProvider.class를 사용할 수 있다.

    - ContentProvider 클래스를 상속받는다.

      ![1565346936251](https://user-images.githubusercontent.com/39547788/62773435-1e46b580-badd-11e9-903e-1ea67e1316ed.png)

    

    - AndroidManifest.xml

    ![1565338655830](https://user-images.githubusercontent.com/39547788/62773583-72ea3080-badd-11e9-9051-566805452da5.png)

    

    - MyContentProvider.class

      ```java
      package com.example.androidsample;
      
      import android.content.ContentProvider;
      import android.content.ContentValues;
      import android.database.Cursor;
      import android.database.sqlite.SQLiteDatabase;
      import android.net.Uri;
      import android.util.Log;
      
      public class MyContentProvider extends ContentProvider {
      
          private SQLiteDatabase db;
      
          public MyContentProvider() {
          }
      
          	.
              .@Override 메소드 생략
              .
      
      
          @Override
          public boolean onCreate() {
              // Content Provider가 만들어지면 helper를 생성함
              
              // 데이터 베이스를 조작할 수 있는 helper
              MySQLiteHelper helper = new MySQLiteHelper(getContext());
              db = helper.getWritableDatabase();
              Log.i("LOG_DATABASE", "Content Provider의 onCreate() 호출");
              return true;
          }
      
          @Override
          public Cursor query(Uri uri, String[] projection, String selection,
                              String[] selectionArgs, String sortOrder) {
      
              // select 계열의 SQL문이 실행되고 그 결과 Cursor를 다른 Application에 제공
              // 인자로 들어오는 값들을 이용해서 사용할 SQL문을 구성해야 한다.
              String sql = "SELECT * FROM Member";
      
              // 데이터 베이스를 뒤져서 나온 결과 데이터를 핸들인 Cursor 객체를 return 한다.
              return db.rawQuery(sql, null);
          }
      
          @Override
          public int update(Uri uri, ContentValues values, String selection,
                            String[] selectionArgs) {
              // TODO: Implement this to handle requests to update one or more rows.
              throw new UnsupportedOperationException("Not yet implemented");
          }
      }
      
      ```





### 다른 Application이 가지고 있는 데이터 베이스에 접근하는 방법

- Content Resolver 사용 

  - 새로운 프로젝트 (MyResolverApp) 생성

    ![1565327822605](https://user-images.githubusercontent.com/39547788/62773471-2ef72b80-badd-11e9-8902-ad864ae614e0.png)

    

  -  다른 App의 MyContentProvider에 접근하기 위해 ContentResolver를 얻는다. 

    ```java
    ContentResolver cr = getContentResolver();
    ```

    

  - 접근할 ContentProvider의 Uri Authorities를 설정한다. 

    - Uri 형식 : **content://**[ContentProvider의 Uri Authorities]**/**[접근할 데이터 베이스명]
      - content://com.test.data/Member : Member 테이블에 대한 Uri

    ```java
    // 'content://' + 어떤 ContentProvider + '/' + 어떤 데이터 베이스에 접근할 것인지
     Uri uri = Uri.parse("content://com.test.data/Member");
    ```

    

  - 지정한 Uri Authorities의 ContentProvider의 query() Method를 호출하고, Cursor를 통해 결과 데이터를 반환한다. 

    ```java
    // MyContentProvider의 query() Method 호출
     Cursor c = cr.query(uri, null, null, null, null);
    ```

    

  - Cursor를 통해 얻은 결과 데이터를 TextView에 출력한다. 

    ```java
    `			String result="";
    
                // == rs.next()
                while (c.moveToNext()){
                    result += c.getString(0); // userName
                    result += ",";
                    result += c.getInt(1);    // userAge
                    result += "\n";
                }
    
                // 이렇게 데이터를 다 얻어오면, 해당 결과를 TextView에 출력
                final TextView tv = (TextView)findViewById(R.id.tv);
                tv.setText(result);
    ```

    
