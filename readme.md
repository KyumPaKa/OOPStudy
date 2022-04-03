# 인프런 강의(객체 지향 프로그래밍 입문, 최범균) 학습

### 객체
- 객체의 핵심 -> 기능 제공
  - 제공하는 기능으로 정의
  - 내부적으로 가진 필드(데이터)로 정의하지 않음 <br>

ex) 회원 객체
> 암호 변경하기 기능 <br>
> 차단 여부 확인하기 기능 <br>
```
pulbic Member {
  public void changePassword(String curPw, String newPw) {
    ...
  }
}
```

ex) 소리제어기
> 소리 크기 증가(감소)하기 기능
```
public class VolumeController {
  pulbic void increase(int inc) {
    ...
  }
  
  pulbic void decrease(int inc) {
    ...
  }
  
  pulbic int volume() {
    ...
  }
}
```

- 기능 명세
  - 메서드를 이요해서 기능 명세(이름, 파라미터 결과로 구성) <br>

- 객체와 객체
  - 객체와 객체는 기능(메서드)을 사용해서 연결
  - 메시지: 객체간의 상호 작용 시 주고 받는 것

* get, set 메서드만 있는것은 객체보단 구조체


### 캡슐화
- 데이터와 관련 기능을 묶는 것
- 기능의 구현을 외부에 감추는 것
- 정보 은닉이 포함
- 외부의 영향 없이 객체 내부 구현 변경 가능
- Tell, Dont't Ask!
  - 데이터 달라 하지 말고 해달라고 하기 <br>

ex) 캡슐화 예시 <br>
캡슐화 전
```
if(acc.getMembership() == REGULAR) {
  ...
}
```
캡슐화 후
```
if(acc.hasRegularPermission()) {
  ...
}
```
- 캡슐화 시도는 기능에 대한 의도 이해를 높임
- 캡슐화를 위한 규칙(Demeter's Law)
  - 메서드에서 생성한 객체의 메서드만 호출
  - 파라미터로 받은 객체의 메서드만 호출
  - 필드로 참조하는 객체의 메서드만 호출


### 다형성과 추상화
- 다형성(Polymorphism): 한 객체가 여러 타입을 갖는 것 <br>

ex)
```
public class Timer {
  public void start() {..}
  public void stop() {..}
}

public interface Rechargeable {
  void charge();
}
```
```
// 상속, 확장
public class IotTimer extends Timer implements Rechargeable {
  public void charge() {..}
}
```
```
IotTimer it = new IotTimer();
it.start();
it.stop();

Timer t = it;
t.start();
t.stop();

Rechargeable r = it;
r.charge();
```

- 추상화(Abstraction): 데이터나 프로세스 등을 의미가 비슷한 개념이나 의미 있는 표현으로 정의하는 과정 <br>
  - 여러 구현 클래스를 대표하는 상위 타입 도출
  - 흔히 인터페이스 타입으로 추상화
ex) 서로 다른 구현 추상화 <br>
SCP로 파일 업로드 <br>
HTTP로 데이터 전송      ---추상화--->       푸시 발송 <br>
DB 테이블에 삽입 <br>
  - 추상 타입은 구현을 감춤(기능의 구현이 아닌 의도를 잘 나타냄)
  - 소스 수정의 유연성 증가가 장점


### 상속보단 조립
- 상속의 단점
  - 상위 클래스 변경 어려움
  - 클래스 파일 증가
  - 상속의 오용(원치 않는 메서드 상속되어 잘못된 사용)
- 조립
  - 여러 객체를 묶어서 더 복잡한 기능을 제공
  - 보통 필드로 다른 객체를 참조하는 방식으로 조립, 객체를 필요 시점에 생성(구함)
- 상속보단 조립으로 가능한지 검토
- 진짜 하위 타입인 경우에만 상속 사용


### 기능과 책임 분리
- 기능은 곧 책임이므로 분리하여 분배해야함
- 책임 분배/분리 방법
  - 패턴적용
    - 전형적인 역할 분리(MVC, AOP, GoF..)
  - 계산 기능 분리 <br>

분리 전
```
Member mem = memberRepository.findOne(id);
Product prod = productRepository.findOne(prodId);

int payAmount = prod.price() * orderReq.getAmount();
double pointRate = 0.01;
if(mem.getMembership() == GOLD) {
  pointRate = 0.03;
} else if(mem.getMembership() == SILVER) {
  pointRate = 0.02;
}
if(isDoublePointTarget(prod)) {
  pointRate *= 2;
}

int point = (int) (payAmount * pointRate);
```
분리 후
```
Member mem = memberRepository.findOne(id);
Product prod = productRepository.findOne(prodId);

int payAmount = prod.price() * orderReq.getAmount();
PointCalculator cal = new PointCalculator(payAmount, mem.getMembership(), prod.getId());
int point = cal.calculate();

public class PointCalculator {
  public int calculate() {
    double pointRate = 0.01;
    if(mem.getMembership() == GOLD) {
      pointRate = 0.03;
    } else if(mem.getMembership() == SILVER) {
      pointRate = 0.02;
    }
    if(isDoublePointTarget(prod)) {
      pointRate *= 2;
    }
    
    int point = (int) (payAmount * pointRate);
    return (int) (payAmount * pointRate);
  }
}
```
  - 외부 연동 분리
    - 네트워크, 메시징, 파일 등 연동 처리 코드 분리
  - 조건별 분기는 추상화
    - 연속적인 if-else는 추상화 고민

- 의도가 잘 드러나느 이름을 사용
- 역할 분리가 잘 되면 테스트도 용이해짐


### 의존과 DI
- 의존
  - 기능 구현을 위해 다른 구성 요소를 사용하는 것
  - ex) 객체 생성, 메서드 호출, 데이터 사용
  - 의존은 변경이 전파될 가능성을 의미
  - 순환의존 가능성 존재
  - 객체를 직접 생성하지 않는 방법
    - 팩토리, 빌더
    - 의존 주입(Dependency Injection)
    - 서비스 로케이터(Service Locator)
- 의존 주입(Dependency Injection)
  - 외부에서 의존 객체를 주입(성성자나 메서드를 이용해서 주입)
  - 조립기가 객체 생성, 의존 주입을 처리
    - ex) Spring FrameWork
- DI 장점
  - 의존 대상이 바뀌면 설정만 변경하면 됨
  - 의존 객체 없이 대역 객체를 사용해서 테스트 가능


### DIP
- 고수준 모듈
  - 의미 있는 단일 기능을 제공
  - 사우이 수준의 정책 구현
- 저수준 모듈
  - 고수준 모듈의 기능을 구현하기 위해 필요한 하위 기능의 실제 구현 <br>

ex) 수정한 도면 이미지를 NAS에 저장하고 측정 정보를 DB 테이블에 저장하고 수정 의뢰 정보를 DB에 저장하는 기능
| 고수준 | 저수준 |
|:----:|:----:|
| 도면이미지를 저장 | NAS에 이미지를 저장 |
| 측정 정보를 저장 | MEAS_INFO 테이블에 저장 |
| 도면 수정 의뢰 | BP_MOD_REQ 테이블에 저장 |

- 의존 역전 원칙(DEPENDENCY INVERSION PRINCIPLE, DIP)
  - 고수준 모듈은 저수준 모듈의 구현에 의존하면 안 됨
  - 저수준 모듈은 고수준 모듈에서 정의한 추상타입에 의존해야함
  - 고수준 입장에서 저수준 모듈을 추상화해야함, 구현 입장에서 추상화하면 안됨
  - 객체간의 유연성을 높임
