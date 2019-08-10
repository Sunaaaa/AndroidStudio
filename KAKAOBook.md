

## KAKAO API를 이용한 책 검색 Application

- Daum의 방대한 책에 대한 검색 결과를 전달하는 API 

- 카카오 앱 만들기 

  ![1565230513352](https://user-images.githubusercontent.com/39547788/62701636-c6964480-ba1f-11e9-82df-467bd842d478.png)



- 앱 만들기 완료 후, 키 발급 (해당 REST API 키 사용 예정)

  ![1565265971341](https://user-images.githubusercontent.com/39547788/62819823-89ec5980-bb95-11e9-8fa8-018d5aa56b77.png)

### AndroidManifest.xml

- 인터넷 접속 권한을 부여한다.

  ```xml
  <uses-permission android:name="android.permission.INTERNET"/>
  ```

  

### Application 화면 구성

- 검색 창 (EditText), 검색 수행 버튼 (Button) 과 검색 결과를 표시할 ListView를 생성한다. 

  - activity_main

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout android:layout_height="match_parent"
        android:layout_width="match_parent"
        android:orientation="vertical"
        xmlns:android="http://schemas.android.com/apk/res/android" >
    
        <LinearLayout
            android:orientation="horizontal"
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
            <EditText
                android:id="@+id/keyword_editText"
                android:layout_weight="1"
                android:text="여행"
                android:layout_width="wrap_content"
                android:layout_height="match_parent" />
    
    
            <Button
                android:id="@+id/search_btn"
                android:text="검색"
                android:layout_width="wrap_content"
                android:layout_height="match_parent" />
    
    
        </LinearLayout>
    
        <ListView
            android:id="@+id/book_listView"
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
    
        </ListView>
    
    </LinearLayout>
    
    ```

  

- KAKAOBookDTO 객체의 결과 데이터를 표시할 CustomListView

  - listview_item.xml
  - 도서 표지 썸네일 URL (thumbnail), 도서 제목 (title), 도서 저자 리스트 (authors), 도서 정가 (price)을 표시할 Custom ListView Layout을 생성한다.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
      android:orientation="horizontal" android:layout_width="match_parent"
      android:layout_height="match_parent">
  
      <ImageView
          android:id="@+id/imgView"
          android:scaleType="fitCenter"
          android:adjustViewBounds="true"
          android:layout_margin="20px"
          android:layout_width="wrap_content"
          android:layout_height="match_parent" />
  
      <LinearLayout
          android:layout_weight="1"
          android:orientation="vertical"
          android:layout_width="wrap_content"
          android:layout_height="match_parent">
  
          <TextView
              android:layout_weight="1"
              android:id="@+id/btitleTV"
              android:textSize="50px"
              android:layout_width="match_parent"
              android:layout_height="wrap_content"
              android:typeface="monospace"/>
  
          <TextView
              android:layout_weight="1"
              android:id="@+id/bauthorsTV"
              android:layout_width="match_parent"
              android:layout_height="wrap_content" />
  
          <TextView
              android:layout_weight="1"
              android:id="@+id/bpriceTV"
              android:layout_width="match_parent"
              android:layout_height="wrap_content" />
  
          <TextView
              android:layout_weight="1"
              android:id="@+id/bdatetimeTV"
              android:layout_width="match_parent"
              android:layout_height="wrap_content" />
  
          <!--<TextView-->
              <!--android:layout_weight="1"-->
              <!--android:id="@+id/bcontentsTV"-->
              <!--android:layout_width="match_parent"-->
              <!--android:layout_height="wrap_content" />-->
  
  
      </LinearLayout>
  
  </LinearLayout>
  ```





### Activity 구현 (java 코드) 

- 책 검색 결과를 담을 KAKAOBookDTO Class 생성

- **마샬링  (Marshaling) == <u>Serialiazation</u>**

  - 사전전 의미 : 정비하다, 정리하다, 배열시키다, 결집시키다

  - 서로 간의 데이터 전달을 위해 일정한 형식의 포맷을 맞추어 전달

  - 클라이언트와 서버 간의 데이터 전송 시, 각 도착지점의 데이터 형식에 맞게 내부적으로 변경 

    - 마샬링이 일어나지 않으면, 클라이언트가 사용하는 메모리의 포인터가 유효하지 않고, 잘못된 곳을 가리킨다.

    - 해당 포인터가 클라이언트와 서버 모두에 동일한 공간의 메모리를 할당하여 해당 포인터를 유효하게 사용해야 한다. 

      - 클라이언트 -> 서버

        - 서버 : 클라이언트와 동일한 공간의 메모리 할당하고, 클라이언트에 할당된 메모리를 복제하여 클라잉언트에서 넘어온 포인터 값을 서버에 할당된 공간의 포인터로 바꿔서 전달한다.

        

- 언마샬링

  - 클라이언트에서 마샬링 된 데이터를 서버에 전달하면, 서버에서는 그 데이터

    

- 마샬링이 가능한 클래스를 생성하여 KAKAO Book의 정보를 담는 객체를 생성한다.

  - KAKAOBookDTO (코드 내의 설명 첨부)

    ```java
    package com.example.kakaobookserach;
    
    import android.graphics.Bitmap;
    import android.graphics.BitmapFactory;
    import android.graphics.drawable.BitmapDrawable;
    import android.graphics.drawable.Drawable;
    import android.net.Uri;
    import android.os.Parcel;
    import android.os.Parcelable;
    import android.util.Log;
    
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.io.InputStream;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.util.ArrayList;
    
    // 해당 클래스의 객체가 마샬링(다른 형태로 변형)이 가능하도록 Parcelable 인터페이스를 구현
    public class KAKAOBookDTO implements Parcelable {
        private String title;
        private String contents;
        private String url;
        private String isbn;
        private String datetime;
        private ArrayList<String> authors;
        private String publisher;
        private ArrayList<String> translators;
        private String price;
        private String sale_price;
        private String thumbnail;
        private String status;
        private byte[] thumbnailImg;
    
        // CREATOR라는 Static 상수를 반드시 정의 해야 한다.
        // 소문자 말고 대문자 쓰자
        public static final Creator<KAKAOBookDTO> CREATOR = new Creator<KAKAOBookDTO>() {
            @Override
            public KAKAOBookDTO createFromParcel(Parcel parcel) {
                // 마샬링된 데이터를 언 마샬링 (복원) 할 때 사용되는  Method
                return new KAKAOBookDTO(parcel);
            }
    
            @Override
            // 총 몇개를 복원할 것인지
            public KAKAOBookDTO[] newArray(int i) {
                return new KAKAOBookDTO[i];
            }
        };
    
        // Default 생성자
        public KAKAOBookDTO() {
        }
    
        // Un 마샬링 (복원 작업)할 때 사용되는 생성자
        //*** 복원시 중요 ***
        // 마샬링 순서와 Un마샬링 순서가 동일해야 한다.
        // 마샬링한 데이터의 순서와 동일한 순서로 Un마샬링을 수행해야한다.
    
    
        // 모든 field를 인자로 받는 생성자자
        public KAKAOBookDTO(ArrayList<String> authors, String contents, String datetime, String isbn, String price, String publisher, String sale_price, String status, String thumbnail, String title, ArrayList<String> translators, String url) {
            this.authors = authors;
            this.contents = contents;
            this.datetime = datetime;
            this.isbn = isbn;
            this.price = price;
            this.publisher = publisher;
            this.sale_price = sale_price;
            this.status = status;
            this.thumbnail = thumbnail;
            this.title = title;
            this.translators = translators;
            this.url = url;
        }
    
    
        protected KAKAOBookDTO(Parcel parcel) {
            // 복원시 제일 신경써야 하는것인 순서
            // 마샬링 순서와 언마샬링 순서가 같아야 합니다.
            authors = parcel.readArrayList(null);
            contents = parcel.readString();
            datetime = parcel.readString();
            isbn = parcel.readString();
            price = parcel.readString();
            publisher = parcel.readString();
            sale_price = parcel.readString();
            status = parcel.readString();
            thumbnail = parcel.readString();
            title = parcel.readString();
            translators = parcel.readArrayList(null);
            url = parcel.readString();
            thumbnailImg = parcel.createByteArray();
        }
    
        @Override
        public int describeContents() {
            return 0;
        }
    
        @Override
        // 마샬링 (데이터를 변환) 시켜주는 역할을 하는 Method - 클래스의 모든 멤버 변수를 변환
        public void writeToParcel(Parcel parcel, int i) {
            try {
                parcel.writeList(authors);
                parcel.writeString(contents);
                parcel.writeString(datetime);
                parcel.writeString(isbn);
                parcel.writeString(price);
                parcel.writeString(publisher);
                parcel.writeString(sale_price);
                parcel.writeString(status);
                parcel.writeString(thumbnail);
                parcel.writeString(title);
                parcel.writeList(translators);
                parcel.writeString(url);
                parcel.writeByteArray(thumbnailImg);
            } catch (Exception e) {
                Log.i("LOG_KAKAOBOOK", "BookVO error : " + e.toString());
            }
        }
    
        //
        // 멤버 변수들의 Getter & Setter
        //
        public void urlToByteArray() {
            Bitmap bmp = null;
            try {
                URL imgUrl = new URL(thumbnail);
                Log.i("LOG_KAKAOBOOK", "썸네일 : " + thumbnail);
                HttpURLConnection con = (HttpURLConnection) imgUrl.openConnection();
                con.setDoInput(true);
                con.connect();
                InputStream inputStream = con.getInputStream();
                bmp = BitmapFactory.decodeStream(inputStream);
                ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
                bmp.compress(Bitmap.CompressFormat.JPEG, 100, byteArrayOutputStream);
                thumbnailImg = byteArrayOutputStream.toByteArray();
    
            } catch (Exception e) {
                Log.i("LOG_KAKAOBOOK", e.toString());
            }
        }
    
    }
    
    ```



### Service 구현













#### Jackson Library를 이용해서 데이터를 처리 

- Jackson Library Add 하기 

  ![1565263114619](https://user-images.githubusercontent.com/39547788/62701648-cd24bc00-ba1f-11e9-8df3-7f8ce653ee37.png)

  

- KAKAOBookSerachService

  - String으로 가져온 데이터를 JSON으로 변형한다. 

    - 변형 결과 데이터 형태 : { document : [실제필요한 데이터] }

    - Key-Value의 형태로 데이터를 가져온다. 

      - Key값 : document
      - Value값 : [실제 필요한 데이터]

      ```java
      ObjectMapper mapper = new ObjectMapper();
      
      // Map<String, Object> 구조로 데이터 변환
      Map<String, Object> map = mapper.readValue(sb.toString(), new TypeReference<Map<String, Object>>(){} );
      ```

    

  - Map 구조로 되어있는 데이터에서 [실제 필요한 데이터]를 JSON 구조로 변형하여 가져온다.

    - 반환된 데이터 형태 : [{책 1}, {책 2}, {책 3}, {책 4}, ... ]

    - [실제 필요한 데이터]가 Tree 구조로 반환된다. 

      ```java
      // [실제 필요한 데이터]를 추출한다.
      Object obj = map.get("documents");
      // JSON 형태로 데이터 변환
      String resultJsonData = mapper.writeValueAsString(obj);
      ```

    

  - 결과로 반환된 모든 책에 대하여 각 책 데이터를 객체화 한다. 

    - KAKAOBookDTO 클래스를 이용하여 데이터의 객체화를 수행한다.

    - 반환된 데이터 형태 : [{책 1}, {책 2}, {책 3}, {책 4}, ... ]

    - ArrayList의 형태로 여러 개의 책 데이터를 담는다.

      ```java
      // JSON 형태를 ArrayList구조로 변형하여 가져온다.
      ArrayList<KAKAOBookDTO> myObject = mapper.readValue(resultJsonData, new TypeReference<ArrayList<KAKAOBookDTO>>() {});
      ```





#### KAKAOAPI 활용하기 

- Request

  ```java
  curl -v -X GET "https://dapi.kakao.com/v3/search/book?target=title" \
  --data-urlencode "query=미움받을 용기" \
  -H "Authorization: KakaoAK kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkk"
  ```

  - GET : GET 방식 

  - "https://dapi.kakao.com/v3/search/book?target=title" : 네트워크 URL

  - target : 검색 필드 제한

    - title : 제목에서 검색
    - isbn : isbn에서 검색
    - publisher : 출판사에서 검색
    - person : 인명에서 검색

  - query : 검색을 원하는 질의어

  - -H : 헤더 

  - "Authorization: KakaoAK kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkk" : kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkk에 인증키 

  - 코드로 구현

    ```java
        private class BookSearchRunnable implements Runnable{
    
            @Override
            public void run() {
                // 전달된 키워드를 이용해서 네트워크 처리함 (API 호출)
                String  url = "https://dapi.kakao.com/v3/search/book?target=title&query=" + keyword;
                String mykey = "REST API Key 값";
    
                try {
                    // 네트워크 연결
                    URL urlObj = new URL(url);
                    HttpURLConnection con = (HttpURLConnection)urlObj.openConnection();
    
                    // request 방식을 지정
                    con.setRequestMethod("GET");
                    con.setRequestProperty("Authorization", "KakaoAK " + mykey);
    
        .
        .
        .
            
    }
    ```

  

- CustomListView (listview_item.xml) 에 Book에 대한 결과 데이터를 출력하기 위해 BaseAdapter를 상속받은 CustomAdapter를 생성한다. 

  - KAKAOListViewAdapter

    - Override Method

      - getCount() : 결과 데이터의 총 개수 

        ```java
            @Override
            public int getCount() {
                return list.size();
            }
        ```

        

      - getItem(int i) : ListViewAdapter에 연결된 ArrayList의 특정 위치 (i)에 있는 Item을 가져온다. 

        ```java
            @Override
            public Object getItem(int i) {
                return list.get(i);
            }
        ```

        

      - getItemId(int i) : getItem(int i)으로 가져오는 Item의 Id 값을 설정해준다. 

        ++ Item의 삭제, 검색 등의 용의성을 위해 Id값을 부여한다.

        ++ ItemListener에서 선택된 Item의 Id 값을 반환하는데 사용할듯 

        ```java
            @Override
            public long getItemId(int i) {
                return i;
            }
        ```

        

      - getView (int i, View view, ViewGroup viewGroup) : listview_item의 Layout으로 출력될 데이터의 View를 반환한다. 

        ```java
        @Override
        // i : 각각의 List 들
        public View getView(int i, View view, ViewGroup viewGroup) {
        
            Bitmap bitmap;
            final Context context = viewGroup.getContext();
        
            // 출력할 View 객체를 생성
            if (view == null) {
                LayoutInflater inflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        
                // 제작한 Custom 형태의 xml의 형태로 view 객체를 구성하게 된다.
                // 생성한 View 객체에 XML Layout을 설정
                view = inflater.inflate(R.layout.listview_item, viewGroup, false);
            }
        
            // 출력할 View Component Reference 획득
            ImageView imageView = (ImageView) view.findViewById(R.id.imgView);
            TextView btitleTV = (TextView) view.findViewById(R.id.btitleTV);
            TextView bauthorsTV = (TextView) view.findViewById(R.id.bauthorsTV);
            TextView bpriceTV = (TextView) view.findViewById(R.id.bpriceTV);
            TextView bdatetimeTV = (TextView) view.findViewById(R.id.bdatetimeTV);
            //       TextView bcontentsTV = (TextView) view.findViewById(R.id.bcontentsTV);
        
            // 화면에 출력할 데이터를 가져와요
            KAKAOBookDTO dto = list.get(i);
            Log.i("DTO", dto.toString());
        
        
            try {
        
                Bitmap bmp = BitmapFactory.decodeByteArray(dto.getThumbnailImg(),
                                                           0, dto.getThumbnailImg().length);
                imageView.setImageBitmap(bmp);
        
        
                StringBuilder sb = new StringBuilder();
                for (String s : dto.getAuthors()) {
                    sb.append(s);
                    sb.append(", ");
                }
        
                bauthorsTV.setText(sb.substring(0,sb.length()-1));
                btitleTV.setText(dto.getTitle());
                bpriceTV.setText("책 가격 : " + dto.getPrice() + "원");
        
                bdatetimeTV.setText(dto.getDatetime().substring(0, 10));
                //           bcontentsTV.setText(dto.getContents());
        
            } catch (Exception e) {
                Log.i("LOG_KAKAOBOOK_ERROR", e.toString());
            }
            return view;
        }
        
        ```

        - i : 각각의 List 내의 값

        - view : 출력할 view 객체 

        - viewGroup : 여러 개의 view 또는 ViewGroup을 포함하는 컨테이너 

        - 이미지 처리 

          - BitmapFactory.decodeByteArray()

            ```java
            Bitmap bmp = BitmapFactory.decodeByteArray(dto.getThumbnailImg(),
                                                               0, dto.getThumbnailImg().length);
                    imageView.setImageBitmap(bmp);
            ```

            





#### 이미지 처리 (Bitmap)

- Bitmap을 DB에 저장하기 위해서는 bytep[ ] 형식으로 변환해야 한다. 

- 주요 Method

  - ByteArrayOutputStream : 내부적으로 저장공간이 존재, 출력되는 모든 내용들이 내부적인 저장공간에 쌓인다.

     ( ByteArrayInputStream : 바이트 배열을 차례대로 읽어 들이는 클래스 )

  - toByteArray() : 저장된 모든 내용이 Byte[ ] 형태로 반환된다. 

  - compress(Bitmap.CompressFormat format, int quality, OutputStream stream) : 

    - 인자로 제공되는 출력 스트림을 통해 Bitmap을 압축하여 Bitmap을 재구성한다.

      - Bitmap.CompressFormat : 압축할 수 있는 형식을 지정

        - JPEG, PNG, WEBP

      - quality : 압축기에 대한 힌트 

        - 0 : 작은 크기의 압축

        - 100 : 최대 품질의 압축

          ** PNG 형식은 무손실 이므로 품질 설정을 무시한다.

      - stream : 압축된 데이터를 쓰는 출력 스트림

  - decodeStream(InputStream inputStream) : 입력 스트림을 Bitmap으로 디코딩한다.

  

- Bitmap -> Byte[ ]

  - 인자로 제공하는 Bitmap 객체를 Byte[ ]로 재구성한다.

    ```java
    public byte[] bitmapToByArray(Bitmap bitmap){
        ByteArrayOutputStream stream = new ByteArrayOutputStream();
        
        // JPEG 형식으로 파일 변환
        bitmap.compress(CompressFormat.JPEG, 100, stream);
        byte[] byteArray = stream.toByteArray();
        return byteArray;
    }
    ```

  

- URL-> Byte[ ]

  - 네트워크를 통하여 가져온 url을 Byte[ ]로 재구성한다.

  - KAKAOBookDTO

    ```java
    // DTO의 method 
    public void urlToByteArray() {
        Bitmap bmp = null;
        try {
            // thumbnail : DTO의 이미지 url 데이터를 갖는 변수
            // url을 얻어 Bitmap으로 변형
            URL imgUrl = new URL(thumbnail);
            HttpURLConnection con = (HttpURLConnection) imgUrl.openConnection();
            con.setDoInput(true);
            con.connect();
            InputStream inputStream = con.getInputStream();
            bmp = BitmapFactory.decodeStream(inputStream);
    
            // Bitmap을 byte[ ]형태로 변형
            ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
            bmp.compress(Bitmap.CompressFormat.JPEG, 100, byteArrayOutputStream);
            thumbnailImg = byteArrayOutputStream.toByteArray();
    
        } catch (Exception e) {
            Log.i("LOG_KAKAOBOOK", e.toString());
        }
    }
    ```

  

  - KAKAOBookSearchService

    - 모든 책에 대해 Url을 byte[]으로 이미지 처리를 수행한다.

    ```java
    for (KAKAOBookDTO dto : myObject){
        dto.urlToByteArray();
    }
    ```





#### Intent를 활용하여 결과 데이터를 Handling 하기

- 정상적으로 객체화가 된 결과 데이터를 Intent를 통해 특정 Activity에게 전달한다.

  - Intent를 설정한다.

    ```java
    Intent i = new Intent(getApplicationContext(), MainActivity.class);
                   i.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);         i.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    i.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    ```

  - 전달할 데이터를 Intent에 붙인다.

    - Parcelable 인터페이스를 구현한 객체를 붙이기 위해서 putExtra() 가 아닌 putParcelableArrayListExtra()를 사용한다.

      ```java
      i.putParcelableArrayListExtra("resultData", myObject);
      
      startActivity(i);
      ```

  - Intent를 담아 특정 Activity를 호출한다.

    ```java
    startActivity(i);
    ```

    



#### EditText Style

- EditText (keyword_editText) 의 커서 위치 

  - 검색이 완료되어, ListView에 결과 데이터를 출력하면, EditText의 커서 위치가 해당 EditText 내의 검색어 맨 끝으로 위치하도록 한다. 

    ```java
    editText.setSelection(editText.length());
    ```

    

    ![1565263114619](https://user-images.githubusercontent.com/39547788/62701672-d44bca00-ba1f-11e9-9680-13bbbcc23eba.png)







#### EditText Style

- Button을 그린 뒤 나머지 부분을 EditText 가 차지한다.![1565227306631](https://user-images.githubusercontent.com/39547788/62819828-a4263780-bb95-11e9-9244-a2ace99e4806.png)





#### 키보드 조작

- 키보드 숨기기 

  - 검색이 완료되어, ListView에 결과 데이터를 출력하면, 키보드를 숨긴다.

  - 첫번 째 인자로 키보드 입력 포커스에 해당되는 EditText의 객체 변수의 getWindowToken()을 넣어준다.

  - 두번 째 인자로 별도의 Flag가 없기 때문에 0을 넣어준다.

    ```java
    InputMethodManager imm = (InputMethodManager) getSystemService(INPUT_METHOD_SERVICE);
    
    // 키보드의 입력 포커스를 맞출 EditText 객체 변수 선언
    EditText editText = (EditText)findViewById(R.id.keyword_editText);
    
    
    imm.hideSoftInputFromWindow(editText.getWindowToken(), 0);
    ```

- 키보드 보이기

  - 키보드를 화면에 나타낸다.

  - 첫번 째 인자로 키보드 입력 포커스가 수행될 EditText의 객체 변수를 넣어준다.

  - 두번 째 인자로 별도의 Flag가 없기 때문에 0을 넣어준다.

    ```java
    InputMethodManager imm = (InputMethodManager) getSystemService(INPUT_METHOD_SERVICE);
    
    // 키보드의 입력 포커스를 맞출 EditText 객체 변수 선언
    EditText editText = (EditText)findViewById(R.id.keyword_editText);
    
    imm.showSoftInput(editText, 0);
    ```

- AndroidManifest.xml에서 제어

  - Activity 시작 시에 자동으로 소프트키보드를 보이거나 숨길 수 있다. 

    ```xml
    <activity android:name=".MainActivity" android:windowSoftInputMode="stateAlwaysVisible"/>
    <activity android:name=".MainActivity" android:windowSoftInputMode="stateAlwaysHidden"/>
    ```



#### ViewGroup 

- 여러 개의 view 또는 ViewGroup을 포함하는 컨테이너 



![1565416153536](https://user-images.githubusercontent.com/39547788/62819832-b011f980-bb95-11e9-9f7b-496ce079a160.png)





#### Context 

- 현재  Activity
- 어떤 일이 발생한 상황

- Application이나 객체의 현재 상태 정보에 대한 클래스 

- UI 구성 요소인 View에 대한 정보를 손쉽게 확인하거나 설정할 수 있도록 View의 생성자에 Context 객체를 전달한다.  ( 안드로이드의 모든 UI 객체들은 Context 객체를 전달 받음)

- Context를 받는 방법 

  1. getApplicationContext()

  2. getContext()

  3. getBaseContext()

  4. [ Activity명 ].this

  5. this

     

  - Context를 상속받은 클래스는 this 를 Context 객체로 사용할 수 있다.
  - Context를 상속받지 않은 클래스는 getApplicationContexet() Method를 통해 Context 객체를 사용한다.

  

- Context 사용 예 

  1. View, Adapter, Listener과 같은 새로운 객체 생성 시 

     ```java
     ArrayAdapter adapter = new ArrayAdapter(getApplicationContext(), android.R.layout.simple_spinner_dropdown_item, list);
     ```

     ```java
     Toast.makeText(getApplicationContext(), R.string.toastMsg, Toast.LENGTH_SHORT).show();
     ```

     

  2.  Standard Common Resources에 접근할때

     ```java
     final Context context = viewGroup.getContext();
     ```

     ```java
     LayoutInflater inflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
     ```

     

  3. content provider, broadcasts, intent에서 사용 시

     ```java
     MySQLiteHelper helper = new MySQLiteHelper(getContext());
     ```

     ```java
     private BroadcastReceiver receiver;
     
     receiver = new BroadcastReceiver() {
         @Override
         public void onReceive(Context context, Intent intent) {
             //  Broadcast 를 잡았을 때 처리해야할 코드 작성
         }
     }
     ```

     ```java
     Intent nIntent = new Intent(context,BroadcastTestActivity.class);
     ```

     
