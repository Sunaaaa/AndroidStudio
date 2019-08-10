## 연락처 

- 사용자의 주소록에 접근하여 등록된 사람의 이름과 전화번호를 화면에 출력한다.

- 주소록에 접근하여 읽고 쓸 수 있도록 Permission을 등록한다.

  ```xml
  <uses-permission android:name="android.permission.READ_CONTACTS"/>
  <uses-permission android:name="android.permission.WRITE_CONTACTS"/>
  ```



- MainActivity

  - 버튼을 클릭하면 묵시적 Intent를 통하여 특정 Activity를 호출한다. 

    ```java
    phone_btn.setOnClickListener(new View.OnClickListener() {
         @Override
         public void onClick(View view) {
             // 새로운 Activity 호출
             Intent i = new Intent();
             i.setAction("CONTACT_ACTIVITY");
             startActivity(i);
         }
     });
    ```

    

- AndroidManifest.xml

  - MyContactActivity의 Action이 "CONTACT_ACTIVITY" 로 설정 되어 있으므로, MainActivity에서 MyContactActivity가 호출된다. 

    ```xml
    <activity android:name=".MyContactActivity">
        <intent-filter>
            <action android:name="CONTACT_ACTIVITY"/>
    
            <category android:name="android.intent.category.DEFAULT" />
        </intent-filter>
    </activity>
    ```

  

- activity_my_contact.xml

  - 버튼 (contact_btn)을 클릭하면, TextView (contactTv)에 주소록에 등록된 사람이름과 전화 번호를 출력을 위한 Layout

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout android:layout_height="match_parent"
        android:layout_width="match_parent"
        android:orientation="vertical"
        xmlns:android="http://schemas.android.com/apk/res/android" >
    
        <Button
            android:text="주소록을 가져와요~"
            android:id="@+id/contact_btn"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    
        <TextView
            android:id="@+id/contactTv"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    
    </LinearLayout>
    ```



- MyContactActivity

  - 주소록에 접근하여 사람 이름과 전화번호를 출력한다.

    - ContentProvider에 접근 하기 위한 ContentResolver 객체를 생성한다. 

      ```java
      ContentResolver cr = getContentResolver();
      ```

      

    - 주소록 Uri

      ```java
      Uri uri = ContactsContract.CommonDataKinds.Phone.CONTENT_URI;
      ```

      

    - 주소록 Uri를 통해 ContentResolver 객체는 ContentProvider의  query() 를 수행한다. 

      ```java
      Cursor c = cr.query(uri, null, null, null, null);
      ```

      

    - Cursor를 통해 결과 데이터를 출력한다.

      - c.getString(컬럼 번호) : Cursor가 위치한 곳의 주어진 컬럼 번호의 데이터 값을 가져온다.

      - c.getColumnIndex(컬럼이름) : 컬럼 이름을 통해 컬럼 번호를 알아온다.

      ```java
      String result = "";
      
      // 사람 이름 컬럼
      String name = ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME;
      // 전화번호 컬럼
      String pnum = ContactsContract.CommonDataKinds.Phone.NUMBER;
      
      while (c.moveToNext()) {
          result += c.getString(c.getColumnIndex(name));
          result += ",";
          result += c.getString(c.getColumnIndex(pnum));
          result += "\n";
      }
      
      // 이렇게 데이터를 다 얻어오면, 해당 결과를 TextView에 출력하는 코드 작성
      
      ```

    - 결과 데이터를 TextView에 출력하기 

      ```java
      final TextView tv = (TextView) findViewById(R.id.contactTv);
      tv.setText(result);
      Log.i("LOG_PHONE", "성공");
      ```

      

  - **<u>보안 관련 코드가 필요!</u>**

    - 간단 보안 로직 

      - 주요 Method

        - 권한 확인 : checkSelfPermission(권한 명)

        - PackageManager.PERMISSION_GRANTED : 앱이 권한이 있는 경우

        - PackageManager.PERMISSION_DENIED : 앱이 권한이 없는 경우

        - 권한이 필요한 이유 설명 :   ActivityCompat.shouldShowRequestPermissionRationale()

        - 권한 요청 : requestPermissions(String[]{권한 명}, requestCode)

        - 권한 요청 결과 가져오기
      
          ```java
          @Override
          public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
              super.onRequestPermissionsResult(requestCode, permissions, grantResults);
       
          ```
  
        - grantResults : 권한 허용 여부가 들어있다. 
  
          - 사용자가 주소록 접근에 대한 허용을 한 경우
  
            ```java
        grantResults[0] == PackageManager.PERMISSION_GRANTED
            ```
      
            
      
        
    
    - 보안 로직 코드화 
    
      ```java
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                          // 마시멜로우 버전 (6) 보다 높은 경우
      
                          // 사용자에게 허가를 얻은 적이 있는지
          if (checkSelfPermission(Manifest.permission.READ_CONTACTS) != PackageManager.PERMISSION_GRANTED) {
              // 이전에 허용을 한 적이 없는 경우
              // 사용자의 허가를 얻어야 한다.
              // 여러 개의 권한을 한번에 처리할 수 있기 때문에 String[] 의 형태
              // 주소록의 데이터를 가져오는 permission을 요청하는 알림창이 빵
              // requestCode는 1111
              requestPermissions(new String[]{Manifest.permission.READ_CONTACTS}, 1111);
      
          } else {
              // 이미 이전에 허용을 한 적이 있는 경우
              ContentResolver cr = getContentResolver();
      
              // 주소록 접근 URI
              Uri uri = ContactsContract.CommonDataKinds.Phone.CONTENT_URI;
              Cursor c = cr.query(uri, null, null, null, null);
      
              String result = "";
      
              // 사람 이름 컬럼
              String name = ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME;
              // 전화번호 컬럼
              String pnum = ContactsContract.CommonDataKinds.Phone.NUMBER;
      
              // == rs.next()
              while (c.moveToNext()) {
                  // c.getColumnIndex() : 컬럼 이름을 통해 컬럼 번호를 알아온다.
                  result += c.getString(c.getColumnIndex(name));
                  result += ",";
                  result += c.getString(c.getColumnIndex(pnum));
                  result += "\n";
              }
      
              // 이렇게 데이터를 다 얻어오면, 해당 결과를 TextView에 출력
              final TextView tv = (TextView) findViewById(R.id.contactTv);
              tv.setText(result);
              Log.i("LOG_PHONE", "성공");
  
      
          }
      
      } else {
          // 마시멜로우 버전 (6) 보다 낮은 경우
      }
      ```
    
    - requestPermissions(new String[]{Manifest.permission.READ_CONTACTS}, 1111);를 수행하면 onRequestPermissionsResult() Method가 자동으로 호출된다. 
    
      - 권한 접근 허용 여부에 대한 정보를 갖는 grantResults를 이용하여 보안 관련 코드를 작성한다. 
    
      ```java
      @Override
      public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
          super.onRequestPermissionsResult(requestCode, permissions, grantResults);
          if (requestCode == 1111) {
      
              try {
      
                  // grantResults :  권한 허용 여부가 들어있음
                  if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                      // 만약, 사용자가 주소록 접근에 대한 허용을 누르면
                      // Content Resolver를 이용해서 주소록에 접근
                      ContentResolver cr = getContentResolver();
      
                      // 주소록 접근 URI
                      Uri uri = ContactsContract.CommonDataKinds.Phone.CONTENT_URI;
                      Cursor c = cr.query(uri, null, null, null, null);
      
                      String result = "";
      
                      // 사람 이름 컬럼
                      String name = ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME;
                      // 전화번호 컬럼
                      String pnum = ContactsContract.CommonDataKinds.Phone.NUMBER;
      
                      // == rs.next()
                      while (c.moveToNext()) {
                          // c.getColumnIndex() : 컬럼 이름을 통해 컬럼 번호를 알아온다.
                          result += c.getString(c.getColumnIndex(name));
                          result += ",";
                          result += c.getString(c.getColumnIndex(pnum));;
                          result += "\n";
                      }
      
                      // 이렇게 데이터를 다 얻어오면, 해당 결과를 TextView에 출력
                      TextView tv = (TextView) findViewById(R.id.contactTv);
                      tv.setText(result);
                      Log.i("LOG_PHONE", "성공");
      
                  }
              } catch (Exception e) {
                  Log.i("LOG_PHONE", e.toString());
      
              }
      
          }
      }
      ```
    



- 실행화면 

  - activity_my_contact.xml

    - '주소록을 가져와요~' 버튼 클릭

      ![1565411485652](https://user-images.githubusercontent.com/39547788/62817574-1e45c480-bb74-11e9-944e-b61045059219.png)

    - 버튼 하단의 TextView에 연락처에 등록된 사람 이름과 연락처를 뿌린다.

      ![1565411774956](https://user-images.githubusercontent.com/39547788/62817576-23a30f00-bb74-11e9-83a6-3ed7f5dd91d1.png)

  

