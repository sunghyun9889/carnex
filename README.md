# Spring 중고차 플랫폼 프로젝트

### 프로젝트 기간 : 24.07.04 ~ 24.12.13
### 프로젝트 수행자 : 조성현(chosh0809@nate.com)
### 시연 영상 : 

## 환경
- Windows 10
- JDK 1.8
- Tomcat 9.0
- Spring 4.3.8
- MariaDB 10.11
- MyBatis 3.4.1
- Lombok
- Gson
- JSTL
- Spring Security
- Encoding : UTF-8

## 디자인 및 차량, 차량이미지, 차량설명 참고 사이트
 1. KB차차차
  - https://www.kbchachacha.com/
 2. SK엔카
  - https://car.encar.com/
 3. 카바조
  - https://www.carvazo.com/

## MySQL 데이터베이스 생성 및 사용자 생성
```sql
CREATE USER 'dba'@'%' identified by 'dbapwd';
GRANT ALL privileges on *.* TO 'dba'@'%';
create database carnex;
```

## MySQL 테이블 생성
```sql
use carnex;
```

```sql
CREATE TABLE user_tbl (
	user_id VARCHAR(30) NOT NULL COMMENT '회원아이디',
	user_pwd VARCHAR(150) NOT NULL COMMENT '회원비밀번호',
	user_name VARCHAR(20) NOT NULL COMMENT '회원 이름',
	user_nick VARCHAR(15)	 NOT NULL COMMENT '닉네임',
	user_birth VARCHAR(15) NOT NULL COMMENT '회원 생년월일',
	user_gender CHAR(2) NOT NULL COMMENT '성별',
	user_tel	 CHAR(13) NOT NULL COMMENT '회원 전화번호',
	user_zipcode VARCHAR(7) NOT NULL COMMENT '회원 우편번호',
	user_address VARCHAR(150) NOT NULL	 COMMENT '회원 주소',
	user_mail VARCHAR(30)	 NULL COMMENT '회원 이메일',
	user_wishcar VARCHAR(50) NULL COMMENT '선호차량',
	user_wishprice VARCHAR(50) NULL COMMENT '선호 가격',
	user_path VARCHAR(50) NULL COMMENT '가입경로',
	user_type INT NULL DEFAULT 1 COMMENT '회원 타입', -- 0 비회원, 1 회원
	user_drop TEXT NULL COMMENT '탈퇴 사유',
	user_img TEXT NULL COMMENT '이미지 경로',
	user_intro TEXT NULL COMMENT '인사말',
	user_reg_date TIMESTAMP NULL COMMENT '회원 가입일시',
	user_up_date TIMESTAMP COMMENT '회원 수정일시',
	PRIMARY KEY (user_id)
) COMMENT '회원테이블';

CREATE TABLE staff_tbl (
	staff_id VARCHAR(15) NOT NULL COMMENT '사원번호',
	staff_pwd VARCHAR(150) NOT NULL COMMENT '비밀번호',
	staff_name VARCHAR(30) NOT NULL COMMENT '이름',
	staff_phone CHAR(13) NULL COMMENT '연락처',
	staff_part INT NULL COMMENT '부서',-- 1 C/S, 2 영업, 3 임원
	staff_type INT NULL DEFAULT 1 COMMENT '고용상태', -- 0 퇴사, 1 근무
	staff_img TEXT NULL COMMENT '이미지 경로',
	staff_intro TEXT NULL COMMENT '인사말',
	staff_reg_date TIMESTAMP NULL COMMENT '입사일',
	staff_up_date TIMESTAMP NULL COMMENT '수정일',
	staff_out_date TIMESTAMP NULL COMMENT '퇴사일',
	PRIMARY KEY (staff_id)
) COMMENT '직원테이블';

CREATE TABLE board_tbl (
	bno INT AUTO_INCREMENT COMMENT '게시물번호',
	title VARCHAR(150) NULL COMMENT '게시물제목',
	content TEXT NULL COMMENT '게시물내용',
	writer VARCHAR(30) NULL COMMENT '작성자',
	viewcnt INT NULL DEFAULT 0 COMMENT '조회수',
	replycnt INT NULL DEFAULT 0 COMMENT '댓글수',
	reg_date TIMESTAMP NULL COMMENT '작성일',
	up_date TIMESTAMP NULL COMMENT '수정일',
	user_id VARCHAR(30) NULL COMMENT '유저아이디',
	staff_id VARCHAR(15) NULL COMMENT '사원번호',
	PRIMARY KEY (bno),
	FOREIGN KEY (user_id) REFERENCES user_tbl(user_id),
	FOREIGN KEY (staff_id) REFERENCES staff_tbl(staff_id) 
) COMMENT '게시판테이블';

CREATE TABLE goods_tbl (
	car_num INT AUTO_INCREMENT COMMENT '자동차 상세번호',
	car_brand VARCHAR(10) NOT NULL COMMENT '자동차 제조사',
	car_type VARCHAR(15) NOT NULL COMMENT '자동차 분류',
	car_model VARCHAR(15) NOT NULL COMMENT '자동차 모델',
	car_price INT NULL COMMENT '가격',
	car_title	 VARCHAR(100) NULL COMMENT '상품 제목',
	car_subtitle VARCHAR(100) NULL COMMENT '서브타이틀',
	car_content TEXT NULL COMMENT '자동차 설명',
	car_accident INT NULL COMMENT '사고 유무', -- 0 무사고, 1 사고내역 있음
	car_zone CHAR(2) NULL COMMENT '보유지점', -- 광교, 용인, 분당
	car_thumbimg TEXT NULL COMMENT '자동차 이미지 경로',
	car_img TEXT NULL COMMENT '상세 이미지 경로',
	car_status VARCHAR(4) NULL COMMENT '판매 상태', -- 거래중, 판매완료, 판매보류, ...
	car_reg_date TIMESTAMP NULL COMMENT '등록일',
	car_up_date TIMESTAMP NULL	COMMENT '수정일',
	staff_id VARCHAR(15) NOT NULL COMMENT '사원번호',
	PRIMARY KEY (car_num),
	FOREIGN KEY (staff_id) REFERENCES staff_tbl(staff_id)
) COMMENT '상품테이블';

CREATE TABLE  reply_tbl (
    rno INT AUTO_INCREMENT COMMENT'댓글번호',
    user_id VARCHAR(30) NULL COMMENT'회원아이디',
    content TEXT NOT NULL COMMENT'내용',
    writer VARCHAR(30) NOT NULL COMMENT'작성자',
    reg_date TIMESTAMP NULL COMMENT'작성일',
    up_date TIMESTAMP NULL COMMENT'수정일',
    bno INT NOT NULL COMMENT'게시물번호',
    staff_id VARCHAR(15) NULL COMMENT'사원번호',
    PRIMARY KEY(rno),
    FOREIGN KEY (bno) REFERENCES board_tbl (bno) ON DELETE CASCADE,
    FOREIGN KEY (staff_id) REFERENCES staff_tbl (staff_id),
    FOREIGN KEY (user_id) REFERENCES user_tbl (user_id)
) COMMENT '댓글테이블';

CREATE TABLE attach_tbl (
	UUID VARCHAR(100) NOT NULL COMMENT '랜덤접두사',
	uploadPath VARCHAR(200) NOT NULL COMMENT '업로드경로',
	fileName VARCHAR(100) NOT NULL COMMENT '파일명',
	fileType VARCHAR(5) DEFAULT 'I' COMMENT '이미지여부',
	bno INT NOT NULL COMMENT  '게시물번호'
) COMMENT '첨부파일테이블';

CREATE TABLE card_tbl (
	card_num INT AUTO_INCREMENT COMMENT'등록번호',
	user_id VARCHAR(30) NOT NULL COMMENT '회원아이디',
	card_bank VARCHAR(10) NOT NULL COMMENT '카드사 이름',
	card_lastname VARCHAR(10) NOT NULL COMMENT '성',
	card_firstname VARCHAR(20) NOT NULL COMMENT '이름',
	card_expdate VARCHAR(10) NOT NULL COMMENT '유효기간(YYYY-MM)',
	card_cvc INT NOT NULL COMMENT 'cvc번호',
	card_digit1 INT NOT NULL COMMENT '카드번호 자리1',
	card_digit2 INT NOT NULL COMMENT '카드번호 자리2',
	card_digit3 INT NOT NULL COMMENT '카드번호 자리3',
	card_digit4 INT NOT NULL COMMENT '카드번호 자리4',
	PRIMARY KEY(card_num),
	FOREIGN KEY (user_id) REFERENCES user_tbl(user_id)
) COMMENT '카드테이블';

CREATE TABLE address_tbl (
	zipcode CHAR(7) NULL COMMENT '우편번호',
	area1 CHAR(15) NULL COMMENT '도시/도',
	area2 CHAR(30) NULL COMMENT '구/군',
	area3 CHAR(40) NULL COMMENT '동/면',
	area4 CHAR(30) NULL COMMENT '번지'
) COMMENT'주소테이블';

CREATE TABLE contract_tbl (
	con_no INT AUTO_INCREMENT COMMENT '계약번호',
	con_sign TEXT NULL COMMENT '서명 정보',
	con_status VARCHAR(50) NULL COMMENT '계약 구분',
	con_reg_date TIMESTAMP NULL DEFAULT now() COMMENT '계약날짜',
	car_num INT NOT NULL COMMENT '자동차 상세번호',
	staff_id VARCHAR(15) NOT NULL COMMENT '사원번호',
	card_num INT NOT NULL COMMENT '카드등록번호',
	user_id VARCHAR(30) NOT NULL COMMENT '구매자 아이디',
	PRIMARY KEY (con_no),
	FOREIGN KEY (car_num) REFERENCES goods_tbl(car_num) ON DELETE CASCADE,
	FOREIGN KEY (staff_id) REFERENCES staff_tbl(staff_id),
	FOREIGN KEY (card_num) REFERENCES card_tbl(card_num) ON DELETE CASCADE,	
	FOREIGN KEY (user_id) REFERENCES user_tbl(user_id)
) COMMENT '계약테이블';

CREATE TABLE reservation_tbl (
	rev_no INT AUTO_INCREMENT COMMENT '상담예약번호',
	rev_status VARCHAR(10) NULL COMMENT '상담예약상태',
	rev_time VARCHAR(50) NULL COMMENT '예약시간',
	rev_date VARCHAR(50) NULL COMMENT '예약날짜',
	rev_reg_date TIMESTAMP NULL COMMENT '접수날짜',
	user_id VARCHAR(30) NOT NULL COMMENT '회원아이디',
	car_num INT NOT NULL COMMENT '자동차 상세번호',
	staff_id VARCHAR(15) NOT NULL COMMENT '사원번호',
	PRIMARY KEY (rev_no),
	FOREIGN KEY (user_id) REFERENCES user_tbl(user_id),
	FOREIGN KEY (car_num) REFERENCES goods_tbl(car_num) ON DELETE CASCADE,
	FOREIGN KEY (staff_id) REFERENCES staff_tbl(staff_id)
) COMMENT '상담예약테이블';

```

# 구현한 기능들
 <b>1. 회원가입</b>
 ![image](https://github.com/user-attachments/assets/f6af8f9f-d720-4bb7-b565-92ba7ed726ac)
 - 회원가입 시 입력한 비밀번호는 Spring Security으로 인코딩되어 DB에 저장됩니다.
   
 ![image](https://github.com/user-attachments/assets/8b7750a5-f90b-48af-bf13-1eb5f11b141a)

 
 <b>2. 회원 로그인</b>
 ![image](https://github.com/user-attachments/assets/51e43a45-e7a0-43d9-a0c6-bb28563f16e1)

 
 <b>3. 직원 로그인</b>
 ![image](https://github.com/user-attachments/assets/93fa138d-4562-4f63-81d6-877ca3fbc866)

 
 <b>4. 유저메인페이지</b>
 ![image](https://github.com/user-attachments/assets/49e2b079-f313-4ac8-ba90-e54d4e61c39f)
 - FlexSlider를 활용하였습니다.

 
 <b>5. 메인페이지 내 필터 검색</b>
 ![image](https://github.com/user-attachments/assets/2407245e-b0fd-406a-a96e-c0f5a97f0b78)
 - 차량등록시 지정한 차종의 데이터로 선택한 모든 차종을 불러옵니다. (매물이 추가되어도 따로 수정할 필요가 없습니다.)
 
 
 <b>6. 차종별 상품 페이지</b>
 ![image](https://github.com/user-attachments/assets/cb621893-9750-4a44-afff-4620492d26cb)
 - 차량등록시 지정한 차종의 데이터로 선택한 모든 차종을 불러옵니다. (매물이 추가되어도 따로 수정할 필요가 없습니다.)

 
 <b>7. 전체 차량 페이지</b>
 ![image](https://github.com/user-attachments/assets/0232af7c-303d-465c-90e4-85d11fb8e6aa)
 - 모든 결과를 보여줍니다.


 <b>8. 게시판 페이지</b>
 ![image](https://github.com/user-attachments/assets/732e10c5-4aca-4f79-9a76-9e20073edc5c)
 - PageDTO를 설계하여 페이징 처리 하였습니다.


 <b>9. 필터검색기능</b>
 ![image](https://github.com/user-attachments/assets/7e4684eb-d7f7-46d2-bd0e-5763a68f8526)
 - 검색타입, 키워드를 활용하여 게시글 제목 또는 작성자 필터검색이 가능합니다.

 
 <b>10. 로그인 시 상단 메뉴 변경</b>
 ![image](https://github.com/user-attachments/assets/2ebe91d6-23c0-4810-a792-ad5e17d01260)
 - 유저가 로그인 시 기존 로그인/회원가입 버튼에서 마이페이지, 로그아웃으로 변경됩니다.
 - 비회원 등급 유저는 매물검색, 게시판 등 페이지는 노출되지 않습니다.
   

 <b>11. 정보수정 클릭 시 비밀번호 재확인</b>
 ![image](https://github.com/user-attachments/assets/22ce82fb-57bb-49f2-8d2f-5b4c4e95d433)
 - 유저가 자신이 설정한 비밀번호를 입력하면, DB에 저장된 값을 디코딩하여 입력받은 값과 비교합니다.


 <b>12. 회원정보 수정</b>
 ![image](https://github.com/user-attachments/assets/1fb83c70-b18f-4a2d-8d66-32fd342d4e40)


 <b>13. 회원 마이페이지</b>
 ![image](https://github.com/user-attachments/assets/4aa7a546-c48d-4658-806e-1268059c6c92)

 
 <b>14. 상세상품 페이지 - 상단</b>
 ![image](https://github.com/user-attachments/assets/1376b1c8-37c7-4c3e-9ac5-45228faef78d)
 - 예약하기 버튼 클릭으로 상담예약이 가능합니다. (로그인 시에만)

 ![image](https://github.com/user-attachments/assets/56922f7c-791a-48d9-9e61-bd810652a537)

 
 <b>15. 어드민 부터 대시보드</b>
 ![image](https://github.com/user-attachments/assets/b5d83838-9e94-4be7-ad31-33632b245bab)
 - 회사가 보유중인 상품 정보 및 회원정보를 불러오며, 회원 선호도와 비교 분석할 수 있는 그래프로 노출합니다.
 - 하단 부에는 로그인한 직원의 예약상담 건수를 노출합니다.


 <b>16. 회원관리</b>
 ![image](https://github.com/user-attachments/assets/8cbd3745-9501-42f5-9e2b-df1bd26bf8bd)
 - 회원 타입에 따라 회원목록/비회원목록으로 구분하였습니다.


 <b>19. 매물관리 판매상태별 메뉴</b>
 ![image](https://github.com/user-attachments/assets/09dbf3a7-cdc3-4568-bed3-90dc3bbe30bc)
 - 판매 상태에 따라 목록을 구분하였습니다.
 - 예약중인 차량에 대해서는 상품제목 클릭 시 상세페이지로 이동하며 즉시 계약서 작성을 가능할 수 있게 합니다.


 <b>20. 문의 관리</b>
 ![image](https://github.com/user-attachments/assets/ef0adfc5-93b1-4310-89c7-003cab02801e)
 - 유저 게시판에 내용을 직원 또는 관리자가 답변이 가능합니다.

 
