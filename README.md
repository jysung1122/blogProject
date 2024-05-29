# blogProject
- React + Spring boot + MySQL로 블로그 개발

### 개발환경
- Macbook M1 2020
- IDE : VSCODE
- mysql  Ver 8.3.0 for macos14.2 on arm64 (Homebrew)
```
❯ java --version
openjdk 21.0.3 2024-04-16 LTS
OpenJDK Runtime Environment Zulu21.34+19-CA (build 21.0.3+9-LTS)
OpenJDK 64-Bit Server VM Zulu21.34+19-CA (build 21.0.3+9-LTS, mixed mode, sharing)
❯ node --version
v18.20.3
❯ npm --version
10.7.0
❯ npx --version
10.7.0
```

# 1. REST API 명세서 작성
[Notion으로 정리](https://capable-slime-584.notion.site/Yongs-Board-REST-API-93305c1c5f464cb990305dc039314c98?pvs=4)

## Rest란
- Representational State Transfer
- REST는 인터넷의 기본 프로토콜인 HTTP를 사용하여 서버 상의 자원을 처리하는 아키텍처 스타일.
- 구성요소
  - Resource: 웹에서 접근 가능한 데이터나 서비스의 한 부분을 의미. 각 리소스는 고유한 URI(Uniform Resource Identifier)로 식별. 사용자 정보, 문서, 이미지 등이 리소스가 될 수 있음.
  - Method: REST는 주로 HTTP 메소드를 사용하여 리소스를 조작. 이에는 다음과 같은 메소드가 포함됨
    - GET: 리소스를 조회할 때 사용
    - POST: 새로운 리소스를 생성할 때 사용
    - PUT: 리소스를 업데이트할 때 사용
    - DELETE: 리소스를 삭제할 때 사용
  - Status Code: HTTP 응답의 상태를 알려 주는 코드. 예를 들어, 200 OK는 성공적인 요청을, 404 Not Found는 요청한 리소스가 서버에 없음을 나타냄.
  - Representation: 클라이언트와 서버 사이에 데이터를 교환할 때, 이 데이터는 JSON, XML 등의 형식으로 표현됨. 클라이언트는 API를 통해 데이터를 요청하고 응답 받은 데이터를 사용하여 애플리케이션에서 필요한 처리를 수행할 수 있음.

## API 란
- Application Programming Interface
- 서로 다른 소프트웨어 시스템이나 애플리케이션 간에 상호작용하는 방법을 정의하는 규칙이나 프로토콜.
- 한 프로그램이 다른 프로그램의 기능이나 데이터를 사용할 수 있도록 해주는 다리와 같은 역할.

# 2. ERD / DDL / DCL 작성
- vscode 확장 프로그램인 ERD Editor (dineug: erd-editor.io)과 MySQL(Weijan Chen: database-client.com) 설치하여 진행
- board.vuerd.json
- ✅ 오타: border_number --> board_number 로 수정!!! ✅

<img width="1440" alt="스크린샷 2024-05-28 오전 10 42 20" src="https://github.com/jysung1122/aiModel/assets/56614779/fedfbaba-e4c3-460e-b939-79717d4c47a7">
<img width="1440" alt="스크린샷 2024-05-28 오전 10 42 35" src="https://github.com/jysung1122/aiModel/assets/56614779/29223255-7109-4872-bee4-ab17408125fd">
<img width="1440" alt="스크린샷 2024-05-28 오전 10 42 42" src="https://github.com/jysung1122/aiModel/assets/56614779/704fd9eb-26af-4a90-9ebb-a4586dbf8ddc">

- board DB 생성 후 DDL Query 생성
<img width="299" alt="스크린샷 2024-05-28 오전 10 47 19" src="https://github.com/jysung1122/aiModel/assets/56614779/772e411e-3f3f-4e4d-89aa-335841280600">

- DDL 코드 작성 후 하나하나 실행
  ```
  -- Active: 1716358723865@@127.0.0.1@3306@board  MYSQL

  CREATE TABLE board
  (
    board_number  INT         NOT NULL AUTO_INCREMENT COMMENT '게시물 번호',
    title          TEXT        NOT NULL COMMENT '게시물 제목',
    content        TEXT        NOT NULL COMMENT '게시물 내용',
    write_datetime DATETIME    NOT NULL COMMENT '게시물 작성 날짜 및 시간',
    favorite_count INT         NOT NULL DEFAULT 0 COMMENT '게시물 좋아요 수',
    comment_count  INT         NOT NULL DEFAULT 0 COMMENT '게시물 댓글 수',
    view_count     VARCHAR(50) NOT NULL DEFAULT 0 COMMENT '게시물 조회 수',
    writer_email   VARCHAR(50) NOT NULL COMMENT '게시물 작성자 이메일',
    PRIMARY KEY (board_number)
  ) COMMENT '게시물 테이블';
  
  CREATE TABLE comment
  (
    comment_number INT         NOT NULL COMMENT '댓글 번호',
    content        TEXT        NOT NULL COMMENT '댓글 내용',
    write_datetime DATETIME    NOT NULL COMMENT '작성 날짜 및 시간',
    user_email     VARCHAR(50) NOT NULL COMMENT '사용자 이메일',
    board_number  INT         NOT NULL COMMENT '게시물 번호',
    PRIMARY KEY (comment_number)
  ) COMMENT '댓글 테이블';
  
  CREATE TABLE favorite
  (
    user_email    VARCHAR(50) NOT NULL COMMENT '사용자 이메일',
    board_number INT         NOT NULL COMMENT '게시물 번호',
    PRIMARY KEY (user_email, board_number)
  ) COMMENT '좋아요 테이블';
  
  CREATE TABLE image
  (
    board_number INT  NOT NULL COMMENT '게시물 번호',
    image         TEXT NOT NULL COMMENT '게시물 이미지 URL'
  ) COMMENT '게시물 이미지 테이블';
  
  CREATE TABLE search_log
  (
    sequence      INT     NOT NULL AUTO_INCREMENT COMMENT '시퀀스',
    search_word   TEXT    NOT NULL COMMENT '검색어',
    relation_word TEXT    NULL     COMMENT '관련 검색어',
    relation      BOOLEAN NOT NULL COMMENT '관련 검색어 여부',
    PRIMARY KEY (sequence)
  ) COMMENT '검색 기록 테이블';
  
  CREATE TABLE user
  (
    email          VARCHAR(50)  NOT NULL COMMENT '사용자 이메일',
    password       VARCHAR(100) NOT NULL COMMENT '사용자 비밀번호',
    nickname       VARCHAR(20)  NOT NULL UNIQUE COMMENT '사용자 닉네임',
    tel_number     VARCHAR(15)  NOT NULL UNIQUE COMMENT '사용자 휴대전화번호',
    address        TEXT         NOT NULL COMMENT '사용자 주소',
    address_detail TEXT         NULL     COMMENT '사용자 상세 주소',
    profile_image  TEXT         NULL     COMMENT '사용자 프로필 사진 URL',
    PRIMARY KEY (email)
  ) COMMENT '사용자 테이블';
  
  ALTER TABLE image
    ADD CONSTRAINT FK_board_TO_image
      FOREIGN KEY (board_number)
      REFERENCES board (board_number);
  
  ALTER TABLE board
    ADD CONSTRAINT FK_user_TO_board
      FOREIGN KEY (writer_email)
      REFERENCES user (email);
  
  ALTER TABLE favorite
    ADD CONSTRAINT FK_user_TO_favorite
      FOREIGN KEY (user_email)
      REFERENCES user (email);
  
  ALTER TABLE favorite
    ADD CONSTRAINT FK_board_TO_favorite
      FOREIGN KEY (board_number)
      REFERENCES board (board_number);
  
  ALTER TABLE comment
    ADD CONSTRAINT FK_user_TO_comment
      FOREIGN KEY (user_email)
      REFERENCES user (email);
  
  ALTER TABLE comment
    ADD CONSTRAINT FK_board_TO_comment
      FOREIGN KEY (board_number)
      REFERENCES board (board_number);
  
  CREATE USER 'developer'@'%' IDENTIFIED BY 'P!ssw0rd';
  
  -- 뷰 생성
  CREATE View board_list_view AS
  SELECT
      B.board_number AS board_number,
      B.title AS title,
      B.content AS content,
      I.image AS title_image,
      B.favorite_count AS favorite_count,
      B.comment_count AS comment_count,
      B.view_count AS view_count,
      B.write_datetime AS write_datetime,
      B.writer_email AS writer_email,
      U.nickname AS writer_nickname,
      U.profile_image AS writer_profile_image
  FROM board AS B
  INNER JOIN user AS U
  ON B.writer_email = U.email
  LEFT JOIN (SELECT board_number, ANY_VALUE(image) AS image FROM image GROUP BY board_number) AS I -- 중복 제거 하기 위해 그룹 함수 사용
  ON B.board_number = I.board_number
  ```
- DCL Query 생성 후 실행
  ```
  -- Active: 1716358723865@@127.0.0.1@3306@board  MYSQL

  GRANT ALL PRIVILEGES ON board.* TO 'developer'@'%';

  ```
- DML Query 생성 후 실행 (하나하나 실행해가며 결과 보는거 추천)
  ```
  -- Active: 1716358723865@@127.0.0.1@3306@board  MYSQL
  -- 회원가입
  INSERT INTO
  user VALUES
  ('email@email.com', 'P!ssw0rd','nickname', '01012345678', '대전광역시 서구', '갤러리아백화점', null);
  
  -- 로그인
  SELECT * FROM user WHERE email = 'email@email.com';
  
  -- 게시물 작성
  INSERT INTO
  board (title, content, write_datetime, favorite_count, comment_count, view_count, writer_email)
  VALUES ('제목입니다.', '내용입니다.', '2024-05-28 11:31', 0, 0, 0, 'email@email.com');
  
  INSERT INTO
  image VALUES (1, 'url');
  
  -- 댓글 작성
  INSERT INTO
  comment (content, write_datetime, user_email, board_number)
  VALUES ('반갑습니다.', '2024-05-28 11:37', 'email@email.com', 1);
  
  UPDATE board SET comment_count = comment_count + 1 WHERE board_number = 1; -- 중복을 가능하게 해서 + 1 해야함
  
  -- 좋아요
  INSERT INTO
  favorite VALUES ('email@email.com', 1);
  
  UPDATE board SET favorite_count = favorite_count + 1 WHERE board_number = 1; -- 중복을 가능하게 해서 + 1 해야함
  
  DELETE FROM favorite WHERE user_email = 'email@email.com' AND board_number = 1;
  
  UPDATE board SET favorite_count = favorite_count - 1 WHERE board_number = 1;
  
  -- 게시물 수정
  UPDATE board SET title = '제목이 수정되었습니다.', content = '내용이 수정되었습니다.' WHERE board_number = 1;
  
  DELETE FROM image WHERE board_number = 1;
  
  INSERT INTO image VALUES (1, 'url');
  
  -- 게시물 삭제
  DELETE FROM comment WHERE board_number = 1;
  DELETE FROM favorite WHERE board_number = 1;
  DELETE FROM image WHERE board_number = 1;
  DELETE FROM board WHERE board_number = 1;
  
  -- 상세 게시물 불러오기
  SELECT
      B.board_number AS board_number,
      B.title AS title,
      B.content AS content,
      B.write_datetime AS write_datetime,
      B.writer_email AS writer_email,
      U.nickname AS nickname,
      U.profile_image AS profile_image
  FROM board AS B
  INNER JOIN user AS U
  ON B.writer_email = U.email
  WHERE board_number = 1;
  
  SELECT image
  FROM image
  WHERE board_number = 1;
  
  SELECT
      U.email AS email,
      U.nickname AS nickname,
      U.profile_image
  FROM favorite AS F
  INNER JOIN user AS U
  ON F.user_email = U.email
  WHERE F.board_number = 1;
  
  SELECT
      U.nickname AS nickname,
      U.profile_image AS profile_image,
      C.write_datetime AS write_datetime,
      C.content AS content
  FROM comment AS C
  INNER JOIN user AS U
  ON C.user_email = U.email
  WHERE C.board_number = 1
  ORDER BY write_datetime DESC;
  
  -- 최신 게시물 리스트 불러오기
  SELECT *
  FROM board_list_view
  ORDER BY write_datetime DESC
  LIMIT 0, 5;
  
  -- 검색어 리스트 불러오기
  SELECT *
  FROM board_list_view
  WHERE title LIKE '%수정%' OR content LIKE '%수정%'
  ORDER BY write_datetime DESC
  LIMIT 0, 5;
  
  -- 주간 상위 3
  SELECT *
  FROM board_list_view
  WHERE write_datetime BETWEEN '2024-05-22 00:00:00' AND '2024-05-28 23:59:59'
  ORDER BY favorite_count DESC, comment_count DESC, view_count DESC
  LIMIT 3;
  
  -- 특정 유저 게시물 리스트 불러오기
  SELECT *
  FROM board_list_view
  WHERE writer_email = 'email@email.com'
  ORDER BY write_datetime DESC;
  
  -- 인기 검색어 리스트
  SELECT search_word, count(search_word) AS count
  FROM search_log
  WHERE relation IS FALSE
  GROUP BY search_word
  ORDER BY count DESC
  LIMIT 15;
  
  -- 관련 검색어 리스트
  SELECT relation_word, count(relation_word) AS count
  FROM search_log
  WHERE search_word = '검색어'
  GROUP BY relation_word
  ORDER BY count DESC
  LIMIT 15;
  
  -- 유저 정보 불러오기 / 로그인 유저 정보 불러오기
  SELECT *
  FROM user
  WHERE email = 'email@email.com';
  
  -- 닉네임 수정
  UPDATE user
  SET nickname = '수정 닉네임'
  WHERE email = 'email@email.com';
  
  -- 프로필 이미지 수정
  UPDATE user
  SET profile_image = 'url2'
  WHERE email = 'email@email.com';

  ```
- 최종적으로
<img width="299" alt="스크린샷 2024-05-28 오후 3 31 32" src="https://github.com/jysung1122/aiModel/assets/56614779/a6513769-260c-415d-a45b-ea2e47c30675">

# 3. Back - End 개발하기
1. vscode 확장 프로그램 Spring Initializer Java Support(MS꺼) 설치
2. vscode에서 F1 키를 누르고 Spring Initializer: Create a Gradle Project... 선택
3. version: 3.3
4. language: Java
5. com.example (디폴트로 GO 아님 회사명 등등)
6. Artifact: board-back
7. packaging type: Jar
8. Java version: 17
9. Spring Web(Web), Validation(I/O), Spring Security(Security), Spring Data JPA(SQL), MySQL Driver(SQL), Lombok(Developer Tools).... Selected 6 dependencies 엔터
10. Generate into this folder 선택

- 필요한 폴더 생성, 필요없는 파일 삭제
<img width="299" alt="스크린샷 2024-05-29 오전 9 08 28" src="https://github.com/jysung1122/aiModel/assets/56614779/bdf5200d-b5d8-474f-8bd1-f95c5e3dca76">

## 개발 순서
1. provider/JwtProvider.java
2. filter/JwtAuthenticationFilter/java


### 개발 시작

# 3. Front - End 개발하기
- 아래 코드 vscode의 터미널에 입력 후 엔터
  ```
  npx create-react-app board-front --template typescript
  ```
  
- 필요한 폴더 생성, 필요없는 파일 삭제
<img width="299" alt="스크린샷 2024-05-29 오전 9 08 57" src="https://github.com/jysung1122/aiModel/assets/56614779/c07e50cd-ab08-4c97-86aa-66d3bb04629e">
