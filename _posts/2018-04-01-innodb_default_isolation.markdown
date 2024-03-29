---
published: false
title: MySQL InnoDB 의 Default isolation level  
categories: DBMS 
tags: [InnoDB. ACID, MySQL, transaction-isolation]
layout: post
excerpt: Isolation Level 에 대해 알아보고, MySQL 에서 왜 이 값을 바꿔야하는지에 대해서 알아봅니다.  
comments: yes
toc: true
last_modified_at: 2018-04-09T03:30:00+09:00
--- 

## 들아가며 

DBMS 에서 제공하는 수백개 이상의 설정 속성을 하나하나 다 알고 해당 값의 합리성을 의심하면서 쓰는 건 솔직히 불가능합니다. 그래서 꼭 바꿔야하는 널리 알려진 속성,  예를 들어 Language 관련 속성이나 Buffer Pool 의 크기와 같은 값을 제외하고는 뭐 쓸만하니까 해당 값을 생각했겠지라고 사용하는 것이 대부분일겁니다.  

그런데 MySQL innodb 에는 이러한 믿음을 깨버리는 설정 값이 있습니다. 바로 tranaction-isolation 이라는 속성인데, 기본값이  REPEATABLE READ 입니다. 며칠동안 궁금해서 MySQL 에서 왜 해당 값을 기본값으로 삼았는지에 대해서 찾아봤습니다. 일단 ISOLATION LEVEL 부터 봅시다. 

## ISOLATION LEVEL

ISOLATION LEVEL 은 트렌젝션의 격리성을 의미합니다. ISOLATION는 격리성 또는 고립성이라는 한자어로 번역되는데, 간단히 말하면 서로 동시에 수행되는 트렌젝션들이 다른 트렌젝션이 일으킨 변화에 대한 영향을 어디까지 허용할 것인가로 보면 됩니다. 트렌젝션의 4가지 속성 기억나시죠 ? ACID 중 I에 해당 되는 놈입니다. 

이러한 ISOLATION LEVEL은 예로부터 ANSI 에서 다음과 같이 4가지로 정의했습니다. 

* READ UNCOMMITED 
* READ COMMITED 
* REPEATABLE READ 
* SERIALIZABLE

#### READ UNCOMMITED 

용어 그대로 커밋되지 않은 레코드를 읽을 수 있다라는 것으로 이해하면 됩니다. 예를 들어서 다음과 같은 상황이 동시에 일어 났다고 합시다. 

* 트렌젝션 A : T1 테이블의 r1 row 를 c1 컬럼을 1에서 2로 바꾸는 중입니다.  
* 트렌젝션 B : T1 테이블을 SELECT 합니다. 

이때 트렌젝션 B 에서 읽은 값이 만약 2라면 이를 READ UNCOMMITED ISOLATION LEVEL 이라고 합니다. 엄밀하게 말하면 트렌젝션 A는 아직 Commit 하지 않은 데이터이기 떄문에 트렌젝션 B는 1을 읽어야 합니다. 사실 이렇게 데이터를 읽는 경우는 버그라고 볼 수도 있습니다. 

그런데 이걸 ANSI 에서 ISOLATION LEVEL 의 한 단계로 설정한 까닭은 무엇일까요 ? 답은 간단합니다. 읽기연산에서 Locking 에 대한 고려가 필요없기 때문입니다. 읽기는 절대로 블럭되지 않습니다. 그리고 읽기가 별도의 LOCK 을 세팅하지 않기 때문에 읽는 것때문에 다른 것들이 블럭되지도 않습니다.  물론 값의 일관성이 깨지는 건 각오해야겠죠. 센서 데이터와 같이 값의 일관성이 크게 중요하지 않은 곳에서는 사용할 만 합니다. 

Oracle 은 Concept Guide를 잘 읽으신 분이나 OCP 를 공부하신 분들은 잘 아시겠지만 READ UNCOMMITED 라는 ISOLATION을 아예 지원도 하지 않습니다. 이는 Oracle 특유의 MVCC 방법 때문에 SELECT 는 그냥 SNAPSHOT 이기 때문입니다. 즉 굳이 Dirty Read 를 하지 않더라도 거기에 준하는 동시성을 보장한다 생각하면 될것 같습니다. MVCC 주제는 너무 크니까 나중에 다른 포스트로 다시 정리해보겠습니다. 재밌는 내용이 많습니다. 

#### READ COMMITED 

말 그래도 커밋된 놈은 읽는다 라는 뜻입니다. 한 트렌젝션 안에서 데이터를 읽을떄 다른 트렌젝션이 이미 읽어서 COMMIT 을 수행했으면 그 값을 읽습니다. 우리가 밀반적으로 DBMS 라고 하면 대부분 기본이 바로 이겁니다. 그렇기 때문에 한 트렌젝션 안에서 동일한  SELECT 가 여러 번 반복 될때 매번 결과가 바뀔 수 있습니다. 여러번 읽을때 값이 바뀌는 것을 Non-Repeatable Read 라고 하고, 갑자기 처음읽을때와 두번째 읽을떄 새로운 row 가 튀어나오는 경우를 Phantom Read라고 하는데 READ COMMITED 에서는 둘다 발생합니다. 

흔히 주니어들이 많이 하는 실수죠. SELECT 로 읽어서 변수에다가 넣어놨는데 이걸 가지고 UPDATE 하러 갔더니 갑자기 Not Found 또는 엉뚱한 값으로 값이 바뀌어있더라.. 아니면 내가 분명히 보고 그 값을 업데이트했는데 업데이트가 사라졌더라. (이런 상황을 Lost Update 라고 합니다) 뭐 이런거요. 보통 이런 상황 발생하면 DBMS 버그라고 난리치기도 하죠. ( DBMS 가 명령을 가끔 씹는다고 표현하는 것도 들은 적이 있습니다 )

그렇기 때문에 서로 동시에 Transaction 이 발생하는 시스템에서는 SELECT ~ FOR UPDATE 구문이 필요합니다. 만약 Row A 를 읽고 나서 이를 무언가 처리를 수행하고, 해당 컬럼의 값을 변경하는 업무 일경우 내가 읽은 값을 아무도 못건드리게 해야 안심하고 업데이트할 수 있습니다. 

주로 웬만한 DBMS 들이 기본으로 가지는 ISOLATION LEVEL입니다. 

#### REPEATABLE READ 

이름 그대로 트렌젝션 어느시점에 값을 조회하더라도 동일한 값을 갖도록 하는 하는 방식입니다. Read Commited 에서는 한 트렉젝션여서 여러번 SELECT 를 수행하면 값이 바뀔 수 있다고 했는데, REPEATABLE READ 는 값이 동일합니다. 

그럼 의문을 가질 수 있는게 누군가 데이터를 바꾸면 어쩌지 입니다. 예를 들어서 트렌젝션 초반에 읽은 값이 누군가 변경했더라도 진행 중인 트렌젝션에서는 알아챌 수 없습니다. 그래서 내가 읽은 레코드가 누군가로 부터 바뀌지 않음을 보장하려면 무조건 SELECT ~ FOR UPDATE 를 써야합니다. 

REPETABLE READ 에서는  Phantom Read 를 막을 수 없고, Non-Repetable Read 는 막을 수 있습니다만 MySQL은 둘다 막습니다. 

#### SERIALIZABLE

어떤 테이블에 SELECT 를 수행하면 해당 SELECT 가 수행된 영역에 INSERT / UPDATE / DELETE 가 불가능합니다. 근데 뭐 이 설정은 거의 볼일이 없습니다.  과감히 무시하고 넘어갑니다. 

## MySQL 에서 transaction-isolation

MySQL InnoDB 에서 제공하는 Repetable Read 는  엄밀한 Repeatable Read 와는 달라요. 위에서도 잠깐 언급했지만,  Repetable Read 와 Serializable 중간에 있습니다. 그래서 Phantom Read 를 허용하지 않는 Serialization 의 속성을 가집니다. 문제는 이러한 모호성때문에 Insert 가 Lock wait 하는 재밌지만 미쳐버리는 상황이 발생합니다. 특히 기존에 Oracle 에 익숙한 사람들은 뜬금없는 Lock 과 Deadlock 앙상블에 맨붕이 오기도 합니다. 뭐 예를 들어 다음과 같은 경우죠. 

* Table T1 을 FULL Scan 하는 쿼리를 수행하는 트렌젝션이 있다면, 이 트렌젝션이 끝나기 전까지 어떤 트렌젝션도 T1에 데이터를 INSERT 할 수 없습니다. 
* INSERT INTO T1 SELECT * FROM T1 구문은 그 자체가 블락됩니다.  
* SELECT * FROM T1 WHERE C1 BETWEEN 1 AND 100 이라는 구문은 T1 테이블에 1 ~ 100 사이 어떤 값도 입력 , 수정될수 없도록 블럭합니다. GAP LOCK 이라고 합니다. 
* SELECT A.* FROM A LEFT OUTER JOIN B WHERE A.C1 = B.C1 WHERE A.C2 = 1 FOR UPDATE 이라는 쿼리가 있고 해당 쿼리의 결과가 No Rows 라도 해당 트렌젝션이 끝날때까지 B 테이블에 어떠한 값 입력도 할 수 없습니다.
* 내가 한번 값을 읽으면 다른 트렌젝션이 그 값을 바꾸었다는 걸을 알수 없습니다. 그래서 SELECT - FOR UPDATE 는 Exclusive Lock 을 설정하고 해당 Row의 최신 값을 가지고 오는 것으로 의미가 확장됩니다. Shared Lock 을 거는 LOCK IN SHARE MODE 라는 구문도 있는데 역시 최신값을 가지고 와서 다른 Transaction 이 못 바꾸게 합니다. 


MySQL 쪽에서도 이걸 바꿔보려고 [시도](http://www.tocker.ca/2015/01/14/proposal-to-change-replication-and-innodb-settings-in-mysql-5-7.html)는 한거 같은데, 호환성의 이슈로 반려당한걸로 보입니다. 사실 메이저 버젼 변경도 아니고, MySQL 과 같이 많이 사용되는 DBMS 의 기본값은 변경하기 상당히 까다롭습니다. 게다가 이렇게 데이터의 정합성에 영향을 미치는 기본값은 매우 변경하기 힘들죠. 


### 영향도 

사실 실제 운영되는 시스템에서 이 값 함부로 못바꿉니다. 특히 Concurrenct 한 Operation 이 자주 일어나는 시스템에서는 업무 고려를 일일히 해봐야합니다. 답이 달라 질 수 있고, 데이터가 의도하지 않은 상태로 바뀌어버릴 수 있습니다. 

그리고 Replication 을 사용할때 SBR ( Statement Based Replication)  을 사용하지 못합니다. RBR ( Row Based Replication) 만 사용할 수 있습니다. 당연합니다. 동시에 수행할 수 있는 트렌젝션의 Concurrency 가 늘었기 때문입니다. 

잘못 설정된 기본값이 이렇게 무섭습니다. 

## 마무리

장황했지만 두줄 요약입니다.  

* 최초 MySQL InnoDB 를 구성할때 transaction-isolation 값을 READ COMMITED 로 바꿔라. 
* 그러나 기존 운영시스템의 기본값은 바꾸지 마라. 완벽하게 어플리케이션을 통제하고 있지 못한 상태에서 바꾸면 잡기도 어려운 동시성 문제가 발생한다. 

이글의 메인 주제였던 왜 MySQL 은 Repeatable Read 가 기본일까 ? 라는 건 사실 답을 못찾았습니다. 그냥 개발자 취향인것으로 이해하고 있습니다. 
