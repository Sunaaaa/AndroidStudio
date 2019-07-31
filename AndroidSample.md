# AndroidSample

- 프로젝트 명

## Activity 

1. MainActivity 

   - 

2. LinearLayoutActivity

   - 

3. ChattingActivity

   - 

4. ChangeImageActivity

   - Button 클릭시, young.png에서 now.png로 이미지가 변경된다. 

     - ```java
       public class ChangeImageActivity extends AppCompatActivity {
       
           int p = 1;
           @Override
           protected void onCreate(Bundle savedInstanceState) {
               super.onCreate(savedInstanceState);
               setContentView(R.layout.activity_change_image);
       
               Button imgBtn = (Button)findViewById(R.id.img_btn);
               final ImageView iv = (ImageView)findViewById(R.id.image);
       
               imgBtn.setOnClickListener(new View.OnClickListener() {
                   @Override
                   public void onClick(View view) {
                       iv.setImageResource(R.drawable.now);
                   }
               });
           }
       }
       ```

       

5. TouchEventToastActivity

   - 

6. SwipeActivity

   - 

7. FruitSelectActivity

8.  ANR

   - ANR (Application Not Responding)

     ```java
             count_btn.setOnClickListener(new View.OnClickListener() {
                 @Override
                 public void onClick(View view) {
                     // Loop를 돌면서 누적합을 TextView (countTv)에  출력함
                     long sum = 0;
                     for (long i = 0; i<1000000000L; i++){
                         sum += i;
                     }
                     countTv.setText("총합은 " + sum);
     
                 }
             });
     ```

     

   - UI Thread (= Main Thread) 가 사용자의 입력을 5초 이상 받지 못할 때 안드로이드 시스템에서 Dialog (종료 / 대기) 를 띄움

     => 별도의 Thread로 수행하도록 하면 UI Thread가 다른 업무를 담당할 수 있다.

     

   - Activity 단에서 네트워킹 등의 시간이 오래 걸리는 작업을 수행하지 않도로 한다. 

     ==> 해결 : 별도의 Thread를 통하여 오래 걸리는 작업을 수행하도록 한다. 

     1. Thread도 자바 객체 이기 때문에, Thread 객체를 생성하기 위한 Class 를 작성한다. 

        1. Thread 클래스를 상속

           ```java
           class MySumThread extends Thread{
               
           }
           ```

           -> 상속이 가지는 단점으로 인해 거의 사용하지 않는다.

        2. Runnable Interface를 구현

           ```java
           class MySumThread implements Runnable{
               private TextView tv;
           
               // Injection : 객체, 클래스 간의 연결 구조와 상관없이 프로그램을 유연하게 끌고 갈 수 있다.
               // Runnable 을 구현한 객체에 TextView를 넣어서, 해당 TextView를 사용할 수 있도록 한다.
               public MySumThread(TextView tv) {
                   this.tv = tv;
               }
           
               @Override
               public void run() {
                   // Loop를 돌면서 누적합을 TextView (countTv)에  출력함
                   long sum = 0;
                   for (long i = 0; i<1000000000L; i++){
                       sum += i;
                   }
                   tv.setText("총합은 " + sum);
           
               }
           }
           ```

           

     

     ** android.view.ViewRootImpl$CalledFromWrongThreadException: Only the original thread that created a view hierarchy can touch its views.

     - UI에 대한 화면 제어 (예 -  countTv.setText("총합은 " + sum); ) 는 무조건 Activity (= Main Thread, UI Thread)가 수행해야 한다. 

     





```
android:layout_weight="1"
Button은 text의 너비 만큼 차지하고 나머지는 EditText
```