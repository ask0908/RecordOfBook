- 어떤 프로그램이든 가장 기본적인 단위가 함수다. 이 장은 함수를 잘 만드는
법을 소개한다
- 목록 3-1의 코드를 보라. FitNesse(오픈 소스 테스트 도구)에서 긴 함수를
찾기는 어렵다. 하지만 한동안 조사한 끝에 다음 함수를 발견했다
- 길이가 길 뿐 아니라 중복된 코드, 괴상한 문자열, 낯설고 모호한 자료유형과
API가 많다

```java
class HtmlUtil {
    public static String testableHtml(PageData pageData,
                                      boolean includeSuiteSetUp
    ) throws Exception {
        WikiPage wikiPage = pageData.getWikiPage();
        StringBuffer buffer = new StringBuffer();
        if (pageData.hasAttribute("Test")) {
            if (includeSuiteSetUp) {
                WikiPage suiteSetup = PageCrawlerImpl.getInheritedPage(
                        SuiteResponder.SUITE_SETUP_NAME, wikiPage
                );
                if (suiteSetup != null) {
                    WikiPagePath pagePath = suiteSetup.getPageCrawler()
                            .getFullPath(suiteSetup);
                    String pagePathName = PathParser.render(pagePath);
                    buffer.append("!include -setup .")
                            .append(pagePathName)
                            .append("\n");
                }
            }
            WikiPage setup = PageCrawlerImpl.getInheritedPage("SetUp", wikiPage);
            if (setup != null) {
                WikiPagePath setupPath = wikiPage.getPageCrawler().getFullPath(setup);
                String setupPathName = PathParser.render(setupPath);
                buffer.append("!include -setup .")
                        .append(setupPathName)
                        .append("\n");
            }
        }
        buffer.append(pageData.getContent());
        if (pageData.hasAttribute("Test")) {
            WikiPage teardown = PageCrawlerImpl.getInheritedPage("TearDown", wikiPage);
            if (teardown != null) {
                WikiPathPath tearDownPath = wikiPage.getPageCrawler().getFullPath(teardown);
                String tearDownPathName = PathParser.render(tearDownPath);
                buffer.append("!include -teardown .")
                        .append(setupPathName)
                        .append("\n");
            }
            if (includeSuiteSetUp) {
                WikiPage suiteTearDown = PageCrawlerImpl.getInheritedPage(
                        SuiteResponder.SUITE_TEARDOWN_NAME,wikiPage
                );
                if (suiteTearDown != null) {
                    WikiPagePath pagePath = suiteTearDown.getPageCrawler().getFullPath(suiteTearDown);
                    String pagePathName = PathParser.render(pagePath);
                    buffer.append("!include -teardown .")
                            .append(setupPathName)
                            .append("\n");
                }
            }
        }
        pageData.setContent(buffer.toString());
        return pageData.getHtml();
    }
}
```

- 코드를 이해하겠는가? 아마 아니리라. 추상화 수준도 너무 다양하고 코드도
너무 길다. 두 겹으로 중첩된 if문은 이상한 플래그를 확인하고 이상한 문자열을
사용하며, 이상한 함수를 호출한다
- 하지만 메서드 몇 개를 추출하고, 이름 몇 개를 바꾸고, 구조를 조금 변경했더니
목록 3-2가 나왔다. 아래 코드는 함수 의도를 코드 9줄로 표현한다

```java
class HtmlUtil {
    public static String renderPageWithSetupsAndTearDowns(PageData pageData,
                                                          boolean isSuite
    ) throws Exception {
        boolean isTestPage = pageData.hasAttribute("Test");
        if (isTestPage) {
            WikiPage testPage = pageData.getWikiPage();
            StringBuffer newPageContent = new StringBuffer();
            includeSetupPages(testPage, newPageContent, isSuite);
            newPageContent.append(pageData.getContent());
            includeTearDownPages(testPage, newPageContent, isSuite);
            pageData.setContent(newPageContent.toString());
        }
        return pageData.getHtml();
    }
}
```

- FitNesse에 익숙하지 않아 코드를 이해하긴 어렵겠지만 그래도 함수가
설정(setup) 페이지와 해제(teardown) 페이지를 테스트 페이지에 넣은 후,
해당 테스트 페이지를 HTML로 렌더링한다는 사실은 짐작할 것이다
- JUnit에 익숙하다면 이 함수가 일종의 웹 기반 프레임워크에 속한단 사실도
짐작하리라. 여러분 짐작이 옳다. 목록 3-1에선 파악하기 어려웠던 정보가
목록 3-2에선 쉽게 드러난다
- 목록 3-2 함수가 읽기 쉽고 이해하기 쉬운 이유는 뭘까? 의도를 분명히
표현하는 함수를 어떻게 구현할 수 있을까? 함수에 어떤 속성을 부여해야
처음 읽는 사람이 프로그램 내부를 직관적으로 파악할 수 있을까?

### 작게 만들어라!

- 함수를 만드는 첫째 규칙은 '작게!'다. 둘째 규칙은 '더 작게!'다
- 이 규칙은 근거를 대기 곤란하다. 함수가 작을수록 더 좋다는 증거나 자료를
제시하기도 어렵다. 하지만 저자는 지난 40여년 동안 온갖 크기로 함수를
구현해 봤다. 거의 3,000줄에 육박하는 끔찍한 함수도 짰다. 100줄~300줄에
달하는 함수, 20~30줄 정도인 함수도 짰다. 지금까지의 경험을 바탕으로, 오랜
시행착오를 바탕으로 저자는 작은 함수가 좋다고 확신한다
- 그렇다면 얼마나 짧아야 좋을까? 켄트 백이 저자에게 Sparkle이란 자바
프로그램을 보여줬는데 화면에 반짝이를 뿌려주는 프로그램이었다
- 이 프로그램의 모든 함수는 2~4줄 정도였다. 각 함수가 너무도 명백했다.
각 함수가 이야기 하나를 표현했고, 멋지게 다음 무대를 준비했다
- 함수가 얼마나 짧아야 하냐고? 일반적으로 목록 3-2보다 짧아야 한다. 사실
목록 3-2는 목록 3-3(아래 코드)으로 줄여야 마땅하다

```java
class HtmlUtil {
    public static String renderPageWithSetupsAndTearDowns(PageData pageData,
                                                          boolean isSuite
    ) throws Exception {
        if (isTestPage(pageData))
            includeSetupAndTearDownPages(pageData, isSuite);
        return pageData.getHtml();
    }
}
```

#### 블록과 들여쓰기

- 다시 말해, `if문, else문, while문 등에 들어가는 블록은 한 줄이어야 한다는
의미다.` 대개 거기서 함수를 호출한다
- 그러면 바깥을 감싸는 함수(enclosing function)가 작아질 뿐 아니라, 블록
안에서 호출하는 함수명을 적절히 짓는다면 코드를 이해하기도 쉬워진다
- 이 말은 `중첩 구조가 생길만큼 함수가 커져서는 안 된다는 뜻이다. 그러므로
함수에서 들여쓰기 수준은 1단이나 2단을 넘어서면 안 된다. 그래야 함수는
읽고 이해하기 쉬워진다`

### 한 가지만 해라

- 목록 3-1은 여러 가지를 처리한다. 버퍼를 생성하고, 페이지를 가져오고,
상속된 페이지를 검색하고, 경로를 렌더링하고, 불가사의한 문자열을 덧붙이고,
HTML을 생성한다. 목록 3-1은 여러가지를 처리하느라 아주 바쁘다
- 반면 목록 3-3은 한 가지만 처리한다. 설정 페이지, 해제 페이지를 테스트
페이지에 넣는다
- 다음은 지난 30여년 동안 여러 가지 다양한 표현으로 프로그래머들에게 주어진
충고다

> 함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한가지만을 해야 한다

- 이 충고에서 문제라면 그 '한 가지'가 뭔지 알기가 어렵다는 것이다. 목록
3-3은 한 가지만 하는가? 3가지를 한다고 주장할 수도 있다

1. 페이지가 테스트 페이지인지 확인한다
2. 그렇다면 설정 페이지, 해제 페이지를 넣는다
3. 페이지를 HTML로 렌더링한다

- 위에서 언급하는 3단계는 지정된 함수 이름 아래에서 추상화 수준이 하나다
- 함수는 간단한 TO 문단으로 기술할 수 있다

> TO RenderPageWithSetupsAndTearDowns, 페이지가 테스트 페이지인지  
> 확인한 후, 테스트 페이지라면 설정 페이지와 해제 페이지를 넣는다  
> 테스트 페이지든 아니든 HTML로 렌더링한다

- 지정된 함수명 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는
한 가지 작업만 한다. 어쨌든 우리가 함수를 만드는 이유는 큰 개념을(다시
말해 함수명을) 다음 추상화 수준에서 여러 단계로 나눠 수행하기 위해서다
- 목록 3-1은 다양한 추상화 수준에서 여러 단계를 처리한다. 그러므로 함수는
여러 작업을 한다. 목록 3-2도 추상화 수준이 둘이다. 그래서 목록 3-3으로
축소가 가능했다. 하지만 의미를 유지하면서 목록 3-3을 더 이상 줄이기란
불가능하다. if문을 includeSetupsAndTearDownsIfTestPate라는 함수로
만든다면 똑같은 내용을 다르게 표현할 뿐 추상화 수준은 바뀌지 않는다
- 따라서 함수가 '한 가지'만 하는지 판단하는 방법이 하나 더 있다. 단순히
다른 표현이 아니라 의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는
여러 작업을 하는 셈이다

#### 함수 내 섹션

- 90쪽 목록 4-7을 본다. generatePrimes()는 declarations, initializations,
sieve라는 3개 섹션으로 나눠진다. 한 함수에서 여러 작업을 한다는 증거다
- `한 가지만 하는 함수는 자연스럽게 섹션으로 나누기 어렵다`

### 함수 당 추상화 수준은 하나로!

- 함수가 확실히 '한 가지' 작업만 하려면 함수 내 모든 문장의 추상화 수준이
동일해야 한다. 목록 3-1은 이 규칙을 확실히 위반한다
- getHtml()은 추상화 수준이 아주 높다. 반면 String pagePathName = 
PathParser.render(pagePath);는 추상화 수준이 중간이다. 그리고 append("\n")와
같은 코드는 추상화 수준이 아주 낮다
- 한 함수 내에 추상화 수준을 섞으면 코드를 읽는 사람이 헷갈린다. 특정 표현이
근본 개념인지 아니면 세부사항인지 구분하기 어려운 탓이다
- 문제는 이 정도로 그치지 않는다. 근본 개념과 세부사항을 뒤섞기 시작하면 깨진
창문처럼 사람들이 함수에 세부사항을 점점 더 추가한다

#### 위에서 아래로 코드 읽기 : 내려가기 규칙

- 코드는 위에서 아래로 이야기처럼 읽혀야 좋다. 한 함수 다음에는 추상화 수준이
한 단계 낮은 함수가 온다. 즉, 위에서 아래로 프로그램을 읽으면 함수 추상화
수준이 한 번에 한 단계씩 낮아진다. 저자는 이걸 내려가기 규칙이라 부른다
- 다르게 표현하면 일련의 TO 문단을 읽듯이 프로그램이 읽혀야 한다는 의미다
- 여기서 각 TO 문단은 현재 추상화 수준을 설명하며, 이어지는 아래 단계 TO
문단을 참고한다

> TO 설정 페이지와 해제 페이지를 포함하려면, 설정 페이지를 포함하고, 테스트 페이지 내용을 포함하고, 해제 페이지를 포함한다  
> TO 설정 페이지를 포함하려면, 슈트이면 슈트 설정 페이지를 포함한 후 일반 설정 페이지를 포함한다  
> TO 슈트 설정 페이지를 포함하려면, 부모 계층에서 "SuiteSetUp" 페이지를 찾아 include 문과 페이지 경로를 추가한다
> TO 부모 계층을 검색하려면....

- 하지만 추상화 수준이 하나인 함수를 구현하기란 쉽지 않다. 많은 프로그래머가 곤란을 겪는다
- 그렇지만 매우 중요한 규칙이다. 핵심은 짧으면서도 '한 가지'만 하는 함수다. 위에서 아래로 읽어내려 가듯이 코드를 구현하면
추상화 수준을 일관되게 유지하기가 쉬워진다
- 이 장 마지막에 나오는 목록 3-7을 봐라. 여기서 설명한 원칙을 사용해 testableHtml()을 완전히 재구성했다
- 코드를 살펴보면 각 함수는 다음 함수를 소개한다. 또한 각 함수는 일정한 추상화 수준을 유지한다

### switch문

- switch문은 작게 만들기 어렵다. 당연히 if/else가 여럿 이어지는 구문도 포함된다. case 분기가 단 2개인 switch문도 저자 취향에는
너무 길며, 단일 블록이나 함수를 선호한다
- 또한 '한 가지' 작업만 하는 switch문도 만들기 어렵다. 본질적으로 switch문은 N가지를 처리한다
- 불행하게도 switch문을 완전히 피할 방법은 없다. 하지만 각 switch문을 저차원 클래스에 숨기고 절대로 반복하지 않는 방법은 있다
- 물론 다형성을 사용한다
- 목록 3-4를 보라. 직원 유형에 따라 다른 값을 계산해 반환하는 함수다

```java
class Payroll {
    public Money calculatePay(Employee e) throws InvalidEmployeeType {
        switch (e.type) {
            case COMMISSIONED:
                return calculateCommissionedPay(e);
            case HOURLY:
                return calculateHourlyPay(e);
            case SALARIED:
                return calculateSalariedPay(e);
            default:
                throw new InvalidEmployeeType(e.type);
        }
    }
}
```

- 위 함수에는 몇 가지 문제가 있다. 첫째, 함수가 길다. 새로운 직원 유형을 추가하면 더 길어진다
- 둘째, '한 가지' 작업만 수행하지 않는다. 셋째, SRP(Single Responsibility Principle, 단일 책임 원칙)을 위반한다. 코드를 변경할 이유가 여럿이기 때문이다
- 넷째, OCP(Open Closed Principle, 개방-폐쇄 원칙)을 위반한다. 새로운 직원 유형을 추가할 때마다 코드를 변경하기 때문이다
- 하지만 가장 심각한 문제라면 위 함수와 구조가 동일한 함수가 무한정 존재한다는 사실이다. 예를 들어 다음과 같은 함수가 가능하다

```java
isPayday(Employee e, Date date);
```

혹은

```java
deliverPay(Employee e, Money pay);
```

- 가능성은 무한하다. 그리고 모두가 똑같이 유해한 구조다
- 이 문제를 해결한 코드가 목록 3-5다. 목록 3-5는 switch문을 추상 팩토리에 숨겨 아무에게도 보여주지 않는다
- 팩토리는 switch문을 사용해 적절한 Employee 파생 클래스의 인스턴스를 생성한다
- calculatePay, isPayday, deliverPay 등 함수는 Employee 인터페이스를 거쳐 호출된다. 그러면 다형성으로 인해 실제 파생 클래스의 함수가 호출된다

```java
// 목록 3-5 Employee And Factory
public abstract class Employee {
    public abstract boolean isPayday();
    public abstract Money calculatePay();
    public abstract void deliverPay(Money pay);
}

public interface EmployeeFactory {
    Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}

public class EmployeeFactoryImpl implements EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
        switch (r.type) {
            case COMMISSIONED:
                return new CommissionedEmployee(r);
            case HOURLY:
                return new HourlyEmployee(r);
            case SALARIED:
                return new SalariedEmployee(r);
            default:
                throw new InvalidEmployeeType(r.type);
        }
    }
}
```

- 일반적으로 필자는 switch문을 한 번만 참아준다. 다형적 객체를 생성하는 코드 안에서다
- 이렇게 상속 관계로 숨긴 후에는 절대로 다른 코드에 노출하지 않는다
- 물론 불가피한 상황도 생긴다. 저자 자신도 이 규칙을 위반한 경험이 여러 번 있다

### 서술적인 이름을 사용하라!

- 목록 3-7에서 저자는 예제 함수명 testableHtml을 setUpTearDownIncluder.render로 변경했다
- 함수가 하는 일을 좀 더 잘 표현하므로 훨씬 좋은 이름이다
- 그리고 저자는 private 함수에도 isTestable, includeSetupAndTearDownPages 등 서술적인 이름을 지었다
- 좋은 이름이 주는 가치는 아무리 강조해도 지나치지 않다. 워드가 말한 원칙을 기억하는가?

> 코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 깨끗한 코드라 불러도 되겠다

- 한 가지만 하는 작은 함수에 좋은 이름을 붙인다면 이런 원칙을 달성함에 있어 이미 절반은 성공했다
- 함수가 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다
- 이름이 길어도 괜찮다. 겁먹을 필요 없다. 길고 서술적인 이름이 짧고 어려운 이름보다 좋다
- 길고 서술적인 이름이 길고 서술적인 주석보다 좋다. 함수명을 정할 때는 여러 단어가 쉽게 읽히는 명명법을 사용한다
- 그 다음 여러 단어를 사용해 함수 기능을 잘 표현하는 이름을 선택한다
- 이름을 정하느라 시간을 들여도 괜찮다. 이런저런 이름을 넣어 코드를 읽어보면 더 좋다. 최신 IDE에서 이름 바꾸기는 식은 죽 먹기다
- IDE를 써서 이런저런 이름을 시도한 후 최대한 서술적인 이름을 골라도 좋겠다
- 서술적인 이름을 쓰면 개발자 머릿속에서도 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다
- 좋은 이름을 고른 후 코드를 더 좋게 재구성하는 사례도 없지 않다
- 이름을 붙일 때는 일관성이 있어야 한다. 모듈 내에서 함수명은 같은 문구, 명사, 동사를 사용한다
- includeSetupAndTearDownPages, includeSetupPages, includeSuiteSetupPage, includeSetupPage 등이 좋은 예다
- 문제가 비슷하면 이야기를 순차적으로 풀어가기도 쉬워진다. 방금 열거한 함수를 봐라. 당장 이런 질문이 떠오른다
- "includeTeardownPages, includeSuiteTeardownPage, includeTearDownPage도 있나요?" 당연하다. 짐작하는 대로다

### 함수 인수

- 함수에서 이상적인 인수 개수는 0개(무항)다. 다음은 1개(단항), 그 다음은 2개(이항)다. 3개(삼항)는 가능한 피하는 게 좋다
- 4개 이상(다항)은 특별한 이유가 필요하다. 특별한 이유가 있어도 사용하면 안 된다
- 인수는 어렵다. 인수는 개념을 이해하기 어렵게 만든다. 이것이 저자가 책의 예제에서 인수를 거의 없앤 이유다
- 예를 들어 목록 3-7에서 StringBuffer를 봐라. 인스턴스 변수로 선언하는 대신 함수 인수로 넘기는 방법도 있었다
- 하지만 그랬다면 코드를 읽는 사람이 StringBuffer를 발견할 때마다 의미를 해석해야 한다
- 코드를 읽는 사람에게는 includeSetupPageInto(new PageContent)보다 includeSetupPage()가 이해하기 더 쉽다
- includeSetupPageInto(new PageContent)는 함수명, 인수 사이에 추상화 수준이 다르다. 게다가 코드를 읽는 사람이
현 시점에서 별로 중요하지 않은 세부사항(StringBuffer)를 알아야 한다
- 테스트 관점에서 보면 인수는 더 어렵다. 갖가지 인수 조합으로 함수를 검증하는 테스트 케이스를 작성한다고 생각해보라
- 인수가 없다면 간단하다. 하나라도 괜찮다. 2개면 조금 복잡해진다. 3개를 넘어가면 인수마다 유효한 값으로 모든 조합을 구성해 테스트하기가
상당히 부담스러워진다
- 출력 인수는 입력 인수보다 이해하기 어렵다. 흔히 우린 함수에다 인수로 입력을 넘기고 반환값으로 출력을 받는다는 개념에 익숙하다
- 대개 함수에서 인수로 결과를 받으리라 기대하지 않는다. 그래서 출력 인수는 독자가 허둥지둥 코드를 재차 확인하게 만든다
- 최선은 입력 인수가 없는 경우이며, 차선은 입력 인수가 1개뿐인 경우다. setUpTearDownIncluder.render(pageData)는 이해하기 아주 쉽다.
pageData 객체 내용을 렌더링하겠단 뜻이다

#### 많이 쓰는 단항 형식

- 함수에 인수 1개를 넘기는 이유로 가장 흔한 경우는 2가지다.