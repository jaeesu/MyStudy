### jpa 도메인 상속 관계

* 상속 전략
@Inheritance(strategy = InheritanceType.XXX)
<br>
추상 클래스, 일반 클래스 어느 것을 상속해도 똑같이 진행된다.
<br>
글을 작성하기 위한 예시 : person(추상 클래스), female(Person을 상속받은 클래스), male(Person을 상속받은 클래스)<br>
1. joined<br>
   person, female, male 테이블이 생성되며, female과 male은 person과 외래 관계를 갖는다.<br>
    값을 insert(delete) 할 때는 상위(하위) 클래스에서 먼저 insert(delete) 한 후 하위(상위) 클래스에서 insert(delete) 한다.<br> 
   값을 찾을 때는 상위 클래스와 하위 클래스의 테이블을 조인한다.
    => 하위 클래스에 직접 id를 명시해주어도 된다. (@Id)<br>
    * 장점 : 테이블 정규화, 외래키 참조 무결성 제약조건 활용 가능, 저장공간 효율화
    * 단점 : 조회 시 조인으로 인한 성능 저하 & 조회 쿼리의 복잡함, insert, delete를 2번 씩 호출
   
   <br>
   <br>
3. table_per_class<br>
   하위 클래스가 완전히 별개의 테이블로 생성된다. person 테이블은 생성되지 않으며, 완전한 female, male 테이블이 생성된다.<br>
    insert, delete, find 시에도 테이블 단독으로 수행한다.
   * 장점 : 서브 타입의 구분이 명확해 처리할 때 효과적이면 not null 제약조건을 사용할 수 있다.
   * 여러 테이블을 함께 조회할 때 성능이 느리다(union sql) => 통합이 힘들다

<br>
<br>
3. single_table<br>
   모든 하위클래스를 person이라는 테이블로 만든다.
   male에만 있는 속성은 female 컬럼에서는 null로 나타난다.
    * 장점 : 조인이 없어 빠른 조회, 조회 쿼리의 단순함
    * 단점 : 자식 엔티티의 속성은 null 값, 서로 다른 속성을 하나의 테이블에 넣어서 테이블이 커질 수 있으며 불필요한
      속성을 검색하며 성능이 느려질 수 있다. 조회 성능을 문제 삼기 위해서는 임계점을 넘어야 하는데, 보통 그럴 일은 없다.

### 매핑 정보 상속
* @MappedSuperclass 공통 매핑 정보가 필요할 때 사용<br>
데이터베이스 입장을 생각하지 않고, 객체 입장에서의 상속만 받고 싶을 때, 공통 매핑 속성을 추상 클래스로 정의해 @MappedSuperclass 애노테이션을 붙여준다.

https://jaeesu.tistory.com/3
* 참고<br>
  https://jyami.tistory.com/23