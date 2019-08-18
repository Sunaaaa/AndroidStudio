# 안드로이드 미니 프로젝트_MNIST

- ### MNIST 이미지 예측 프로그램을 이용하여, 안드로이드 이미지 예측 Application을 제작한다

- 로직 

  1. 안드로이드 내에서 카메라 실행 
  2. 카메라를 이용하여 예측할 이미지를 촬영한다. 
  3. '이미지 예측하기' 버튼을 통해 이미지를 예측한다.
  4. 예측 결과 값을 사진 하단에 출력한다.

- 이미지 전송 방식 

  1. 이미지 파일 자체를 예측 이미지로 보낸다. 
  2. 안드로이드에서 이미지 전처리를 통하여 픽셀 데이터로 변환한 뒤, 해당 데이터를 String Type으로 예측 프로그램의 이미지 값으로 전달한다. 
  3. 해당 String 픽셀 데이터를 python 코드에서 다시 이미지로 변환하여 예측 프로그램의 실제 이미지 데이터로 재 생성한다. 

- 이미지 예측 방식 

  - Servlet 이용하여 이미지 예측 프로그램을 작성한다. 



- 안드로이드 프로젝트 구조 

  - ### AndroidManifest.xml

    - permission 추가

      ```xml
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
      <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
      <uses-permission android:name="android.hardware.camera" android:required="true" />
      ```

      

  - ### PermissionRequest.java

    - 특정 요청 권한 허용 여부를 확인하는 클래스 

      - 요청 권한의 허용 여부를 리턴하는 변수 (permissionCheck) 를 선언한다. 

      - 만약, permissionCheck 가 PackageManager.PERMISSION_DENIED이면, 사용자에게 권한 허용을 요청하는 코드를 작성한다. 

        ```java
        context.requestPermissions(new String[]{permission}, requestCode);
        ```

        

      - 만약, permissionCheck 가 PERMISSION_GRANTED이면, 수행할 코드 (사진 파일 저장 등) 를 작성한다. 

    - Builder Class

      - 사용자의 권한 허용을 요청하는 경우, Dialog를 생성하여 권한 허용 여부를 요청한다. 

        

  - ### MainActivity.java

    - '숫자 예측하러 가시죠' 버튼 (goBtn) 클릭 시, 실제 숫자를 예측하는 Activity로 화면으로 이동한다.

      ```java
      Button goBtn = (Button)findViewById(R.id.goBtn);
      goBtn.setOnClickListener(new View.OnClickListener() {
          @Override
          public void onClick(View view) {
              Intent i = new Intent();
              ComponentName cname = new ComponentName("com.example.myapplication","com.example.myapplication.PredictImageActivity");
              i.setComponent(cname);
              startActivity(i);
      
          }
      });
      ```

      - Intent를 이용하여 화면 전환을 수행한다. 
        - ComponentName Class
          - Component를 식별하는 클래스 
          - 2가지 정보 
            - 해당 Component가 들어있는 패키지 명
            - 해당 패키지에 들어있는 실제 Component의 이름
      - i.setComponent() : 명시적 인텐트로 인자로 주어진 Component의 정보를 갖는다. 
      - startActivity(i) : 인텐트를 통해 주어진 Activity를 실행한다. 

      

  - ### activity_main.xml

    - 화면 이동을 위한 버튼 하나를 생성한다. 

      ```xml 
      <?xml version="1.0" encoding="utf-8"?>
      <LinearLayout android:layout_height="match_parent"
          android:layout_width="match_parent"
          android:orientation="vertical"
          xmlns:android="http://schemas.android.com/apk/res/android" >
      
          <Button
              android:id="@+id/goBtn"
              android:layout_margin="20px"
              android:gravity="center"
              android:background="@drawable/round_button"
              android:text="숫자 예측하러 가시죠"
              android:layout_width="match_parent"
              android:layout_height="match_parent" />
      
      </LinearLayout>
      
      ```

      

  - ### PredictImageActivity.java

    - '이미지 예측하기' 버튼 (doPredict_btn) 클릭 시, String Type의 이미지 픽셀 데이터로 변환하여 해당 데이터를 Intent에 담고, Intent를 통해 Service를 시작한다.

      ```java
      doPredict_btn.setOnClickListener(new View.OnClickListener() {
          @Override
          public void onClick(View view) {
              Intent i = new Intent();
              ComponentName cname = new ComponentName("com.example.myapplication", "com.example.myapplication.ImagePredictService");
              i.setComponent(cname);
      
              bm = resizeMyBitmap(bm);
              String predictImage = getPixel(bm);
      
              imageView.setImageBitmap(toBlachNWhite(resizeMyBitmap(bm)));
              resultTv.setText("");
      
              i.putExtra("IMG", predictImage);
              startService(i);
          }
      });
      ```

    - MNIST 이미지 예측을 위해 28 * 28 크기의 숫자 이미지로 크기를 변환한다. 

    - 이미지를 흑백으로 변환하여 0 또는 1의 흑백 이미지로 변환한다.

    - 변환한 숫자 이미지를 String Type의 이미지 픽셀 데이터로 변형한다. 

    - 

    - 

  

  

  

  

  

  

  - ### activity_predict_image.xml

    - '사진 찍기' 버튼 (takePhoto_btn), '갤러리에서 가져오기' 버튼 (getPhoto_btn) 을 생성하여 예측할 사진을 선택한다.

    - '이미지 예측하기' 버튼 (doPredict_btn) 을 생성하여 숫자 예측을 수행하도록 한다. 

    - 이미지 뷰 (predict_img)

      - '사진 찍기' 혹은 '갤러리에서 가져오기' 버튼을 통해 불러온 사진이 출력된다. 
      - '이미지 예측하기' 버튼이 눌리면, 해당 이미지를 흑백 사진으로 변형하여 재 출력한다. 

    - 텍스트 뷰 (resultTv)

      - 숫자 이미지 예측 결과 값이 출력된다. 
        - 숫자 이미지 예측 수행 전의 text : "예측 결과 값이 여기에 보입니다."
        - 숫자 이미지 예측 수행 후의 text : "예측 값은 0 입니다."

      ```xml
      <?xml version="1.0" encoding="utf-8"?>
      <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
          android:layout_width="match_parent"
          android:layout_height="match_parent"
          android:orientation="vertical">
      
          <LinearLayout
              android:layout_width="match_parent"
              android:layout_height="wrap_content"
              android:layout_margin="20px"
              android:orientation="vertical">
      
              <LinearLayout
                  android:layout_width="match_parent"
                  android:layout_height="wrap_content"
                  android:layout_margin="20px"
                  android:layout_weight="1"
                  android:orientation="horizontal">
      
                  <Button
                      android:id="@+id/takePhoto_btn"
                      android:layout_width="wrap_content"
                      android:layout_height="wrap_content"
                      android:layout_weight="1"
                      android:background="@drawable/round_button"
                      android:text="사진 찍기" />
      
                  <Button
                      android:id="@+id/getPhoto_btn"
                      android:layout_width="wrap_content"
                      android:layout_height="wrap_content"
                      android:layout_weight="1"
                      android:background="@drawable/round_button"
                      android:text="갤러리에서 가져오기" />
      
      
              </LinearLayout>
      
              <Button
                  android:id="@+id/doPredict_btn"
                  android:layout_width="match_parent"
                  android:layout_height="wrap_content"
                  android:layout_margin="20px"
                  android:layout_weight="1"
                  android:background="@drawable/round_button"
                  android:text="이미지 예측하기" />
      
      
          </LinearLayout>
      
          <ImageView
              android:id="@+id/predict_img"
              android:layout_width="match_parent"
              android:layout_height="wrap_content"
              android:adjustViewBounds="true"
              android:scaleType="fitCenter" />
      
          <TextView
              android:id="@+id/resultTv"
              android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:text="예측 결과 값이 여기에 보입니다."
              android:textSize="50px" />
      
      </LinearLayout>
      
      ```

      

  - ### ImagePredictService.java

    - 이미지 픽셀 값을 이용하여 숫자 이미지 예측 프로그램을 수행하는 Thread를 수행하고 결과를 받는다. 

      ```java
      PredictRunnable runnable = new PredictRunnable(myImage);
      Thread t = new Thread(runnable);
      
      try {
          t.start();
          try{
              t.join();
          }catch (Exception e) {
              Log.i("ERROR_THREAD", e.toString());
          }
          resultIntent.putExtra("result", result);
          Log.i("RESULT", result);
      }catch (Exception e){
          Log.i("ERROR_THREAD", e.toString());
      }
      ```

      

    - 숫자 이미지 예측 기능을 별도의 Service로 수행하고 결과 값을 Intent를 통해 전송한다. 

      *** Service의 binder를 사용하는 방법도 있다.

      - MainActiviy.java로 부터 Intent를 통해 Image의 픽셀 값을 String으로 전달 받는다. 

        ```java
        final String myImage = intent.getStringExtra("IMG");
        ```

      - PredictRunnable Class를 Service의 내부 함수로 생성한다. 

        - Runnable을 이용해서 네트워크 연결을 수행하고 결과 데이터를 PredictImageActivity로 전달하는 Thread 를 구현한다.

          - 이미지의 픽셀 값을 인자로 전달받아, 해당 픽셀 String으로 predict 프로그램에 접속한다. 

            - 네트워크 연결은 Exception의 발생 여지가 분명 있기 때문에, 반드시 try-catch 문을 이용하여 예외처리를 작성한다. (예외 처리 문이 없는 경우, 컴파일 에러 발생)

              ```java
              public PredictRunnable(String keyword) {
                  this.keyword = keyword;
              }
              
              // run() Method 내부 
              String url = "http://70.12.115.72:80/MNIST/predict?KEYWORD=" + keyword;
              
              // url을 URL 객체로 만들어 실제 url의 주소로 접속한다.
              URL urlObject = new URL(url);
              HttpURLConnection con = (HttpURLConnection)urlObject.openConnection();
              ```

            

            - HTTP 응답 메시지 상태 코드 (con.getResponseCode()) 가 200 (HTTP_OK) 인 경우, 결과 데이터를 읽어 들인다. 

              ```java
              if (con.getResponseCode()==200){
              	// 결과 데이터 읽어 들이는 코드 
              }
              ```

              

            - 네트워크 연결이 성공한 후, 데이터를 읽어들이기 위한 데이터 연결 통로인 Stream을 생성하여, 서버가 보내는 데이터를 다 읽어 들인다. 

              - con.getInputStream() : Stream 을 뽑아 낸다. 

              - new InputStreamReader(con.getInputStream()) : 조금 더 나은 Stream 생성한다.

              - new BufferedReader(new InputStreamReader(con.getInputStream())) : 조금 더 더 나은 Stream으로 생성한다.

              - input=br.readLine() : 서버가 보내주는 데이터를 읽어 input 변수에 담는다.

              - StringBuffer 를 사용하여 문자열 (서버가 보내주는 데이터) 을 추가 (append) 한다. 

                ```java
                BufferedReader br = new BufferedReader(new InputStreamReader(con.getInputStream()));
                String input = "";
                StringBuffer sb = new StringBuffer();
                
                while ((input=br.readLine())!= null){
                	result += sb.append(input).toString();
                }
                ```

              

            - 결과 데이터를 Intent에 담아 PredictImageActivity에게 전달한다. 

              ```java
              Intent intent = new Intent(ImagePredictService.this, PredictImageActivity.class);
              ComponentName cname = new ComponentName("com.example.myapplication", "com.example.myapplication.PredictImageActivity");
              intent.setComponent(cname);
              intent.putExtra("result", result );
              startActivity(intent);
              ```

      

    

    

    

  - ### round_button.xml

    - 버튼의 스타일 지정

      - 버튼이 눌린 상태일때 

      - 버튼일 늘린 상태가 아닐 때 

  ```xml
      <?xml version="1.0" encoding="utf-8"?>
  <selector xmlns:android="http://schemas.android.com/apk/res/android">
      
      <!-- 버튼이 눌린 상태일때 -->
          <item android:state_pressed="true">
            <shape>
                  <solid android:color="@color/colorBtn"/>
                  <stroke
                      android:width="5px"
                      android:color="@android:color/black" />
                  <corners
                      android:radius="10dp"/>
              </shape>
          </item>
      
          <!-- 버튼이 눌린 상태가 아닐 때 -->
          <item android:state_pressed="false">
              <shape>
                  <solid android:color="@android:color/holo_orange_dark"/>
                  <stroke
                      android:width="5px"
                      android:color="@android:color/black" />
                  <corners
                      android:radius="10dp"/>
              </shape>
          </item>
      
      </selector>
  ```

  - 적용 화면

  - 버튼이 눌린 상태가 아닐 때

    ![1566010919500](https://user-images.githubusercontent.com/39547788/63213683-4623c200-c14a-11e9-946a-42c6231ea143.png)

    - 버튼이 눌린 상태일때 

      ![1566010949498](https://user-images.githubusercontent.com/39547788/63213685-4c19a300-c14a-11e9-9e7b-0308b7a586e0.png)





#### 안드로이드 레이아웃

- Main
