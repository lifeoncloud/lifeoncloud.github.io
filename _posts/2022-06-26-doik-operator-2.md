---
title:  "NoSQL, MongoDB, Percona Operator for MongoDB 소개"
excerpt: "mongoDB 간단 소개"

categories: k8s
tags: [Kubernetes]

header:
  teaser: "/assets/images/k8s/220626-doik-nosql-vs-sql.png" 

toc: true
toc_sticky: true
comments: true

comments: true

date: 2022-06-26
last_modified_at: 2022-07-18
---

## 들어가며

본 포스팅은 [CloudNet@ 팀의 가시다 님](https://gasidaseo.notion.site/gasidaseo/CloudNet-Blog-c9dfa44a27ff431dafdd2edacc8a1863){:target="_blank"} 이 진행하시는 [쿠버네티스 데이터베이스 오퍼레이터 스터디](https://gasidaseo.notion.site/e49b329c833143d4a3b9715d75b5078d){:target="_blank"} 내용을 정리한 내용입니다.


## NoSQL


### NoSQL 소개
![SQL과 NOSQL 비교](/assets/images/k8s/220626-doik-nosql-vs-sql.png)

용어 'NoSQL'은 비-관계형 데이터베이스 유형을 나타내며, 이러한 데이터베이스는 관계형 테이블과 다른 형식으로 데이터를 저장합니다. 그러나 NoSQL 데이터베이스는 관용적 언어 API, 선언적 구조화 쿼리 언어, 쿼리별 예제 언어를 사용하여 쿼리할 수 있으므로 "not only SQL"(SQL 이외도 사용하는) 데이터베이스라고도 합니다.  from [오라클](https://www.oracle.com/kr/database/nosql/what-is-nosql/){:target="_blank"}

한마디로 Not Only SQL, 즉 NoSQL은 SQL에다가 부가적인 특성을 지원한다는 의미를 가지고 있다.


### NoSQL 특징

NoSQL의 특징으로는 다음과 같다.

출처: [https://docs.microsoft.com/ko-kr/dotnet/architecture/cloud-native/relational-vs-nosql-data](https://docs.microsoft.com/ko-kr/dotnet/architecture/cloud-native/relational-vs-nosql-data){:target="_blank"} 

* Document Store(mongoDB, CouchDB)
* Key-Value Store(Redis, Voldemort, Dynamo)
* Wide Column Store(HBase, Cassandra)
* Graph Store(Neo4j)


### NoSQL사용을 권장할 때 vs 권장하지 않을 때
#### 권장할 때



* 데이터 구조가 자주 바뀌어서, 사전에 정확한 데이터 구조를 정의하기 힘들 때
* 지속적으로 새로운 기능이 추가되고 변경될 때
* 막대한 양의 데이터를 다룰 때(=수평 확장이 필요할 때)


#### 권장하지 않을 때
=SQL을 사용하는 것이 더 나을 떄



* 복잡한 조건(예: 데이터 유형이 다양할 때, 데이터를 여러 차례에 걸칠 때)으로 데이터를 필터링해야 할 때
* [ACID 특성](https://www.youtube.com/watch?v=e9PC0sroCzc){:target="_blank"} 을 지켜야 할 때
* 변경되지 않는 스키마가 사용자와 데이터에게 중요한 경우(무결성)
* 몽고DB를 지원하지 않는 도구를 사용해야할 때(예: 워드프레스)
    * 아직 MongoDB는 관계형 데이터베이스 생태계를 따라가지 못한다.


## MongoDB


### MongoDB 소개

![mongoDB와 SQL 비교](/assets/images/k8s/220626-doik-mdb-vs-sql-resize-from-mongodb.png)
출처: [https://www.mongodb.com/docs/](https://www.mongodb.com/docs/){:target="_blank"} 

데이터 스키마가 엄격하게 정의되지 않은 NoSQL의 특성을 MongoDB도 그대로 물려받는다.
위에 나열된 NoSQL의 특징 중에서 MongoDB는 Document Store특징을 가지고 있다.
관계형데이터베이스에서는 어려운 분산 확장을 NoSQL에서 할 수 있도록 나온 개념이 샤딩이다.
그런데 MongoDB 커뮤니티에서 공식 지원하는 오퍼레이터들은 샤딩을 지원하지 않는다.~~어째서!~~


### MongoDB 기본 개념
특수키(_id)
* "Object**_ID**" Field : 관계형데이터베이스에서 Primary Key에 해당하는 ID키를 가진다.

도큐먼트(Document)
* 몽고DB의 핵심. (쿠버네티스로 치면 Pod같은 느낌)
* 키와 연결된 값의 집합.
* 표현 방식: map, hash, dictionary
* 몽고디비는 스키마가 고정되어 있지 않으므로 필요할 때마다 필드를 추가하거나 제거할 수 있다.
* 관계형데이터베이스에서 하나의 데이터를 '레코드'라고 하듯이, 몽고디비에서는 '도큐먼트'라고 한다. 
* 키는 중복될 수 없다.
    * 잘못된 도큐먼트 예시: {"greeting" : "Hello, world!", "greeting" : "Hello, world!"}
* 도큐먼트는 데이터형과 대소문자를 구분한다.
    * {"count" : 5} 와 {"count" : **"5"**} 는 다르다
    * {"count" : 5} 와 {"**C**ount" : 5} 는 다르다

컬렉션(Collection)
* 도큐먼트의 모음.
* 관계형데이터베이스에서 테이블에 대응된다.
* 동적스키마를 가진다. 
    * 하나의 컬렉션 안에 도큐먼트들이 모두 다른 구조를 가질 수 있다는 뜻


컬렉션은 동적 스키마를 가진다.
하나의 컬렉션 안에 있는 도큐먼트들은 다른 구조를 가질 수 있다는 뜻이다.
```sql
#컬렉션 예시
{
    "greeting": "Good Night!",
    "age": 17
}

{
    "emotion": "sad"
}
```

데이터베이스(Database)



* 컬렉션이 도큐먼트를 그룹화 한 것이라면, 데이터베이스는 컬렉션을 그룹화한 것이다.
* 데이터베이스는 컬렉션과 마찬가지로 이름으로 식별된다. 
    * 이름은 (admin, local, config를 제외한) UTF-8 문자열로 쓸 수 있다.
* 컬렉션을 저장할 때, 컬렉션명 앞에 데이터베이스명을 붙이면, 즉 데이터베이스명을 명시하면 올바른 컬렉션명인 네임스페이스를 얻는다.

정리하면 다음과 같다.


<table>
  <tr>
   <td>관계형 데이터베이스
   </td>
   <td>MongoDB
   </td>
  </tr>
  <tr>
   <td>Table
   </td>
   <td>Collection
   </td>
  </tr>
  <tr>
   <td>Record
   </td>
   <td>Document
   </td>
  </tr>
  <tr>
   <td>Column
   </td>
   <td>Field
   </td>
  </tr>
  <tr>
   <td>Primary Key
   </td>
   <td>Object<strong>_ID</strong> Field
   </td>
  </tr>
  <tr>
   <td>Relationship
   </td>
   <td>Embeded & Link
   </td>
  </tr>
</table>



### MongoDB 기본 사용법(CRUD)


#### Create



```sql
# 콜렉션 생성하기
db.createCollection("users")

# Document 1개 입력하기 : insertOne 
db.users.insertOne(
 {
  name: "kai",
  age: 29,
  emotion: "happy",
 }
)

# Document 여러개 입력하기 : insertMany
# insertMany를 사용하면 RDBMS 처럼 컬럼(스키마)를 편하게 입력 가능(파이썬의 List [] 문법을 사용한다.)
db.users.insertMany(

 [
  { name: "soojin", age: 32, emotion: "joyful" },
  { name: "jk", age: 31, emotion: "happy" },
  { name: "mummu", age: 3, emotion: "boring" }
 ]
)

```

![DB 생성 후, 컬렉션 생성](/assets/images/k8s/220626-doik-mongodb-crud-1-use-db-create-collection-document.png)

#### Search(=Read)

|MongoDB|RDBMS|
|---|---|
|db.users.find()|SELECT * FROM user|
|db.users.find({ }, { name: 1, age: 1 })|SELECT _id, name, age FROM user|
|db.users.find({ emotion: "happy"})|SELECT * FROM user WHERE emotion = "happy"|
|db.users.find({ age: { $gt: 30 } })|SELECT * FROM user WHERE age > 30|
|db.users.find({ emotion: "happy" } ).sort( { age: 1 })|SELECT * FROM user WHERE emotion = "happy" ORDER BY age ASC|

![read](/assets/images/k8s/220626-doik-mongodb-crud-2-read.png)

#### Update

set는 field를 지정한다.
inc: field 값을 증가시키거나, 감소시킨다.

```sql
# $inc를 사용한 update 예시: name이 "soojin"인 Document의 age를 10 감소시키기
db.users.updateOne(
    {name: "soojin"},
    {$inc: {age: -10}}
)

# $set를 사용한 update 예시: age 가 30 보다 큰 Document 들의 status 를 sad 로 변환하기
db.users.updateMany( { age: {$gt: 30} }, { $set: {emotion: "sad"} } )
```
![update](/assets/images/k8s/220626-doik-mongodb-crud-3-update-set.png)
![update](/assets/images/k8s/220626-doik-mongodb-crud-3-update-inc.png)


#### Delete
```sql
# emotion이 "sad"인 도큐먼트만 삭제하기
db.users.deleteMany( { emotion: "sad" } )

# 도큐먼트 일괄 삭제하기
db.users.deleteMany({})

#user 컬렉션 삭제하기
db.users.drop()
```

![도큐먼트 삭제](/assets/images/k8s/220626-doik-mongodb-crud-4-delete-document.png)

![컬렉션 삭제](/assets/images/k8s/220626-doik-mongodb-crud-4-delete-collections.png)



## Percona Operator for MongoDB 소개

"Percona Server for MongoDB는 MongoDB Enterprise Edition에서만 사용할 수 있는 기능을 포함하도록 MongoDB Community Edition을 확장합니다."

출처: [https://docs.percona.com/percona-server-for-mongodb/5.0/comparison.html](https://docs.percona.com/percona-server-for-mongodb/5.0/comparison.html){:target="_blank"} 

퍼코나 오퍼레이터(Percona Operator)가 지원하는 MongoDB Enterprise Edition에서만 사용할 수 있는 기능들

대표적으로 Hot BackUp과 인증이 있다.

Percona Operator에서 지원하는 인증들


* SCRAM (기본값)
* x.509 certificate authentication
* LDAP authentication with SASL
* Kerberos Authentication
* Authentication and authorization with direct binding to LDAP


## 참고링크
* [DOIK 스터디](https://gasidaseo.notion.site/e49b329c833143d4a3b9715d75b5078d){:target="_blank"}
* [https://www.oracle.com/kr/database/nosql/what-is-nosql/](https://www.oracle.com/kr/database/nosql/what-is-nosql/){:target="_blank"} 
* [https://velog.io/@fore0919/TIL-DB-SQL-vs-NoSQL](https://velog.io/@fore0919/TIL-DB-SQL-vs-NoSQL){:target="_blank"} 


{% if page.comments %}
<div id="disqus_thread"></div>
<script>
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://lifeoncloud-github-io.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}