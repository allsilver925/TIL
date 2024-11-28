## DJango

### manage.ty
* 장고를 띄우거나 장고 DB를 만들거나 모델에서 객체를 만들거나 DB 마이그레이션 할 때 사용하는 소스


### MTV (Model - Template - View)

#### Model
* 마이그레이션 했을 때, DB생성 시 자동적으로 ID필드(Integer Field)가 생성됨
<br>

* 이미지나 파일을 경로로 DB에 넣는 이유?
  - DB에 이미지나 파일을 직접 넣고 불러올 경우 많은 용량과 시간이 듦
  - 실제 이미지와 파일은 각각의 서버에 넣어두고 그 경로만 DB에 저장하고, 클라이언트(html)에서 경로로 불러오는 방식을 사용함
 
<br>

#### View
* ```Feed.objects.all()```는 SQL문 select * from content_feed;과 같은 의미

<br>

* ```Feed.objects.all().order_by('-id')```에서 ```.order_by('-id')```는 id필드의 역순으로 가져온다는 것을 의미함
  - 최신글은 id 순서대로 추가되고, 최신글이 맨위로 올라가야 하기 때문
 
<br>

* render함수에 context 넣어줄때는 Dictionary형태로 넣어주어야 함.
  - 실제로 필요한 형태는 JSON형식인데, Dictionary가 json과 호환이 되기 때문 <br>
    ``` return render(request, "main.html", context=dict(object=object)) ```

<br>

* view.py에서 정의한 클래스는 urls.py에 import하고 패턴에 경로 등록해주어야 사용할 수 있음
  - 이때, post방식으로 전달하는 클래스의 경우에는 경로 마지막 '/'를 빼주어야 함.
 
<br>

* 보통 이미지나 파일은 각각에 해당하는 서버에 저장하지만, 현재 프로젝트에서는 Django의 media라는 폴더에 저장할 것임
  - https://wayhome25.github.io/django/2017/05/10/media-file/ 참고
<br>

* 파일을 저장할 때 사용하는 코드<br>

  ```
  with open(save_path, 'wb+') as destination:
        for chunk in file.chunks():
        destination.write(chunk)
  ```
  - save_path에 저장할 경로를 넣고 file을 넣은 변수를 for문에 넣음

<br>

#### Template
* {{ }}로 template에서 데이터를 view로부터 불러옴
  <br>
  
* template에서의 for문은 Python 코드 변수 뿐만 아니라 html도 같이 적용됨.
  ```
    {% for feed in feeds %}
        <p>{{ feed.content }}</p>  # 이때, <p>도 반복됨
    {% endfor %}
  ```

* Javascript나 jQuery는 화면에서 액션을 취했을때 어떤 동적 반응이 나오도록 해주는 작업
  ```
    $('.CLASS / $ID').click()      # 클래스 일 때는 .class, 아이디일때는 $id
  ```

* Javascript나 jQuery에서는 ;(세미콜론)을 써도되고 안써도 됨. (통일할 것)

* body를 가져오는 방법은 ``` $(document.body)  ```

* ```e.stopPropagation();```은 해당 이벤트가 상위(부모)엘리먼트에게 전달되지 않도록 막아주는 함수
  
* ```e.preventDefault();```은 해당 함수의 고유한 동작이 실행되지 않도록 막아주는 함수
