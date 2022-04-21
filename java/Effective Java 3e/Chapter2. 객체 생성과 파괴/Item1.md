## ***Item1. 생성자 대신 정적 팩터리 메서드를 고려하라***
<br>

### 정적 팩터리 메서드의 장점
<hr>

1. 이름을 가질 수 있다.<br>
   * 메서드의 이름으로 특성을 명확하게 나타낼 수 있다.
   * 매개변수가 다른 생성자를 여러 개 만드는 것 보다 명시적으로 사용하기 쉽다, 
    ```
        public Member(String id, String name, Integer age, String addr, String email) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.addr = addr;
        this.email = email;
    }

    public static Member from(String id, String name, Integer age, String addr, String email) {
        return new Member(
                id, name, age, addr,email
        );
    }
    public static Member fromDto(MemberDto dto) {
        return new Member(
                dto.getId(), dto.getName(), dto.getAge(), dto.getAddr(), dto.getEmail()
        );
    }
    ```

2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.
    * 불변 클래스는 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱해 재활용할 수 있다.<br>
        ex) Boolean.valueOf(boolean)
    * 플라이웨이트 패턴과 비슷 : Flyweight pattern, 데이터를 공유해 메모리를 적약하는 패턴, pool이 객체를 관리, 사용
    * 인스턴스 통제(istance-controlled) 클래스 : 반복되는 요청에 같은 객체를 반환하여 언제 어느 인스턴스를 살아 있게 할지 철저히 통제<br>
        => 통제하는 이유 : 싱글턴(singleton), 인스턴스화 불가(noninstantiable)로 만들 수 있다. 동치인 인스턴스가 단 하나뿐임을 보장.
3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
    * 반환할 객체의 클래스를 자유롭게 선택 가능<br>
    * 구현 클래스를 공개하지 않을 수 있다. (API를 작게 유지)<br>
    => ***인터페이스 기반 프레임워크를 만드는 핵심 기술*** : 반환 객체로 인터페이스를 다룬다.
4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
    * 반환 타입의 하위 타입이기만 하면 된다.
    * enumSet 클래스 : public 생성자 없이 정적 팩터리만 제공 => 원소의 개수에 따라 반환 타입이 달라진다. (RegularEnumSet, JumboEnumSet)
    => ***사용자는 두 클래스의 존재를 모른다.***
5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
    * 서비스 제공자 프레임워크 (service provider framework)를 만드는 근간 (ex. JDBC)
    * 제공자 : 서비스의 구현체
    => ***구현체들이 클라이언트에 제공하는 역할을 프레임워크가 통제 => 클라이언트를 구현체로부터 분리***
    <br>
    <br>
    * 서비스 제공자 프레임워크의 핵심 컴포넌트
        1. 서비스 인터페이스 (service interface) : 구현체의 동작 정의<br>
            ex. JDBC Connection
        2. 제공자 등록 API (provider registration API) : 제공자가 구현체를 등록할 때 사용<br>
            ex. DriverManager.registerDriver
        3. 서비스 접근 API (service access API) : 클라이언트가 서비스의 인스턴스를 얻을 때 사용<br>
            원하는 구현체의 조건을 명시할 수 있다. "유연한 정적 팩터리"의 실체<br>
            ex. Driver
        4. 서비스 제공자 인터페이스 (service provider interface) : 서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체를 설명

### 정적 팩터리 메서드의 단점
<hr>

1. 상속을 위해서는 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.
    상속보다 컴포지션을 사용하도록 유도.<br>
    불변 타입으로 만들려면 이 제약을 지켜야 한다는 것이 장점일 수 있다.
2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.<br>
    사용자는 정적 팩터리 메서드 방식 클래스를 인스턴스화할 방법을 알아내야 한다.
    * 정적 팩터리 메서드의 흔한 명명 방식<br>
        from | of | valueOf | instance | getInstance | create | newInstance | getType | newType | type





