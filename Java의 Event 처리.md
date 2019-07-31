## Java의 Event 처리

- 이벤트 여부를 지속적으로 검사하면서 기다리는 프로세스가 항상 동작하는 것 보다는 이벤트가 발생된 주체가 이벤트가 발생했음을 특정 이벤트 처리자에 통지하여 처리하도록 하는 것이 보다 효율적이다. 

  ==> 동일한 이벤트에 대해 서로 다른 처리 초직을 쉽게 추가하거나 변경할 수 있어 보다 유연하게 Application을 작성할 수 있다. 

- Component가 특정 Event를 발생시키고, 특정 Event가 발생했을 경우 이에 대한 처리를 Event Listener기 처리하는 구조이다. 

  ==> Component가 이벤트 처리를 Listener에게 위임(Delegate)하기 때문에 Delegation Event Model 이라고 한다.

### Delegation Model

1. Event Source 

   - Edit Text, Button 등 이벤트가 발생한 객체를 지칭 = Component

   - 자바 초창기에는 Event Source (이벤트가 발생한 View)가 직접 Event를 처리

     => 비 효율적, 프로그램 상의 어려움 발생

     ==> 이벤트 처리 방식을 **<u>"Delegation Model (위임)"</u>** 으로 변경

   - Delegation Model : 이벤트를 전문적으로 처리하는 객체에게 위임하여 이벤트를 처리하도록 한다.

     => Component에서 이벤트가 발생하면 Component에 등록된 Listener 들에게 특정한 이벤트가 발생했음을 알린다.

     

2. Event Listener

   - 이벤트 처리 객체/처리자를 지칭

     

3. Event 객체 

   - Button은 1번 터치, 2번 터치 등의 이벤트 정보를 가짐

   - 자바에서 발생하는 모든 Event에 대해 세부적인 정보를 가지고 있는 객체를 지칭

     => 이벤트 처리 시스템에 의해 Event Listener에게 해당 객체가 전달 



** Event Source에 Listener가 부착 (Linstener가 부착되어 있지 않으면, Event가 발생했음에도 불구하고 어떠한 처리를 수행할 수 없다.)



## Android Event 처리

- Java 코드 내에서 해당 View에 이벤트를 등록 (해당 View의 id 사용) 시켜, 추후에 이벤트 발생시 Listener가 동작하여 이벤트를 처리하도록 한다.

- ### Event Source 

  - ```java
    Button button = (Button)findViewById(R.id.btn1);
    ```

    - id 값으로 View를 가져오는데 Object Type으로 반환하기 때문에, 원래 View로 형변환을 수행하고, 변수에 담는다.
    
      

- ### Event Listener

  - Event Source 에 부착할 Event 처리 객체를 만들기 위한 새로운 Class 를 정의한다. 

    - 특정 Event를 처리할 수 있는 Interface를 implements한다.

      - 추상 메소드 onClick (View v)를 Override 를 해야한다. 

        ```java
            class MyClickListener implements View.OnClickListener {
        
                @Override
                public void onClick(View view) {
                
                }
            }
        
        
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_main);
        
                // btn1 : Event Source
                Button btn1 = (Button)findViewById(R.id.linear_btn);
        
               // 새로운 클래스 만들어서 이벤트 처리
                //Listener 객체 생성
                MyClickListener listener = new MyClickListener();
                // Event Source에 Listener 객체를 부착
                btn1.setOnClickListener(listener);
            }
        ```
      
        ==> Listener 객체를 생성할 수 있는 클래스를 생성할 수 있다.
      
      
      
      
    
  - Anonymous 클래스를 통해 Event Source에 Event Listener를 부착한다.
  
    - Anonymouse Class : 전체 클래스 내부에 하나의 클래스가 익명으로 들어가 있음
  
      ```java
      	Button book_btn = (Button)findViewById(R.id.book_btn);
              
          linear_btn.setOnClickListener(new View.OnClickListener() {
                  @Override
                  public void onClick(View view) {
                     
                  }
          });
      ```
  
      



## Activity 호출

### Intent

- Explicit Intent

  : 명시적으로 특정 Activity를 지정해서 실행시키는 방식

- Activity 사이의 중간 매개체 객체로 생각하면 된다. 

- ComponentName ("사용하려는 Component가 포함되어 있는 패키지 이름", "사용할 실제 Class의 이름")

  ```java
  ComponentName cname = new ComponentName("com.example.androidsample","com.example.androidsample.LinearLayoutExampleActivity");
  ```






this와 

- context를 지칭한다. 

- 해당 Activity

  

클래스 명.this

- Anonymouse Class 내부에서의 this는 Listener 객체를 지칭하기 때문에, context를 지정하기 위해서는 클래스명.this 로 접근해야한다. 





final 이라는 키워드로 상수화 

-> 실제 Stack이 아닌 상수 pool에 참조할 레퍼런스를 올린다. => Run Time 상수 pool에 들어간다.

-> 추후에 해당 값을 참조해서 실행할 바이트 코드를 찾아 메모리에 적재 

-> 동적 로딩이 가능



1. 클래스의 멤버 변수로 하면 Heap => 메모리에 안좋아 
2. 



- 이미지 처리

```
android:scaleType="fitCenter"android:adjustViewBounds="true"
```















