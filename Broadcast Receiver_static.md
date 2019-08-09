## Broadcast Receiver

### Broadcast 

- 안드로이드 시스템으로부터 나오는 신호 
- 사용자  Application에서 발생시키는 임의의 신호
- 베터리 용량 부족, 네트워크 환경 바꿈, USB 케이블 연결/ 비연결



### Broadcast Receiver

- Broadcast를 청취/수신하는 Component

- 사용자와의 대면은 하지 않는다. 

  

### 정적 Broadcast 예제

- 버튼 (broad_btn) 을 클릭하면, "MY_BROADCAST" 라는 Action을 수행한다. 해당 Action이 수행되면서 Broadcast가  발생하고, 이를 수신하는 MyReceiver에게 메시지를 전달한다. 

  - MainActivity

    - Intent를 통해 Action을 설정한다. 

    - 전송한 메시지를 Intent에 넣는다. 

    - Intent를 통해 Broadcast를 발생시킨다.

      ```java
      broad_btn.setOnClickListener(new View.OnClickListener() {
          @Override
          public void onClick(View view) {
              // 새로운 Activity 호출
              Intent i = new Intent();
              i.setAction("MY_BROADCAST");
              i.putExtra("BroadCastMsg", "브로트케스트는 메세지가 전달되요~");
              sendBroadcast(i);
          }
      });
      ```

    

  - Receiver 생성하기

    - MyReceiver

      - BroadcastReceiver를 상속받고, onReceive() 를 오버라이딩 하는 클래스 생성

        ![1565351332579](https://user-images.githubusercontent.com/39547788/62777304-16404300-bae8-11e9-9630-20e4870f2f5c.png)

        

      - onReceive (Context context, Intent intent) 의 인자로 주어진 Intent를 통해 Intent에서 전달한 메시지를 받는다.

        ```java
        public class MyReceiver extends BroadcastReceiver {
        
            @Override
            public void onReceive(Context context, Intent intent) {
                String msg = intent.getExtras().getString("BroadCastMsg");
                Toast.makeText(context,msg,Toast.LENGTH_SHORT).show();
            }
        }
        ```

        

      