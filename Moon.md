월령 API 활용

# Moon Application

### MoonActivity

- Splash 화면 

  - 기존 : inflate를 통해 layout 파일을 변경하고, Thread.sleep(4000) , Intent 등을 이용해 Miain  화면으로 화면 전환
  - Moon Application : layout 파일을 사용하지 않고, Activity의 Theme 배경을 변경한다.

- style.xml에 사용할 SplashTheme를 제작한다.

  - android:windowBackground : 

    ```xml
    <resources>
    
        <!-- Base application theme. -->
        <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
            <!-- Customize your theme here. -->
            <item name="colorPrimary">@color/colorPrimary</item>
            <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
            <item name="colorAccent">@color/colorAccent</item>
        </style>
    
        <style name="SplashTheme" parent="Theme.AppCompat.NoActionBar">
            <item name="android:windowBackground">@drawable/moon</item>
        </style>
    
    </resources>
    
    ```

- MoonActivity를 최초 실행하는 Activity로 설정하고, theme 를 제작한 SplashTheme로 설정한다.

  ```xml
  <activity android:name=".MainActivity">
  
  </activity>
  <activity
            android:name=".MoonActivity"
            android:theme="@style/SplashTheme">
      <intent-filter>
          <action android:name="android.intent.action.MAIN" />
  
          <category android:name="android.intent.category.LAUNCHER" />
      </intent-filter>
  </activity>
  ```

  
