- 소프트웨어에서 이름은 어디나 쓰인다. 변수, 함수, 인수, 클래스, 패키지에도 이름을 붙인다. 이외에도 많이 이름을 붙인다
- 이렇게 많이 사용하므로 이름을 잘 지으면 편하다. 이 장에선 이름을 잘 짓는 간단한 규칙을 몇 개 소개한다

### 의도를 분명히 밝혀라

- 여기선 의도가 분명한 이름이 정말 중요하단 사실을 거듭 강조한다. 좋은 이름을 지으려면 시간이 걸리지만, 좋은 이름으로 절약하는 시간이 훨씬 더 많다
- 그러므로 이름을 주의깊게 살펴 더 나은 이름이 떠오르면 개선하라
- 변수, 함수, 클래스명은 아래의 굵직한 질문에 모두 답해야 한다

> 변수(혹은 함수, 클래스)의 존재 이유는?  
> 수행 기능은?  
> 사용법은?

- 따로 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다

```java
int d; // 경과 시간(단위 : 날짜)
```

- 이름 d는 아무 의미도 드러나지 않는다. 경과 시간이나 날짜라는 느낌이 안 든다. 측정하려는 값, 단위를 표현하는 이름이 필요하다

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
- 다시 말해 코드 맥락이 코드 자체에 명시적으로 드러나지 않는다. 위 코드는 독자가 아래 정보를 안다고 가정한다

> theList에 뭐가 들었는가?  
> theList에서 0번 값이 왜 중요한가?  
> 값 4는 무슨 의미인가?  
> 함수가 반환하는 list1을 어떻게 쓰는가?

- 위 코드 샘플엔 이런 정보가 드러나지 않았다. 정보 제공은 충분히 가능했었다
- 지뢰찾기 게임을 만든다 가정한다. 그러면 theList가 게임판이다. theList를 gameBoard로 바꾼다
- 게임판에서 각 칸은 단순 배열로 표현한다. 배열 0번째 값은 칸 상태를 뜻한다. 값 4는 깃발이 꽂힌 상태를 가리킨다. 각 개념에 이름만 붙여도 코드가 상당히 나아진다

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

- 코드의 단순성은 변하지 않았다. 연산자 수와 상수 수, 들여쓰기 등 모두 동일하다. 그런데도 코드는 명확해졌다
- 더 나아가 int[]을 쓰는 대신 칸을 간단한 클래스로 만들어도 되겠다. isFlagged라는 좀 더 명시적인 함수를 써서 FLAGGED라는 상수를 감춰도 좋겠다

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

- 프로그래머는 코드에 그릇된 단서를 남기면 안 된다
- 나름대로 널리 쓰이는 의미가 있는 단어를 다른 의미로 써도 안 된다
- 예를 들어 hp, aix, sco는 변수명으로 부적합하다. 유닉스 플랫폼이나 유닉스 변종을 가리키는 이름이기 때문이다
- 직각 삼각형의 빗변(hypotenuse)을 구현할 때는 hp가 좋아보여도, hp는 독자에게 그릇된 정보를 제공한다
- 여러 계정을 그룹으로 묶을 때 실제 List가 아니면 accountList라 명명하지 않는다. 프로그래머에게 List란 단어는 특수한 의미다
- 서로 흡사한 이름을 쓰지 않게 주의한다
- 유사한 개념은 유사한 표기법을 사용한다. 이것도 정보다. 일관성이 떨어지는 표기법은 그릇된 정보다. 어떤 개발자는 객체에 달린 주석이나 클래스가 제공하는 메서드 목록을 안 보고 이름만 봐서 객체를 선택한다

---

### 의미 있게 구분하라

- 컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하는 개발자는 스스로 문제를 일으킨다
- 예를 들어 동일한 범위 안에선 다른 두 개념에 같은 이름을 적용하지 못한다. 그래서 한쪽 이름을 바꾸는 유혹에 빠진다
- 컴파일러를 통과하더라도 연속된 숫자를 덧붙이거나 불용어(noise word)를 추가하는 방식은 적절치 못하다. 이름이 달라야 한다면 의미도 달라져야 한다
- 연속적인 숫자를 덧붙인 이름(a1, a2 등)은 의도적 이름과 정반대다. 아무런 정보를 제공하지 못하는 이름일 뿐이다. 저자 의도가 드러나지 않는다

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
- 불용어를 추가한 이름 역시 아무 정보도 제공 못한다. Product라는 클래스가 있다고 가정하자. 다른 클래스를 ProductInfo 혹은 ProductData라 부른다면 개념을 구분하지 않은 채 이름만 달리한
  경우다. Info, Data는 a, an, the와 마찬가지로 의미가 불분명한 용어다
- 의미가 분명히 다르면 a, the 같은 접두어를 써도 상관없다. 모든 지역 변수는 a를 쓰고 모든 함수 인수는 the를 써도 되겠다. 요지는 zork라는 변수가 있다는 이유만으로 theZork라 명명해선 안
  된다는 말이다
- 불용어는 중복이다. 변수명에 variable이란 단어는 금물이다. 표 이름에 table이란 용어도 마찬가지다
- 명확한 관례가 없다면 변수 moneyAmount는 money와 구분이 안 된다. cusromerInfo는 customer와, accountData는 account와, theMessage는 message와 구분이
  안 된다. 읽는 사람이 차이를 알도록 이름을 지어라

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
- MAX_CLASSES_PER_STUDENT는 grep로 찾기가 쉽지만 숫자 7은 은근 까다롭다. 7이 들어가는 파일명, 수식이 모두 검색되기 때문이다
- 검색은 됐지만 7을 쓴 의도가 다른 경우도 있다. 상수가 여러 자리 숫자고 누군가 상수 내 숫자 위치를 바꿨다면 문제는 심각해진다. 상수에 버그가 있지만 검색으로 찾지 못한다
- e라는 문자도 변수명으로 부적합하다. e는 영어에서 가장 많이 쓰이는 문자다. 이런 관점에서 긴 이름이 짧은 이름보다 좋다. 검색하기 쉬운 이름이 상수보다 좋다
- 저자 개인적으로는 간단한 메서드에서 로컬 변수만 한 문자를 사용한다. 이름 길이는 범위 크기에 비례해야 한다
- 변수나 상수를 여러 곳에서 쓴다면 검색하기 쉬운 이름이 바람직하다. 아래 두 예제를 비교해보자

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

- 위 코드에서 sum이 별로 유용하진 않지만 최소한 검색이 가능하다. 이름을 의미있게 지으면 함수가 길어진다
- 하지만 WORK_DAYS_PER_WEEK를 찾기가 얼마나 쉬운가? 그냥 5를 쓴다면 5가 들어가는 이름을 모두 찾은 후 의미를 분석해 원하는 상수를 가려내야 한다

---

### 인코딩을 피해라

- 이름에 인코딩할 정보는 아주 많다. 유형이나 범위 정보까지 인코딩에 넣으면 그만큼 이름 해독이 어려워진다
- 대개 새로운 개발자가 익힐 코드량은 상당히 많다. 여기에 인코딩 '언어'까지 익히란 요구는 비합리적이다. 문제 해결에 집중하는 개발자에게 인코딩은 불필요한 부담이다

#### 헝가리식 표기법

- 이름 길이가 제한된 언어를 쓰던 옛날엔 어쩔 수 없이 이 규칙을 위반했다. 포트란은 첫 글자로 유형을 표현했고, 초창기 베이직은 글자 하나에 숫자 하나만 허용했다
- 자바 개발자는 변수명에 타입을 인코딩할 필요가 없다. 객체는 강한 타입(strongly-typed)이며 IDE는 코드를 컴파일하지 않고도 타입 오류를 감지할 정도로 발전했다. 따라서 이젠 헝가리식 표기법이나 기타 인코딩 방식이 방해가 될 뿐이다
- 변수, 함수, 클래스명이나 타입 바꾸기가 어려워지며 읽기도 어려워진다. 독자를 오도할 가능성도 커진다

```java
PhoneNumber phoneString;
// 타입이 바뀌어도 이름은 바뀌지 않는다
```

#### 멤버 변수 접두어

- 이제 멤버 변수에 m_이란 접두어를 붙일 필요도 없다. 클래스, 함수는 접두어가 필요없을 정도로 작아야 마땅하다
- 멤버 변수를 다른 색으로 표시하거나 눈에 띄게 보여주는 IDE를 써야 한다

```java
public class Part {
    private String m_dsc;   // 설명 문자열
  void setName(String name) {
      m_dsc = name;
  }
}
```

- 사람들은 접두어 또는 접미어를 무시하고 이름을 해독하는 방식을 재빨리 익힌다. 코드를 읽을수록 접두어는 관심 밖으로 밀려난다

#### 인터페이스 클래스와 구현 클래스

- 때로는 인코딩이 필요한 경우도 있다. 도형을 생성하는 abstract factory를 구현한다고 가정한다
- 이 팩토리는 인터페이스 클래스다. 구현은 구체 클래스(concrete class)에서 한다. 그럼 두 클래스명을 어떻게 지어야 좋을까?
- IShapeFactory와 ShapeFactory? 저자 개인적으로 인터페이스명은 접두어를 안 붙이는 게 좋다고 생각한다
- 옛날 코드에서 많이 쓰는 i는 주의흘 흐트리고 나쁘게는 과한 정보를 제공한다. 저자는 자신이 다루는 클래스가 인터페이스란 사실을 남에게 알리고 싶지 않았다
- 클래스 사용자는 그냥 ShapeFactory라고만 생각하면 좋겠다. 그래서 인터페이스 클래스명과 구현 클래스명 중 하나를 인코딩해야 한다면 구현 클래스를 선택한다
- ShapeFactoryImpl이나 CShapeFactory가 IShapeFactory보다 좋다

---

### 자기 기억력을 자랑하지 마라

- 독자가 코드를 보면서 변수명을 자신이 아는 이름으로 바꿔야 한다면 그 변수명은 바람직하지 못하다
- 이는 일반적으로 문제 영역, 해법 영역에서 쓰지 않는 이름을 선택했기 때문에 생기는 문제다
- 문자 하나만 쓰는 변수명은 문제가 있다. 루프에서 반복 횟수를 세는 i, j, k는 괜찮다. 단 루프 범위가 아주 작고 다른 이름과 충돌하지 않을 때만 괜찮다
- 전문가 프로그래머는 명료함이 최고라는 사실을 이해한다. 전문가 프로그래머는 자신의 능력을 좋은 방향으로 사용해 남들이 이해하는 코드를 내놓는다

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

- 생성자를 중복 정의할 때는 정적 팩토리 메서드를 사용한다. 메서드는 인수를 설명하는
이름을 사용한다