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
- vscode 확장 프로그램인 ERD Editor (dineug: erd-editor.io) 설치하여 진행
- board.vuerd.json

- <img width="1440" alt="스크린샷 2024-05-28 오전 10 42 20" src="https://github.com/jysung1122/aiModel/assets/56614779/fedfbaba-e4c3-460e-b939-79717d4c47a7">
- <img width="1440" alt="스크린샷 2024-05-28 오전 10 42 35" src="https://github.com/jysung1122/aiModel/assets/56614779/29223255-7109-4872-bee4-ab17408125fd">
- <img width="1440" alt="스크린샷 2024-05-28 오전 10 42 42" src="https://github.com/jysung1122/aiModel/assets/56614779/704fd9eb-26af-4a90-9ebb-a4586dbf8ddc">

- board DB 생성 후 DDL Query 생성
- <img width="299" alt="스크린샷 2024-05-28 오전 10 47 19" src="https://github.com/jysung1122/aiModel/assets/56614779/772e411e-3f3f-4e4d-89aa-335841280600">

- DDL 코드 작성 후 하나하나 실행
  ```
  -- Active: 1716358723865@@127.0.0.1@3306@board  MYSQL

        
  CREATE TABLE board
  (
    border_number  INT         NOT NULL AUTO_INCREMENT COMMENT '게시물 번호',
    title          TEXT        NOT NULL COMMENT '게시물 제목',
    content        TEXT        NOT NULL COMMENT '게시물 내용',
    write_datetime DATETIME    NOT NULL COMMENT '게시물 작성 날짜 및 시간',
    favorite_count INT         NOT NULL DEFAULT 0 COMMENT '게시물 좋아요 수',
    comment_count  INT         NOT NULL DEFAULT 0 COMMENT '게시물 댓글 수',
    view_count     VARCHAR(50) NOT NULL DEFAULT 0 COMMENT '게시물 조회 수',
    writer_email   VARCHAR(50) NOT NULL COMMENT '게시물 작성자 이메일',
    PRIMARY KEY (border_number)
  ) COMMENT '게시물 테이블';
  
  CREATE TABLE comment
  (
    comment_number INT         NOT NULL AUTO_INCREMENT COMMENT '댓글 번호',
    content        TEXT        NOT NULL COMMENT '댓글 내용',
    write_datetime DATETIME    NOT NULL COMMENT '작성 날짜 및 시간',
    user_email     VARCHAR(50) NOT NULL COMMENT '사용자 이메일',
    border_number  INT         NOT NULL COMMENT '게시물 번호',
    PRIMARY KEY (comment_number)
  ) COMMENT '댓글 테이블';
  
  CREATE TABLE favorite
  (
    user_email    VARCHAR(50) NOT NULL COMMENT '사용자 이메일',
    border_number INT         NOT NULL COMMENT '게시물 번호',
    PRIMARY KEY (user_email, border_number)
  ) COMMENT '좋아요 테이블';
  
  CREATE TABLE image
  (
    border_number INT  NOT NULL COMMENT '게시물 번호',
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
      FOREIGN KEY (border_number)
      REFERENCES board (border_number);
  
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
      FOREIGN KEY (border_number)
      REFERENCES board (border_number);
  
  ALTER TABLE comment
    ADD CONSTRAINT FK_user_TO_comment
      FOREIGN KEY (user_email)
      REFERENCES user (email);
  
  ALTER TABLE comment
    ADD CONSTRAINT FK_board_TO_comment
      FOREIGN KEY (border_number)
      REFERENCES board (border_number);

  CREATE USER 'developer'@'*' IDENTIFIED BY 'P!ssw0rd';
  ```
- DCL Query 생성 후 실행
  ```
  -- Active: 1716358723865@@127.0.0.1@3306@board  MYSQL

  GRANT ALL PRIVILEGES ON board.* TO 'developer'@'*';

  ```
