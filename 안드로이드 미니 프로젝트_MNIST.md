# 안드로이드 미니 프로젝트_MNIST

- ### MNIST 이미지 예측 프로그램을 이용하여, 안드로이드 이미지 예측 Application을 제작한다

- 로직 
  1. 안드로이드 내에서 카메라 실행 
  2. 카메라를 이용하여 예측할 이미지를 촬영한다. 
  3. '이미지 예측하기' 버튼을 통해 이미지를 예측한다.
  4. 예측 결과 값을 사진 하단에 출력한다.
- 이미지 전송 방식 
  1. 이미지 파일 자체를 예측 이미지로 보낸다. 
  2. 안드로이드에서 이미지 전처리를 통하여 
- 이미지 예측 방식 
  - Servlet 이용하여 이미지 예측한다. 
  - Maven Project의 Controller를 이용하여 이미지를 예측한다.



- 안드로이드 프로젝트 구조 

  - AndroidManifest.xml

    - permission 추가

      ```xml
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
      <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
      <uses-permission android:name="android.hardware.camera" android:required="true" />
      ```

      

  - MainActivity.java

  - activity_main.xml

  - PredictImageActivity.java

  - activity_predict_image.xml

  - ImagePredictService.java

  - round_button.xml

    - 버튼의 스타일 지정

      - 버튼이 눌린 상태가 아닐 때

        - 색상 : 

          

      - 버튼이 눌린 상태일때

        - 색상 : 

      ```xml
      <?xml version="1.0" encoding="utf-8"?>
      <selector xmlns:android="http://schemas.android.com/apk/res/android">
      
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





#### 안드로이드 레이아웃

- Main