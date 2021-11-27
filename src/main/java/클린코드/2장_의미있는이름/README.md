- 소프트웨어에서 이름은 어디나 쓰인다. 변수, 함수, 인수, 클래스, 패키지에도 이름을 붙인다.
이외에도 많이 이름을 붙인다
- 이렇게 많이 사용하므로 이름을 잘 지으면 편하다. 이 장에선 이름을 잘 짓는 간단한 규칙을
몇 개 소개한다

### 의도를 분명히 밝혀라

- 여기선 의도가 분명한 이름이 정말 중요하단 사실을 거듭 강조한다. `좋은 이름을 지으려면 시간이
걸리지만, 좋은 이름으로 절약하는 시간이 훨씬 더 많다`
- `이름을 주의깊게 살펴 더 나은 이름이 떠오르면 개선하라`
- 변수, 함수, 클래스명은 아래의 굵직한 질문에 모두 답해야 한다

> 변수(혹은 함수, 클래스)의 존재 이유는?  
> 수행 기능은?  
> 사용법은?

- 따로 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다

```java
int d; // 경과 시간(단위 : 날짜)
```

- 이름 d는 아무 의미도 드러나지 않는다. 경과 시간이나 날짜라는 느낌이 안 든다.
- 측정하려는 값, 단위를 표현하는 이름이 필요하다

```java
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```

- 의도가 드러나는 이름을 지으면 코드 이해, 변경이 쉬워진다. 아래 코드는 무슨 일을 하는가?

```java
import java.util.ArrayList;

class Main {
    public List<int[]> getThem() {
        List<int[]> list1 = new ArrayList<int[]>();
        for (int[] x : theList) {
            if (x[0] == 4)
                list1.add(x);
            return list1;
        }
    }
}
```

- 코드가 하는 일을 짐작하기 어렵다. 복잡한 문장은 없다. 문제는 코드의 단순성이 아닌 함축성이다
- 다시 말해 코드 맥락이 코드 자체에 명시적으로 드러나지 않는다. 위 코드는 독자가 아래 정보를
안다고 가정한다

> theList에 뭐가 들었는가?  
> theList에서 0번 값이 왜 중요한가?  
> 값 4는 무슨 의미인가?  
> 함수가 반환하는 list1을 어떻게 쓰는가?

- 위 코드 샘플엔 이런 정보가 드러나지 않았다. 정보 제공은 충분히 가능했었다
- 지뢰찾기 게임을 만든다 가정한다. 그러면 theList가 게임판이다. theList를 gameBoard로 바꾼다
- 게임판에서 각 칸은 단순 배열로 표현한다. 배열 0번째 값은 칸 상태를 뜻한다
- 값 4는 깃발이 꽂힌 상태를 가리킨다. 각 개념에 이름만 붙여도 코드가 상당히 나아진다

```java
import java.util.ArrayList;

class Main {
    public static void main(String[] args) {
        public List<int[]> getFlaggedCells () {
            List<int[]> flaggedCells = new ArrayList<int[]>();
            for (int[] cell : gameBoard) {
                if (cell[STATUS_VALUE] == FLAGGED)
                    flaggedCells.add(cell);
                return flaggedCells;
            }
        }
    }
}
```

- 코드의 단순성은 변하지 않았다. 연산자 수와 상수 수, 들여쓰기 등 모두 동일하다.
그런데도 코드는 명확해졌다
- 더 나아가 int[]을 쓰는 대신 칸을 간단한 클래스로 만들어도 되겠다.
isFlagged라는 좀 더 명시적인 함수를 써서 FLAGGED라는 상수를 감춰도 좋겠다

```java
import java.util.ArrayList;

class Main {
    public static void main(String[] args) {
        public List<Cell> getFlaggedCells () {
            List<Cell> flaggedCells = new ArrayList<Cell>();
            for (Cell cell : gameBoard)
                if (cell.isFlagged())
                    flaggedCells.add(cell);
            return flaggedCells;
        }
    }
}
```

---

### 그릇된 정보를 피하라

- `프로그래머는 코드에 그릇된 단서를 남기면 안 된다`
- 나름대로 널리 쓰이는 의미가 있는 단어를 다른 의미로 써도 안 된다
- 예를 들어 hp, aix, sco는 변수명으로 부적합하다. 유닉스 플랫폼이나 유닉스 변종을 가리키는
이름이기 때문이다
- 직각 삼각형의 빗변(hypotenuse)을 구현할 때는 hp가 좋아보여도, hp는 독자에게 그릇된
정보를 제공한다
- 여러 계정을 그룹으로 묶을 때 실제 List가 아니면 accountList라 명명하지 않는다.
프로그래머에게 List란 단어는 특수한 의미다
- 서로 흡사한 이름을 쓰지 않게 주의한다
- 유사한 개념은 유사한 표기법을 사용한다. 이것도 정보다. 일관성이 떨어지는 표기법은 그릇된
정보다. 어떤 개발자는 객체에 달린 주석이나 클래스가 제공하는 메서드 목록을 안 보고 이름만
봐서 객체를 선택한다

---

### 의미 있게 구분하라

- `컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하는 개발자는 스스로 문제를 일으킨다`
- 예를 들어 동일한 범위 안에선 다른 두 개념에 같은 이름을 적용하지 못한다. 그래서 한쪽
이름을 바꾸는 유혹에 빠진다
- 컴파일러를 통과하더라도 연속된 숫자를 덧붙이거나 불용어(noise word)를 추가하는 방식은 
적절치 못하다. `이름이 달라야 한다면 의미도 달라져야 한다`
- 연속적인 숫자를 덧붙인 이름(a1, a2 등)은 의도적 이름과 정반대다. 아무런 정보를 제공하지
못하는 이름일 뿐이다. 저자 의도가 드러나지 않는다

```java
class Main {
    public static void main(String[] args) {
        public static void copyChars ( char a1[], char a2[]){
            for (int i = 0; i < a1.length; i++) {
                a2[i] = a1[i];
            }
        }
    }
}
```

- 함수 인수 이름으로 source, destination을 쓰면 코드 읽기가 더 쉬워진다
- 불용어를 추가한 이름 역시 아무 정보도 제공 못한다. Product라는 클래스가 있다고 가정하자.
다른 클래스를 ProductInfo 혹은 ProductData라 부른다면 개념을 구분하지 않은 채 이름만
달리한 경우다. Info, Data는 a, an, the와 마찬가지로 의미가 불분명한 용어다
- `의미가 분명히 다르면 a, the 같은 접두어를 써도 상관없다.` 모든 지역 변수는 a를 쓰고
모든 함수 인수는 the를 써도 되겠다
- 요지는 zork라는 변수가 있다는 이유만으로 theZork라 명명해선 안 된다는 말이다
- 불용어는 중복이다. 변수명에 variable이란 단어는 금물이다. 표 이름에 table이란 용어도
마찬가지다
- 명확한 관례가 없다면 변수 moneyAmount는 money와 구분이 안 된다. cusromerInfo는
customer와, accountData는 account와, theMessage는 message와 구분이 안 된다
- `읽는 사람이 차이를 알도록 이름을 지어라`

---

### 발음하기 쉬운 이름을 써라

- 발음하기 어려운 이름은 토론하기도 어렵다. 프로그래밍은 사회 활동이다

```java
import java.util.Date;

class DtaRcrd102 {
    private Date genymdhms;
    private Date modymdhms;
    private final String pszqint = "102";
}
```

```java
import java.util.Date;

class Customer {
    private Date generationTimeStamp;
    private Date modificationTimeStamp;
    private final String recordId = "102";
}
```

- 2번 코드는 지적인 대화가 가능해진다

### 검색하기 쉬운 이름을 써라

- 문자 하나를 사용하는 이름, 상수는 텍스트 코드에서 쉽게 눈에 띄지 않는다
- MAX_CLASSES_PER_STUDENT는 grep로 찾기가 쉽지만 숫자 7은 은근 까다롭다.
7이 들어가는 파일명, 수식이 모두 검색되기 때문이다
- 검색은 됐지만 7을 쓴 의도가 다른 경우도 있다. 상수가 여러 자리 숫자고 누군가 상수 내
숫자 위치를 바꿨다면 문제는 심각해진다. 상수에 버그가 있지만 검색으로 찾지 못한다
- e라는 문자도 변수명으로 부적합하다. e는 영어에서 가장 많이 쓰이는 문자다
- 이런 관점에서 `긴 이름이 짧은 이름보다 좋다. 검색하기 쉬운 이름이 상수보다 좋다`
- 저자 개인적으로는 간단한 메서드에서 로컬 변수만 한 문자를 사용한다. 이름 길이는 범위
크기에 비례해야 한다
- `변수나 상수를 여러 곳에서 쓴다면 검색하기 쉬운 이름이 바람직하다.` 아래 두 예제를 비교해보자

```java
class Main {
    public static void main(String[] args) {
        for (int j = 0; j < 34; j++) {
            s += (t[j] * 4) / 5;
        }
    }
}
```

```java
class Main {
    public static void main(String[] args) {
      int realDaysPerIdealDay = 4;
      int WORK_DAYS_PER_WEEK = 5;
      int sum = 0;
      for (int j = 0; j < NUMBER_OF_TASKS; j++) {
          int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
          int realTaskWeeks = (realTaskDays / WORK_DAYS_PER_WEEK);
          sum += realTaskWeeks;
      }
    }
}
```

- 위 코드에서 sum이 별로 유용하진 않지만 최소한 검색이 가능하다. 이름을 의미있게 지으면
함수가 길어진다
- 하지만 WORK_DAYS_PER_WEEK를 찾기가 얼마나 쉬운가? 그냥 5를 쓴다면 5가 들어가는 이름을
모두 찾은 후 의미를 분석해 원하는 상수를 가려내야 한다

---

### 인코딩을 피해라

- 이름에 인코딩할 정보는 아주 많다. 유형이나 범위 정보까지 인코딩에 넣으면 그만큼 이름
해독이 어려워진다
- 대개 새로운 개발자가 익힐 코드량은 상당히 많다. 여기에 인코딩 '언어'까지 익히란 요구는
비합리적이다. 문제 해결에 집중하는 개발자에게 인코딩은 불필요한 부담이다

#### 헝가리식 표기법

- 이름 길이가 제한된 언어를 쓰던 옛날엔 어쩔 수 없이 이 규칙을 위반했다. 포트란은 첫 글자로
유형을 표현했고, 초창기 베이직은 글자 하나에 숫자 하나만 허용했다
- 자바 개발자는 변수명에 타입을 인코딩할 필요가 없다. 객체는 강한 타입(strongly-typed)이며
IDE는 코드를 컴파일하지 않고도 타입 오류를 감지할 정도로 발전했다
- 따라서 이젠 헝가리식 표기법이나 기타 인코딩 방식이 방해가 될 뿐이다
- 변수, 함수, 클래스명이나 타입 바꾸기가 어려워지며 읽기도 어려워진다. 
독자를 오도할 가능성도 커진다

```java
PhoneNumber phoneString;
// 타입이 바뀌어도 이름은 바뀌지 않는다
```

#### 멤버 변수 접두어

- 이제 멤버 변수에 m_이란 접두어를 붙일 필요도 없다. 클래스, 함수는 접두어가 필요없을
정도로 작아야 마땅하다
- 멤버 변수를 다른 색으로 표시하거나 눈에 띄게 보여주는 IDE를 써야 한다

```java
public class Part {
    private String m_dsc;   // 설명 문자열
  void setName(String name) {
      m_dsc = name;
  }
}
```

- 사람들은 접두어 또는 접미어를 무시하고 이름을 해독하는 방식을 재빨리 익힌다.
코드를 읽을수록 접두어는 관심 밖으로 밀려난다

#### 인터페이스 클래스와 구현 클래스

- 때로는 인코딩이 필요한 경우도 있다. 도형을 생성하는 abstract factory를 구현한다고 가정한다
- 이 팩토리는 인터페이스 클래스다. 구현은 구체 클래스(concrete class)에서 한다.
그럼 두 클래스명을 어떻게 지어야 좋을까?
- IShapeFactory와 ShapeFactory? 저자 개인적으로 인터페이스명은 접두어를 안 붙이는 게
좋다고 생각한다
- 옛날 코드에서 많이 쓰는 i는 주의흘 흐트리고 나쁘게는 과한 정보를 제공한다.
저자는 자신이 다루는 클래스가 인터페이스란 사실을 남에게 알리고 싶지 않았다
- 클래스 사용자는 그냥 ShapeFactory라고만 생각하면 좋겠다. 그래서 인터페이스 클래스명과
구현 클래스명 중 하나를 인코딩해야 한다면 구현 클래스를 선택한다
- ShapeFactoryImpl이나 CShapeFactory가 IShapeFactory보다 좋다

---

### 자기 기억력을 자랑하지 마라

- `독자가 코드를 보면서 변수명을 자신이 아는 이름으로 바꿔야 한다면
그 변수명은 바람직하지 못하다`
- 이는 일반적으로 문제 영역, 해법 영역에서 쓰지 않는 이름을 선택했기 때문에 생기는 문제다
- 문자 하나만 쓰는 변수명은 문제가 있다. 루프에서 반복 횟수를 세는 i, j, k는 괜찮다.
단 루프 범위가 아주 작고 다른 이름과 충돌하지 않을 때만 괜찮다
- `전문가 프로그래머는 명료함이 최고라는 사실을 이해한다. 전문가 프로그래머는 자신의 능력을
좋은 방향으로 사용해 남들이 이해하는 코드를 내놓는다`

---

### 클래스명

- 클래스명, 객체명은 명사나 명사구가 적합하다
- Customer, WikiPage, Account, AddressParser 등이 좋은 예다
- Manager, Processor, Data, Info 등의 단어는 피하고 동사는 쓰지 않는다

---

### 메서드명

- 메서드명은 동사나 동사구가 적합하다
- postPayment, deletePage, save 등이 좋은 예다
- 접근자, 변경자, 조건자는 자바빈 표준에 따라 값 앞에 get, set, is를 붙인다
```java
class Main {
  public static void main(String[] args) {
    String name = employee.getName();
    customer.setName("mike");
    if (paycheck.isPosted()) {/* ... */}
  }
}
```

- `생성자를 중복 정의할 때는 정적 팩토리 메서드를 사용한다. 메서드는 인수를 설명하는
이름을 사용한다`
- 예를 들어서 아래 두 예제를 확인한다

```java
Complex fulcrumPoint = Complex.FromRealNumber(23.0);
```

- 위 코드가 아래 코드보다 좋다

```java
Complex fulcrumPoint = new Complex(23.0);
```

### 기발한 이름은 피하라

- 이름이 너무 기발하면 저자와 유머 감각이 비슷한 사람만, 농담을 기억하는 동안만 이름을 기억한다
- `재미난 이름보다 명료한 이름을 선택하라`
- 간혹 나름대로 재치를 발휘해 구어체, 속어를 이름으로 쓰는 사례가 있다. 특정 문화에서만 사용하는
농담은 피하는 게 좋다
- `의도를 분명하고 솔직하게 표현하라`

### 한 개념에 한 단어를 사용하라

- 추상적인 개념 하나에 단어 하나를 선택해 이를 고수한다
- 예를 들어 똑같은 메서드를 클래스마다 fetch, retrieve, get으로 제각각 부르면 혼란스럽다.
어느 클래스에서 어느 이름을 썼는지 기억하기 어렵다
- 현실에선 이름을 기억하기 위해 라이브러리를 작성한 회사, 그룹, 개인을 기억해야 하는 경우가 많다
- 안 그러면 헤더, 과거 코드 예제를 보느라 엄청난 시간을 소모하기 십상이다
- 이클립스, 인텔리제이 같은 최신 IDE는 문맥에 맞는 단서를 제공한다
- 예를 들어 객체를 사용하면 그 객체가 제공하는 메서드 목록을 보여준다. 하지만 목록은 보통
함수명과 매개변수만 보여줄 뿐 주석은 보여주지 않는다. 운이 좋다면 매개변수명도 보여준다
- 따라서 메서드명은 독자적, 일관적이어야 한다. 그래야 주석을 보지 않고도 프로그래머가 올바른
메서드를 선택한다
- 마찬가지로 동일 코드 기반에 controller, manager, driver를 섞어 쓰면 혼란스럽다.
DeviceManager와 ProtocolController는 근본적으로 어떻게 다른가? 어째서 둘 다 Controller
가 아닌가? 어째서 둘 다 Manager가 아닌가? 정말 둘 다 Driver가 아닌가?
- 이름이 다르면 독자는 당연히 클래스도 다르고 타입도 다를 거라 생각한다. 일관성 있는 어휘는
코드를 사용할 프로그래머가 반갑게 여길 선물이다

### 말장난 하지 마라

- 한 단어를 두 가지 목적으로 쓰지 마라. 다른 개념에 같은 단어를 사용하면 그건 말장난이다
- "한 개념에 한 단어를 사용하라"는 규칙을 따랐더니 예를 들어 여러 클래스에 add라는 메서드가
생겼다. 모든 add 메서드의 매개변수, 리턴값이 의미적으로 똑같다면 문제가 없다
- 하지만 때로는 프로그래머가 같은 맥락이 아닌데도 '일관성'을 고려해 add라는 단어를 선택한다
- 예를 들어 지금까지 구현한 add 메서드는 모두가 기존 값 2개를 더하거나 이어서 새로운 값을
만든다고 가정하자. 새로 작성하는 메서드는 집합에 값 하나를 추가한다. 이 메서드를 add라 불러도
괜찮은가? add라는 메서드가 많으므로 일관성을 지키려면 add라 불러야 하지 않을까?
- 하지만 새 메서드는 기존 add 메서드와 맥락이 다르다. 그러므로 insert, append라는 이름이
적당하다. 새 메서드를 add라 부른다면 이건 말장난이다
- `프로그래머는 코드를 최대한 이해하기 쉽게 짜야 한다.` 집중적인 탐구가 필요한 코드가
아니라 대충 훑어봐도 이해할 코드 작성이 목표다
- 의미를 해독할 책임이 독자에게 이쓴 논문 모델이 아니라 의도를 밝힐 책임이 저자에게 있는
잡지 모델이 바람직하다

### 해법 영역에서 가져온 이름을 사용하라

- 코드를 읽을 사람도 프로그래머란 사실을 명심한다. 그러므로 전산 용어, 알고리즘 이름, 패턴
이름, 수학 용어 등을 사용해도 괜찮다
- 모든 이름을 문제 영역(domain)에서 가져오는 정책은 현명하지 못하다. 같은 개념을 다른 이름으로
이해하던 동료들이 매번 고객에게 물어야 하기 때문이다
- VISITOR 패턴에 친숙한 프로그래머는 AccountVisitor라는 이름을 금방 이해한다. JobQueue를
모르는 프로그래머가 있을까? 프로그래머에게 익숙한 기술 개념은 아주 많다
- `기술 개념에는 기술 이름이 가장 적합한 선택이다`

### 문제 영역에서 가져온 이름을 사용하라

- 적절한 프로그래머 용어가 없다면 문제 영역에서 이름을 가져온다. 그러면 코드를 보수하는
프로그래머가 분야 전문가에게 의미를 물어 파악할 수 있다
- 우수한 프로그래머와 설계자라면 해법 영역과 문제 영역을 구분할 줄 알아야 한다. 문제 영역
개념과 관련이 깊은 코드라면 문제 영역에서 이름을 가져와야 한다

### 의미 있는 맥락을 추가하라

- 스스로 의미가 분명한 이름이 없지 않다. 하지만 대다수 이름은 그렇지 못하다
- 그래서 클래스, 함수, 이름 공간에 넣어 맥락을 부여한다. 모든 방법이 실패하면 마지막 수단으로
접두어를 붙인다
- 예를 들어 firstName, street, houseNumber, city, state, zipcode라는 변수가 있다.
변수를 훑어보면 주소란 사실을 금방 알아챈다. 하지만 어느 메서드가 state란 변수 하나만 쓴다면?
변수 state가 주소 일부란 사실을 금방 알아챌까?
- addr라는 접두어를 추가해 addrFirstName, addrLastName, addrState라 쓰면 맥락이
좀 더 분명해진다. 변수가 좀 더 큰 구조에 속한다는 사실이 적어도 독자들에게는 분명해진다
- 물론 Address라는 클래스를 만들면 더 좋다. 그러면 변수가 좀 더 큰 개념에 속한다는 사실이
컴파일러에게도 분명해진다
- 목록 2-1의 메서드를 보면 변수에 좀 더 의미 있는 맥락이 필요할까? 함수명은 맥락 일부만
제공하며, 알고리즘이 나머지 맥락을 제공한다. 함수를 끝까지 읽고 나야 number, verb,
pluralModifier라는 변수 3개가 '통계 추측(guess statistics)' 메시지에 쓰인다는 사실이
드러난다
- 불행히도 독자가 맥락을 유추해야 한다. 그냥 메서드만 훑어선 세 변수의 의미가 불분명하다

```java
class Main {
  public static void main(String[] args) {
    
    private void printGuessStatistics(char candidate, int count) {
        String number;
        String verb;
        String pluralModifier;
        if (count == 0) {
            number = "no";
            verb = "are";
            pluralModifier = "s";
        } else if (count == 1) {
            number = "1";
            verb = "is";
            pluralModifier = "";
        } else {
            number = Integer.toString(count);
            verb = "are";
            pluralModifier = "s";
        }
        String guessMessage = String.format("There %s %s %s",
                verb, number, candidate, pluralModifier);
        print(guessMessage);
    }
      
  }
}
```

- 일단 함수가 좀 길다. 그리고 세 변수를 함수 전반에서 사용한다. 함수를 작은 조각으로 쪼개고자
GuessStatisticsMessage라는 클래스를 만든 후 세 변수를 클래스에 넣었다
- 그러면 세 변수는 맥락이 분명해진다. 즉 세 변수는 확실하게 GuessStatisticsMessage에
속한다
- 이렇게 맥락을 개선하면 함수를 쪼개기가 쉬워져서 알고리즘도 명확해진다. 목록 2-2를 보라

```java
public class GuessStatisticsMessage {
    private String number;
    private String verb;
    private String pluralModifier;
    
    public String make(char candidate, int count) {
        createPluralDependentMessageParts(count);
        return String.format("There %s %s %s%s", verb, number, candidate,
                pluralModifier);
    }
    
    private void createPluralDependentMessageParts(int count) {
        if (count == 0)
            thereAreNoLetters();
        else if (count == 1)
            thereIsOneLetter();
        else
            thereAreManyLetters(count);
    }
    
    private void thereAreManyLetters(int count) {
        number = Integer.toString(count);
        verb = "are";
        pluralModifier = "s";
    }
    
    private void thereIsOneLetter() {
        number = "1";
        verb = "is";
        pluralModifier = "";
    }
    
    private void thereAreNoLetters() {
        number = "no";
        verb = "are";
        pluralModifier = "s";
    }
    
}
```

### 불필요한 맥락을 없애라

- 고급 휘발유 충전소(Gas Station Deluxe)라는 앱을 짠다고 가정한다
- 모든 클래스 이름을 GSD로 시작하겠단 생각은 전혀 바람직하지 못하다. 전봇대로 이 쑤시는 격이다
- IDE에서 G를 입력하고 자동완성 키를 누르면 IDE는 모든 클래스를 열거한다. 현명치 못하다
- IDE는 개발자를 지원하는 도구다. IDE를 방해할 이유는 없다
- 비슷한 예로 GSD 회계 모듈에 MailingAddress 클래스를 추가하면서 GSDAccountAddress로
이름을 바꿨다. 나중에 달느 고객 관리 프로그램에서 고객 주소가 필요하다. GSDAccountAddress
클래스를 사용할까? 이름이 적절한가? 이름 17자 중 10자는 중복이거나 부적절하다
- `일반적으론 짧은 이름이 긴 이름보다 좋다. 단, 의미가 분명한 경우에 한해서다.` 이름에 불필요한
맥락을 추가하지 않게 주의한다
- accountAddress와 customerAddress는 Address 클래스 인스턴스로는 좋은 이름이나 클래스
이름으로는 적합하지 못하다. Address는 클래스 이름으로 적합하다
- 포트 주소, MAC 주소, 웹 주소를 구분해야 한다면 PostalAddress, MAC, URI란 이름도
괜찮겠다. 그러면 의미가 좀 더 분명해진다. 바로 이것이 이름을 붙이는 이유 아니던가?

### 마치면서

- 좋은 이름을 선택하려면 설명 능력이 뛰어나야 하고 문화적 배경이 같아야 한다. 이게 제일 어렵다
- 좋은 이름을 선택하는 능력은 기술, 비즈니스, 관리 문제가 아니라 교육 문제다. 우리 분야
사람들이 이름 짓는 법을 제대로 익히지 못하는 이유가 여기에 있다
- 사람들이 이름을 바꾸지 않으려는 이유 하나는 다른 개발자가 반대할까 두려워서다. 우리들 생각은
다르다. 오히려 (좋은 이름으로 바꿔주면) 반갑고 고맙다. 우리들 대다수는 자신이 짠 클래스명과
메서드명을 모두 암기하지 못한다. 암기는 요즘 나오는 도구에게 맡기고 우리는 문장이나 문단처럼
읽히는 코드 아니면 (정보를 표시하는 최선의 방법이 항상 문장만은 아니므로) 적어도 표나 자료구조
처럼 읽히는 코드를 짜는 데만 집중해야 마땅하다
- 여느 코드 개선 노력과 마찬가지로 이름 역시 나름대로 바꿨다가는 누군가 질책할지도 모른다.
그렇다고 코드를 개선하려는 노력을 중단해선 안 된다
- 이 장에서 소개한 규칙 몇 개를 적용해 코드 가독성이 높아지는지 살펴봐라. 다른 사람이 짠
코드를 손본다면 리팩터링 도구를 사용해 문제 해결 목적으로 이름을 개선하라. 단기적 효과는 물론
장기적 이익도 보장한다