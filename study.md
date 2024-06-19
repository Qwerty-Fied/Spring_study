# 6장
링크 : 미리 정해놓은 요청을 간편하게 전송하는 기능
리다이렉트 : A에게 전화 -> 이 일은 B 담당이니 B에게 전화해라 -> B에게 전화
            클라이언트가 보낸 요청을 마치고, 계속해서 처리할 다음 요청주소를 재지시하는 것
            분리된 기능을 하나의 연속적인 흐름처럼 연결할 수 있다 ( 클라 -> A -> 클라에게 B로 가라 전송 -> 클라 -> B )

그냥 articles로 들어감 -> 아무것도 없다, Submit 버튼은 있는데 뒤로가기버튼은 없다

<a href="url"></a>을 통해 페이지를 이동할 수 있는 버튼을 만들 수 있다.

입력하면 상세 페이지로 이동한다
메서드에 return "redirect:URL_주소";
뒤에 + 연산자를 통해서 전에 만들어뒀던 Article 객체 saved를 이용
saved 객체 내 .getId()를 호출해 사용할 수 있다. (Article 클래스에 롬복 @Getter를 추가한다
Title과 Content가 입력되면서 만들어진 Id를 가져와 "/articles/1" 로 리다이렉션된다.

index.mustache 의 테이블에 동적으로 생성되는 ArticleList에도 a 태그에 href를 추가해 링크가 가능하게 할 수 있다.

# 7장 수정 페이지 만들기
<a href="/articles/{{article.id}}/edit">Edit</a>
위처럼 article의 사용범위를 지정하지 않고 {{article.id}} 를  할 수 도 있고
{{#article}}
로 사용범위를 지정해서 사용할 수 도 있다 (이 경우 아래에서 {{id}}로 바로 접근할 수 있다.

{{>layouts/header}}
<form class="container" action="" method="post">
    <a href="/articles/{{article.id}}">Back</a>
</form>
{{>layouts/footer}}
와 같은 형식으로 수정할 페이지를 만들 수 있다. 지금 단계에서는 수정할 title, content가 넘어오지 않는다
<input type="text" class="form-control" name="title" value="{{title}}">
<textarea class="form-control" rows="3"
          name="content">{{content}}</textarea>

# 7-3 수정 데이터를 DB에 갱신하기
폼데이터를 클라가 던짐(HTTP) -> DTO를 엔티티로 변환함(MVC) -> DB 갱신(JPA)
클라와 서버간의 데이터 전송시 프로토콜을 따른다
프로토콜 - 컴퓨터간 통신을 위한 전세계 표준
FTP, SMTP, HTTP 등 다양함 -> 그중 HTTP는 웹 서비스를 위한 프로토콜
다양한 클라의 요청을 메서드를 통해 서버로 보냄
HTTP 대표 메서드 : POST(생성), GET(조회), PACTH(PUT)(수정), DELETE(삭제) -> CRUD

더미데이터를 생성해 매번 데이터 입력을 안하게 하는법
data.sql 파일을 만들어 insert문을 넣어놓는다

application.properties 파일에
spring.jpa.defer-datasource-initialization=true를 입력해준다

form 태그에 method로 get이나 post만 쓰는 이유
form 태그가 옛날 규격이라 PATCH로 하면 지원을 안함

데이터를 DTO에 담아 DB에 저장하려면
1. DTO를 엔티티로 변환 2. 엔티티를 DB에 저장 3. 수정 결과 페이지로 리다이렉션

1. toEntity()를 통해 DTO를 엔티티로 변환 (Article에 정의됨)

2. 변경할 데이터를 불러와 널을 확인하고 업데이트 한다
Article target = articleRepository.findById(articleEntity.getId()).orElse(null);
        if (target != null) {
            articleRepository.save(articleEntity)
        }

# 8 데이터 삭제
Redirect Attributes -> 리다이렉트 페이지에서 사용할 데이터를 남길 수 있다
                       리다이렉트 시점에서 한번만 사용할 데이터를 등록할 수 있다 (날아감)
                       객체이름.addFlashAttribute(키,객체)

삭제할때니 Delete를 쓴다
DB에 접근 후 데이터 처리 -> JPA 사용
id를 기반으로 데이터를 찾아, 있으면 target변수에 저장, 없으면 null
articleRepository.delete()에 엔티티를 인자로 넘겨 해당 값을 삭제한다
