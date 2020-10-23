---
title: DynamoDB 시작하기
date: 2020-10-13 14:28:38
tags:
    - DynamoDB
categories:
    - AWS
    - AWS Guide
thumbnail: /images/AWS/AWSGuide/다이나모DB_시작하기/index.svg
---

![DynamoDB](/images/AWS/AWSGuide/다이나모DB_시작하기/index.svg)


---------------------------------------

관계형 DB vs NO SQL 비교

- 관계형 DB
    - 정형 데이터
    - 대용량 처리 시 성능 하향
    - 미리 정해진 스키마 존재
    - 트랜잭션을 통해 일관성 유지 보장
    - 조인 등의 복잡한 검색 기능
    - 클러스터 환경에 적합하지 않음
    - 고가의 라이센스 비용
    - Oracle, MySql, MSSql 등

- NO SQL
    - 정형, 반정형, 비정형 데이터
    - 대용량 데이터 처리 지원
    - 스키마가 없거나 변경이 자유로움
    - 트랜잭션 지원하지 않음, 일관성이 보장 어려움(사실, 보장 하지만 관계형 DB보다는 여유롭게 보장)
    - 단순히 데이터 검색 기능
    - 클러스터 환경에 적합
    - 오픈 소스
    - 카산드라, 몽고DB 등

---------------------------------------

DynamoDB는 크게 쿼리와 스캔이라는 데이터 탐색 방법을 제공
    - 쿼리 : 삽입된 기본키를 기준으로 데이터를 찾는 방법
    - 스캔 : 조건 값과 맞는 데이터를 찾을 때까지 모든 데이터를 검색

---------------------------------------

구축 과정
1. 다이나모DB 테이블 만들기
    A. 테이블 이름 : univStudent
    B. 기본키 : univ_name + univ_id
2. 테이블 데이터 추가
3. 데이터 수정 및 삭제
4. 데이터 스캔과 쿼리
5. 테이블 삭제

---------------------------------------

![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart.JPG)


![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart1.JPG)

- AWS console에서 DynamoDB를 선택
- 죄측 대시보드 밑의 테이블을 선택
- 테이블 만들기를 선택


![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart2.JPG)

- 테이블 이름은 univStudent, 기본키는 univ_name과 univ_id를 선택 후 생성 버튼을 클릭


![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart3.JPG)

- 항목 만들기 선택


![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart4.JPG)

- 내용을 입력
- 이 때, + 키를 누른 후 append를 클릭하여 데이터를 추가할 수 있음
- 저장을 클릭


![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart5.JPG)

- 데이터를 여러개 넣을 수 있으며, 형식이 전부 통일되지 않아도 됨

---------------------------------------

![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart6.JPG)

- 해당 데이터를 선택한 후 작업의 삭제를 통하여 데이터를 삭제할 수 있음

---------------------------------------

![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart7.JPG)

- 스캔은 조건값이 맞는 데이터를 찾을 때까지 모든 데이터를 탐색해서 어떤 조건 값에 맞는 데이터를 몇 개 찾아와라라는 명령어가 없다고, 모든 데이터를 찾음
- 필터를 통해서 조건을 줄 수 있음


![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart8.JPG)

- 쿼리는 아무런 값도 없이 검색을 누르면 에러가 발생
- 쿼리는 기본키를 입력하여 데이터를 검색하는 방법

---------------------------------------

![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart9.JPG)

- 글로벌 보조 인덱스는 동일한 데이터를 갖지만, 다른 키 값과 정렬키를 갖는 클론 테이블을 만들어서 테이블을 만들때와 같은 처리 용량이 필요함


![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart10.JPG)

- 인덱스를 만드는 시간은 오래 걸리지만 상태가 활성이 되면 완료된 것


![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart11.JPG)

- 스캔을 인덱스로 변경한 후 검색 시작을 선택하면 major가 포함된 데이터만 표시 됨
- 인덱스를 만든 테이블에서는 major을 기본키로 하기 때문에 원 테이블에서 major가 포함되지 않은 데이터는 가져올 수 가 없음


![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart12.JPG)

- 쿼리는 키 값을 찾아 데이터를 검색하는 방법
- 원 테이블에서 스캔으로 major을 찾는 것보다 인덱스에서 쿼리로 찾는 것이 처리용량이 더 작음

---------------------------------------

![](/images/AWS/AWSGuide/다이나모DB_시작하기/DynamoDBStart13.JPG)

- 좌측 상단의 테이블 삭제를 클릭하여 안전하게 테이블을 삭제할 수 있음