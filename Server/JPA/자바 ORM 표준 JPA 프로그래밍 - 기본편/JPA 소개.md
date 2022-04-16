
### 1. SQL 중심적인 개발의 문제점

현재는 객체를 관계형 DB에 정리하는 시대로 관계형 DB가 이해할 수 있는 SQL이 무조건 필요하다. 이 떄 객체 중심이 아니라 SQL 중심으로 개발이 이루어질 떄 문제가 발생한다.
1. 반복적인 crud를 개발자가 무한 반복하여 sql을 작성하면 시간, 개발 생산성, 유지 보수의 측면에서 좋지 않다. 객체와 sql 변환의 무한 반복이다.
    ```
    public class Memeber {
        private String memberId;
        private String name;
    }

    //sql
    insert into member(member_id, name) value (..., ...);
    select * from memeber m;
    update member set member_id="...";

    //JPA
    em.persist(memeber);
    em.find(member, id);
    member.setId(...);
    ```
    위와 같은 예시에서 sql보다 JPA를 이용했을 때 코드도 짧고, 보기에 좋다. 실제로 age라는 필드를 추가한다고 하면, sql은 필드를 하나씩 모두 추가해줘야 하지만, JPA에서는 정말 "필드만" 추가하면 된다.

2. 패러다임의 불일지 : 객체 <-> 관계형 데이터베이스<br>
    관계형 데이터베이스의 목적 : 데이터를 정규화하여 보관<br>
    객체의 목적 : 필드나 메서드를 캡슐화하여 사용<br>
    (객체 지향 프로그래밍은 추상화와 캡슐화, 정보은닉, 상속, 다형성 등 시스템의 복잡성을 제어할 수 있는 다양한 장치들을 제공한다.)<br>

    * 객체와 관계형 데이터베이스의 차이
        1. 상속<br>
            데이터베이스에는 상속 관계가 존재하지 않는다.
            ![](./../../../images/2022-04-16-20-17-04.png)
            (그나마 상속과 유사한 형식의 데이터베이스, 하지만 동작 방식은 전혀 다르다.)
        2. 연관관계<br>
            객체 지향 언어 : 객체 참조<br>
            관계형 데이터베이스 : pk, fk를 이용하여 조인
            ![](./../../../images/2022-04-16-20-22-12.png)
            (객체의 경우, Member는 Team을 조회할 수 있지만, Team은 Member를 조회할 수 없다. 테이블의 경우, 외래키로 조인하기 때문에 양쪽에서 서로를 조회할 수 있다.)

        3. 데이터 타입
        4. 데이터 식별 방법
3. 객체 그래프 탐색
    처음 실행하는 sql에 따라 탐색 범위가 정해진다. (select member_id from member;) 쿼리로 조회된 결과값에서 추후에 name을 조회하기 위해서는 다시 sql을 작성해야 한다. <br>
    언제나 name을 조회할 수 있다는 보장이 없기 때문에 객체에 대한 신뢰도가 떨어진다.<br>
    레이어드 아키텍처에서 진정한 의미의 계층 분할이 어렵다. 자바 컬렉션은 항상 동일한 객체임을 보장한다.<br>

객체답게 모델링 할수록 매핑 작업만 늘어나며, 매핑이 용이하게 하기 위해서는 여러 도메인을 한 객체로 저장해야 한다. 이런 문제 때문에 "객체를 자바 컬렉션에 저장하는 DB에 저장"할 수 있는 JPA가 고안되었다.

### 2. JPA 소개
* JPA : Java Persistence API<br>
* 자바 진영의 orm 기술 표준<br>
객체와 관계형 데이터베이스는 각자의 방식대로 설계하고 orm 프레임워크가 중간에서 매핑 역할을 한다. 따라서 orm은 객체와 rdb 두 기둥 위에 서 있는 기술이다.

1. JPA의 동작<br>
   JPA는 애플리케이션과 JDBC 사이에서 동작한다. orm이 객체와 관계형 데이터베이스의 패러다임 불일치 문제를 해결.
    ![](./../../../images/2022-04-16-20-32-04.png)
    * 저장
        ![](./../../../images/2022-04-16-20-33-35.png)
    * 조회
        ![](./../../../images/2022-04-16-20-33-49.png)
2. JPA의 등장
   EJB (엔티티 빈, 자바 표준) -> hibernate (오픈 소스) -> JPA(자바 표준)<br>
   ![](./../../../images/2022-04-16-20-35-28.png)

3. JPA를 사용해야 하는 이유
   * SQL 중심적인 개발에서 객체 중심으로 개발(생산성) -> crud의 간소화
   * 유지보수 -> 기존에는 필드 수정 시, 모든 sql 수정
   * 패러다임의 불일치 해결
        1. JPA와 상속
        2. JPA와 연관관계
        3. JPA와 객체 그래프 탐색 (신뢰, 자유로운 탐색)
        4. JPA와 비교하기 (동일한 트랜잭션에서는 동일한 객체임을 보장)
   * 성능
        1. 1차 캐시와 동일성(identity) 보장
            1. 같은 트랜잭션 안에서는 같은 엔티티를 반환
            2. DB Isolation Level이 Read Commit이어도 애플리케이션에서 Repeatable Read 보장
        2. 트랜잭션을 지원하는 쓰기 지연(transactional write-behind) 
            1. 트랜잭션을 커밋할 때까지 INSERT SQL을 모음 (버퍼링)
            2. JDBC BATCH SQL 기능을 사용해서 한번에 SQL 전송
            3. UPDATE, DELETE로 인한 로우(ROW)락 시간 최소화
            4. 트랜잭션 커밋 시 UPDATE, DELETE SQL 실행하고, 바로 커밋
        3. 지연 로딩(Lazy Loading)
           1. 지연로딩 : 객체가 실제 사용될 때 로딩
                멤버 조회 : select * from member ...
                member.getTeam() : select * from Team ...
           2. 즉시 로딩: JOIN SQL로 한번에 연관된 객체까지 미리 조회
                select m.*, t.* from member m join team t ...


### 용어 정리
* orm : object relational mapping(객체 관계 매핑)