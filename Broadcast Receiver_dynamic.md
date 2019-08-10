## Broadcast Receiver

### Broadcast 

- 안드로이드 시스템으로부터 나오는 신호 
- 사용자  Application에서 발생시키는 임의의 신호
- 베터리 용량 부족, 네트워크 환경 바꿈, USB 케이블 연결/ 비연결




- BroadcastTestActivity

  - 버튼을 클릭하면 Broadcast를 발생시킨다.

  - 묵시적 Intent를 사용하여 Action을 설정한다. 

    ```java
        Button sendSignal_btn = (Button)findViewById(R.id.sendSignal_btn);
        sendSignal_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
    
                // 신호를 발생시키기 위해서 Intent 가 필요 - Action을 설정한다 .
                Intent i = new Intent();
                
                // MY_BROADCAST_DYNAMIC 의 액션을 잡기
                i.setAction("MY_BROADCAST_DYNAMIC");
                Log.i("BROADCAST_DYNAMIC", "BroadCast 발생");
                sendBroadcast(i);
            }
        });
    ```

    
    

  - AndroidManifest.xml에 Action과 Category를 등록한다.

    ```xml
    <activity android:name=".BroadcastTestActivity">    
        <intent-filter>        
            <action android:name="START_BROADCAST_ACTIVITY" />
            <category android:name="android.intent.category.DEFAULT" />
        </intent-filter>
    </activity>
    ```

    



### Broadcast Receiver

- Broadcast를 청취/수신하는 Component

- 사용자와의 대면은 하지 않는다. 

- 발생한 Broadcast를 catch하기 위에서는 BroadCast Receiver를 등록해야한다. 등록한 BroadCast Receiver를 해제할 수도 있다.

  - activity_broadcast_test.xml

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout android:layout_height="match_parent"
        android:layout_width="match_parent"
        android:orientation="vertical"
        xmlns:android="http://schemas.android.com/apk/res/android" >
    
        <Button
            android:id="@+id/register_btn"
            android:text="Broadcast Receiver 등록"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    
        <Button
            android:id="@+id/unRegister_btn"
            android:text="Broadcast Receiver 등록 해제"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    
        <Button
            android:id="@+id/sendSignal_btn"
            android:text="Broadcast 발생!!!!!!!!"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    
    </LinearLayout>
    ```

    


- Broadcast Receiver를 등록/해제 

  - BroadcastTestActivity

    - ### Broadcast Receiver 등록하기

      - Intent Filter 생성

        : Broadcast Receiver가 청취할 특정 Broadcast를 설정할 Filter 이다.  (AndroirManifest.xml의 < intent-filter >와 같은 기능)

        ```java
        IntentFilter filter = new IntentFilter();
        ```


        
        

      - 생성한 Intent Filter에 Action을 설정한다. 

        - 안드로이드 시스템에서 나오는 정해진 다수의 Broadcast를 catch 할 수 있다. 
        - AndroirManifest.xml의 < intent-filter > 와 같은 기능을 수행한다. 
          - 다수의 Action을 설정할 수 있다. 

        ```java
        filter.addAction("MY_BROADCAST_DYNAMIC");
        ```

        
        

      - Broadcast Receiver 객체를 생성한다. 

        - Broadcast Receiver 객체는 AnonyClass (익명 클래스) 방식을 사용한다.

          - BroadcastReceiver의 함수를 오버라이딩하며 Inner Class로 객체를 생성해야 한다. 

            ```java
            // onCreate() 외부, Class 가장 상단에 전역변수로 선언
            private BroadcastReceiver receiver;
            
            // onCreate() 안에 
            
            receiver = new BroadcastReceiver() {
                @Override
                public void onReceive(Context context, Intent intent) {
                    //  Broadcast 를 잡았을 때 처리해야할 코드 작성
                    Toast.makeText(context, "등록된 Broadcast Receiver 사용중~", Toast.LENGTH_SHORT).show();
            
                }
                // Broadcast Receiver 등록하기 코드작성
                
            };
            ```


        

      - Broadcast Receiver를 생성한 이후,  Activity에 등록한다.

        - 등록된 Broadcast Receiver가 Broadcast (신호) 를 catch 할 수 있다.

        - 인자로 등록할 Broadcast Receiver 객체와 해당 객체가 사용할 Intent Filter를 제공한다.

          ```java
          registerReceiver(receiver, filter);
          ```

      


      

    - ### Broadcast Receiver 등록 해제하기

      - 현재 등록이 되어 있는 Broadcast Receiver 객체 인지 check한 후, 등록된 경우 해당 Broadcast Receiver의 등록을 해제한다.

        ```java
        if (receiver != null) {
            unregisterReceiver(receiver);
            Log.i("BROADCAST_DYNAMIC", "BroadCast Receiver 해제");
        }
        ```

        
        

