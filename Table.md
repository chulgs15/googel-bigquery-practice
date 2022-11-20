# Table

* 정의

  * 데이터를 보관하는 Object 로, 열 기반으로 저장한다.
  * 스키마 : 모든 테이블의 열 이름, 데이터 유형 같은 메타 정보.

* 이름

  * `projectname.datasetname.tablename` 형식

* 유형

  * 테이블 
  * 외부 테이블 
  * 뷰 

* 제한사항

  * 데이터 세트마다 이름이 고유해야 한다.
  * 테이블 복사는 동일한 Region 에서만 가능하다.
    * US => EU 로 복사 불가능하다.
  * 여러 소스 테이블로 대상 테이블에 복사할 때, 소스 테이블들은 스키마가 동일해야 한다.
    * :arrow_double_down: 열 길이가 다르다면 복제가 가능한가?
  * API 호출을 사용할 때 데이터 세트의 테이블 수가 5만개에 근접하면 성능이 저하됩니다.

* public data 를 개인 Dataset 으로 가져오려면 어떻게 해야 하는가?
  * 복제 기능이 있네.

* 테이블의 종류
  * 파티션 테이블
  * 클러스터링 테이블
* 테이블 만들기
  * create table
  * create table as select 
* 테이블 정보 INFORMATION_SCHEMA 확인
  * 예시를 위주로 정리하면 되겠네.

## Table 변경

### Alter table

alter table 로 테이블의 내용을 변경할 수 있다. 

* 아래와 같은 쿼리로 변경한다.

```sql
alter table mydataset.testtable
  set options (
    description = 'my first test table'
  );
```

* 확인 결과.

![image](https://user-images.githubusercontent.com/22446581/202307295-cbd07bc4-b028-48a6-8783-945d49fec533.png)

### 만료 시간 업데이트

*  수명 또는 TTL 이라고도 합니다. 
* 시간이 만료되면 모든 데이터와 테이블이 삭제된다.
  * 지정하지 않으면 무제한으로 사용.

```sql
alter table mydataset.testtable
  set options (
    description = 'my first test table',
    expiration_timestamp = timestamp '2022-11-18 12:34:56'
  );
```

* 결과 확인

![image](https://user-images.githubusercontent.com/22446581/202308090-01081c0c-e0ea-4b30-947b-222887ba743d.png)



### 테이블 이름 바꾸기

* 테이블을 만든 후 이름을 변경

```sql
alter table mydataset.testtable rename to testtable3;
```

## 테이블 복사

* 복사 제한 사항
  * 프로젝트 당 하루 최대 100000 개 작업 실행.
  * 소스 테이블보다 열이 많고 추가 열에 기본값이 포함된 대상 테이블로 소스 테이블을 복사하고 연결할 수 없습니다.
  * 다른 Dataset 으로 복제 가능하다.

### 단일 소스 테이블 복사.

* 소스

```sql
create table  `mydataset.publications`
copy `mydataset.publiccations`
;
```

* 결과 확인.

![image](https://user-images.githubusercontent.com/22446581/202350671-d3a06112-409c-495c-953c-83ac02843b1c.png)

### 여러 소스 테이블 복사

* 복수 테이블을 1개 테이블로 복제할 수 있다.
* 단 복수 테이블은 스키마가 동일해야 한다.
* SQL 로는 불가. `bq` 나 Client Library 를 사용한다.

## 테이블 삭제

* 한번에 테이블 한개만 삭제할 수 있다.

```sql
drop table mydataset.publiccations;
```

## 삭제된 테이블 복원

* 시간 이동을 사용하면 데이터를 복구할 수 있다. 
  * 2~7 일 보관한다. 기본값은 7일이다.
  * 시간 이동 창을 구헝하는 기능은 미리보기에 있다.

* 데이터 삭제

```sql
TRUNCATE TABLE
  mydataset.publications;
```

![image](https://user-images.githubusercontent.com/22446581/202352062-8ca7ebc8-2cdd-438e-b13f-1558b63fab97.png)

* bq 로 복구

```powershell
C:\Users\shin\AppData\Local\Google\Cloud SDK>bq cp mydataset.publications@-300000 mydataset.publications_restore
Waiting on bqjob_r30546a83f11a5574_0000018483be79ea_1 ... (0s) Current status: DONE
Table 'master-gecko-367600:mydataset.publications@-300000' successfully copied to 'master-gecko-367600:mydataset.publications_restore'
```

* 실제 복구 여부 확인.

![image-20221117130252858](C:\Users\shin\AppData\Roaming\Typora\typora-user-images\image-20221117130252858.png)



