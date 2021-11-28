- 어떤 프로그램이든 가장 기본적인 단위가 함수다. 이 장은 함수를 잘 만드는
법을 소개한다
- 목록 3-1의 코드를 보라. FitNesse(오픈 소스 테스트 도구)에서 긴 함수를
찾기는 어렵다. 하지만 한동안 조사한 끝에 다음 함수를 발견했다
- 길이가 길 뿐 아니라 중복된 코드, 괴상한 문자열, 낯설고 모호한 자료유형과
API가 많다

```java
// 목록 3-1
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
// 목록 3-2 (리팩토링한 버전)
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
// 목록 3-3 다시 리팩토링한 버전
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
// 목록 3-4
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

- 함수에 인수 1개를 넘기는 이유로 가장 흔한 경우는 2가지다. 하나는 인수에 질문을 던지는 경우다
- boolean fileExists("MyFile")이 좋은 예다. 다른 하나는 인수를 뭔가로 변환해 결과를 반환하는 경우다
- InputStream fileOpen("MyFile)은 String 형의 파일명을 InputStream으로 변환한다. 이들 두 경우는 독자가 당연하게 받아들인다
- 함수명을 지을 때는 두 경우를 분명히 구분한다. 또한 언제나 일관적인 방식으로 두 형식을 사용한다(56p 명령과 조회를 분리하라! 참조)
- 드물게 사용하지만 그래도 아주 유용한 단항 함수 형식이 이벤트다. 이벤트 함수는 입력 인수만 있고 출력 인수는 없다.
프로그램은 함수 호출을 이벤트로 해석해 입력 인수로 시스템 상태를 바꾼다. passwordAttemptFailedNtimes(int attempts)가 좋은 예다
- 이벤트 함수는 조심해서 사용한다. 이벤트란 사실이 코드에 명확히 나타나야 한다. 그러므로 이름, 문맥을 주의해서 선택한다
- 지금까지 설명한 경우가 아니면 단항 함수는 가급적 피한다. 예를 들어 void includeSetupPageInto(StringBugger pageText)는 피한다
- 변홤 함수에서 출력 인수를 사용하면 혼란을 일으킨다. 입력 인수를 변환하는 함수라면 변환 결과는 반환값으로 돌려준다. StringBuffer transform(StringBuffer in)이
void transform(StringBuffer out)보다 좋다
- StringBuffer transform(StringBuffer in)이 입력 인수를 그대로 돌려주는 함수라 할지라도 변환 함수 형식을 따르는 편이 좋다. 적어도 변환 형태는
유지하기 때문이다

#### 플래그 인수

- 플래그 인수는 추하다. 함수로 bool 값을 넘기는 관례는 끔찍하다. 왜냐면 함수가 한꺼번에 여러 가지를 처리한다고 대놓고 공표하는 것이기 때문이다
- 플래그가 참이면 이걸 하고 거짓이면 저걸 한다는 뜻이기 때문이다

#### 2항 함수

- 인수가 2개인 함수는 1개인 함수보다 이해하기 어렵다. 예를 들어 writeField(name)는 writeField(outputStream, name)보다 이해하기 쉽다
- 둘 다 의미는 명백하지만 전자가 더 쉽게 읽히고 더 빨리 이해된다. 후자는 잠시 주춤하며 첫 인수를 무시해야 한다는 사실을 깨닫는 시간이 필요하다
- 그리고 그 사실이 결국 문제를 일으킨다. 어떤 코드든 절대로 무시하면 안되고, 무시한 코드에 오류가 숨어들기 때문이다
- 이항 함수가 적절한 경우도 있다. Point p = new Point(0, 0)가 좋은 예다. 직교 좌표계 점은 일반적으로 인수 2개를 취한다. 하지만 여기서 인수 2개는
한 값을 표현하는 두 요소다. 두 요소에는 자연적인 순서도 있다. 반면 outputStream, name은 한 값을 표현하지도 자연적인 순서가 있지도 않다
- 아주 당연하게 여겨지는 이항 함수 assertEquals(expected, actual)에도 문제가 있다. expected 인수에 actual 값을 넣는 실수를 하듯 두 인수는 자연적인
순서가 없다. expected 다음에 actual이 온다는 순서를 인위적으로 기억해야 한다
- 이항 함수가 무조건 나쁘단 소리는 아니다. 프로그램을 짜다보면 불가피한 경우도 생긴다. 하지만 그만큼 위험이 따른단 사실을 이해하고 가능하면 단항 함수로 바꾸도록
애써야 한다

#### 3항 함수

- 인수가 3개인 함수는 2개인 함수보다 훨씬 이해하기 어렵다. 순서, 주춤, 무시로 야기되는 문제가 2배 이상 늘어난다. 그래서 3항 함수를 만들 땐 신중히 고려해야 한다
- assertEquals(message, expected, actual) 함수를 보라. 첫 인수가 expected라고 예상하지 않았나? 저자는 매번 함수를 볼 때마다 주춤했다가 message를 무시해야
한다는 사실을 상기했다
- 반면 assertEquals(1.0, amount, .001)은 그리 음험하지 않은 3항 함수다. 여전히 주춤하지만 그만한 가치가 충분하다. 부동소수점 비교가 상대적이란 사실은
언제든 주지할 중요한 사항이다

#### 인수 객체

- 인수가 2~3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어본다. 예를 들어 아래 두 함수를 보라

```java
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```

- 객체를 생성해 인수를 줄이는 방법이 눈속임이라 여겨질지 모르지만 그렇지 않다. 위 예제에서 x, y를 묶었듯이 변수를 묶어 넘기려면
이름을 붙여야 하므로 결국은 개념을 표현하게 된다

#### 인수 목록

- 때로는 인수 개수가 가변적인 함수도 필요하다. String.format()이 좋은 예다

```java
String.format("%s worked %.2f hours", name, hours);
```

- 위 예제처럼 가변 인수 전부를 동등하게 취급하면 List형 인수 하나로 취급할 수 있다
- 이런 논리로 따져보면 String.format()은 사실상 이항 함수다. 실제로 String.format() 선언부를 보면 이항 함수란 사실이 분명히 드러난다

```java
public String format(String format, Object ... args)
```

- 가변 인수를 취하는 모든 함수에 같은 원리가 적용된다. 가변 인수를 취하는 함수는 단항, 2항, 3항 함수로 취급할 수 있다
- 하지만 이를 넘어선 인수를 사용할 경우 문제가 있다

```java
void monad(Integer ... args);
void dyad(String name, Integer ... args)
void triad(String name, int count, Integer ... args);
```

#### 동사와 키워드

- 함수의 의도나 인수의 순서, 의도를 제대로 표현하려면 좋은 함수명이 필수다
- 단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다. 예를 들어 write(name)은 누구나 곧바로 이해한다. '이름이 뭐든 쓴다'는 뜻이다
- 좀 더 나은 이름은 writeField(name)이다. 그럼 이름이 필드라는 사실이 분명히 드러난다
- 마지막 예제는 함수명에 키워드를 추가하는 형식이다. 즉, 함수명에 인수명을 넣는다. 예를 들어 assertEquals()보다 assertExpectedEqualsActual(expected, actual)이 더 좋다.
그러면 인수 순서를 기억할 필요가 없어진다

### 부수 효과를 일으키지 마라!

- 부수 효과는 거짓말이다. 함수에서 1가지를 하겠다고 약속하고 남몰래 다른 것도 하니까. 때로는 예상치 못하게 클래스 변수를 수정한다. 때로는 함수로 넘어온 인수나
시스템 전역 변수를 수정한다. 어느 쪽이든 교활하고 해로운 거짓말이다
- 많은 경우 시간적인 결합(temporal coupling)이나 순서 종속성(order dependency)을 초래한다
- 목록 3-6을 보라. 아주 무해하게 보이는 함수다. 함수는 표준 알고리즘을 써서 userName과 password를 확인한다. 두 인수가 올바르면 true, 아니면 false를 리턴한다
- 하지만 함수는 부수 효과를 일으킨다

```java
// 목록 3-6 UserValidator.java
public class UserValidator {
    private Cryptographer cryptographer;
    
    public boolean checkPassword(String userName, String password) {
        User user = UserGateway.findByName(userName);
        if (user != User.NULL) {
            String codedPhrase = user.getPhraseEncodeByPassword();
            String phrase = cryptographer.decrypt(codedPhrase, password);
            if ("Valid Password".equals(phrase)) {
                Session.initialize();
                return true;
            }
        }
        return false;
    }
}
```

- 여기서 함수가 일으키는 부수 효과는 Session.initialize() 호출이다. checkPassword()는 이름 그대로 암호를 확인한다
- 이름만 봐선 세션을 초기화한다는 사실이 드러나지 않는다. 그래서 함수명만 보고 함수를 호출하는 사용자는 사용자를 인증하면서 기존 세션 정보를 지워버릴 수 있다
- 이런 부수 효과가 시간적인 결합을 초래한다. 즉, checkPassword()는 특정 상황에서만 호출 가능하다. 다시 말해, 세션을 초기화해도 괜찮은 경우에만 호출 가능하다. 잘못
호출하면 의도치 않게 세션 정보가 날아간다
- 시간적인 결합은 혼란을 일으킨다. 특히 부수 효과로 숨겨진 경우에는 더더욱 혼란이 커진다. 만약 시간적인 결합이 필요하다면 함수명에 분명히 명시한다
- 목록 3-6은 checkPasswordAndInitializeSession이란 이름이 훨씬 좋다. 물론 함수가 '한 가지'만 한다는 규칙을 위반하긴 하지만

#### 출력 인수

- 일반적으로 우린 인수를 함수 **입력**으로 해석한다. 어느 정도 프로그래밍 경력이 쌓였다면 인수를 출력으로 사용하는 함수에 어색함을 느끼리라. 예를 들어 다음 함수를 보라

```java
appendFooter(s);
```

- 이 함수는 뭔가에 s를 바닥글로 첨부할까? 아니면 s에 바닥글을 첨부할까? 인수 s는 입력인가 출력인가? 함수 선언부를 찾아보면 분명해진다

```java
public void appendFooter(StringBuffer report)
```

- 인수 s가 출력 인수란 사실은 분명하지만, 함수 선언부를 찾아보고 나서야 알았다. 함수 선언부를 찾아보는 행위는 코드를 보다가 주춤하는 행위와 동급이다. 인지적으로 거슬린다는
뜻이므로 피해야 한다
- 객체 지향 프로그래밍이 나오기 전에는 출력 인수가 불가피한 경우도 있었다. 하지만 객체 지향 언어에서는 출력 인수를 사용할 필요가 거의 없다. 출력 인수로 사용하라고
설계한 변수가 this기 때문이다
- 다시 말해, appendFooter는 다음과 같이 호출하는 방식이 좋다

```java
report.appendFooter()
```

- `일반적으로 출력 인수는 피해야 한다. 함수에서 상태를 바꿔야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택한다`

### 명령과 조회를 분리하라

- 함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야 한다. 둘 다 하면 안 된다
- 객체 상태를 바꾸거나 객체 정보를 반환하거나 둘 중 하나다. 둘 다 하면 혼란을 초래한다
- 예를 들어 아래 함수를 보라

```java
public boolean set(String attribute, String value)
```

- 이 함수는 이름이 attribute인 속성을 찾아 값을 value로 설정한 후 성공하면 true, 실패하면 false를 리턴한다. 그래서 아래처럼 괴상한 코드가 나온다

```java
if (set("username", "unclebob")) ...
```

- 독자 입장에선 이 코드가 무슨 뜻일까? "username"이 "unclebob"으로 설정돼 있는지 확인하는 코드인가? 아니면 "username"을 "unclebob"으로 설정하는 코드인가?
- 함수를 호출하는 코드만 봐선 의미가 모호하다. "set"이란 단어가 동사인지 형용사인지 분간하기 어려운 탓이다
- 함수를 구현한 개발자는 "set"을 동사로 의도했다. 하지만 if문에 넣고 보면 형용사로 느껴진다. 그래서 if문은 "username 속성이 unclebob으로 설정되어 있다면..."으로
읽힌다. "username을 unclebob으로 설정하는데 성공하면..."으로 읽히지 않는다
- set이란 함수명을 setAndCheckIfExists라고 바꾸는 방법도 있지만 if문에 넣고 보면 여전히 어색하다
- 진짜 해결책은 명령과 조회를 분리해 혼란을 애초에 뿌리뽑는 방법이다

```java
if (attributeExists("username")) {
    setAttribute("username", "unclebob");
    ...
}
```

### 오류 코드보다 예외를 사용하라

- 명령 함수에서 오류 코드를 반환하는 방식은 명령/조회 분리 규칙을 미묘하게 위반한다. 자칫하면 if문에서 명령을 표현식으로 쓰기 쉬운 탓이다

```java
if (deletePage(page) == E_OK)
```

- 위 코드는 동사/형용사 혼란을 일으키지 않는 대신 여러 단계로 중첩되는 코드를 야기한다. 오류 코드를 반환하면 호출자는 오류 코드를 곧바로 처리해야 한다는 문제에 부딪힌다

```java
if (deletePage(page) == E_OK) {
    if (registry.deleteReference(page.name) == E_OK) {
        logger.log("page deleted");
    } else {
        logger.log("configKey not deleted");
    }
} else {
    logger.log("delete failed");
    return E_ERROR;
}
```

- 반면 오류 코드 대신 예외를 쓰면 오류 처리 코드가 원래 코드에서 분리되므로 코드가 깔끔해진다

```java
try {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
} catch (Exception e) {
    logger.log(e.getMessage());
}
```

#### try-catch 블록 뽑아내기

- try-catch 블록은 원래 추하다. 코드 구조에 혼란을 일으키며, 정상 동작과 오류 처리 동작을 뒤섞는다. 그래서 try-catch 블록을 별도 함수로 뽑아내는 게 좋다

```java
public void delete(Page page) {
    try {
        deletePageAndAllReferences(page);
    }   catch (Exception e) {
        logError(e);
    }
}

private void deletePageAndAllReferences(Page page) throws Exception {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.getKey());
}

private void logError(Exception e) {
    logger.log(e.getMessage());
}
```

- 위에서 delete()는 모든 오류를 처리한다. 그래서 코드 이해가 쉽다. 한 번 훑어보고 넘어가면 충분하다
- 실제로 페이지를 제거하는 함수는 deletePageAndAllReferences()다. deletePageAndAllReferences()는 예외를 처리하지 않는다
- 이렇게 정상 동작, 오류 동작을 분리하면 코드를 이해하고 수정하기 쉬워진다

#### 오류 처리도 한 가지 작업이다

- 함수는 '한 가지' 작업만 해야 한다. `오류 처리도 '한 가지' 작업에 속한다. 그러므로 오류를 처리하는 함수는 오류만 처리해야 마땅하다`
- `함수에 try 키워드가 있다면 함수는 try 문으로 시작해 catch/finally 문으로 끝나야 한다`

#### Error.java 의존성 자석

- 오류 코드를 반환한단 이야기는 클래스든 열거형 변수든 어디선가 오류 코드를 정의한다는 뜻이다

```java
public enum Error {
    OK,
    INVALID,
    NO_SUCH,
    LOCKED,
    OUT_OF_RESOURCES,
    WAITING_FOR_EVENT;
}
```

- 위와 같은 클래스는 의존성 자석(magnet)이다. 다른 클래스에서 Error enum을 import해 사용해야 하므로, 즉 Error enum이 변하면 이를 사용하는 클래스 전부를
다시 컴파일하고 재배치해야 한다. 그래서 Error 클래스 변경이 어려워진다. 프로그래머는 재컴파일/재배치가 번거롭기에 새 오류 코드를 정의하고 싶지 않다. 그래서
새 오류 코드를 추가하는 대신 기존 오류 코드를 재사용한다
- `오류 코드 대신 예외를 쓰면 새 예외는 Exception 클래스에서 파생된다. 따라서 재컴파일/재배치 없이도 새 예외 클래스를 추가할 수 있다`

### 반복하지 마라!

- 목록 3-1을 주의해서 읽어보면 4번이나 반복되는 알고리즘이 보인다. 알고리즘 하나가 setUp, suiteSetUp, TearDown, SuiteTearDown에서 반복된다
- 다른 코드와 섞이면서 모양새가 조금씩 달라져 중복이 금방 드러나진 않는다. 그래도 중복은 문제다. 코드 길이가 늘어날 뿐 아니라 알고리즘이 변하면 네 곳이나 손봐야 하니까. 게다가
어느 한 곳이라도 빠뜨리는 바람에 오류가 발생할 확률도 4배나 높다
- 목록 3-7은 include 방법으로 중복을 없앤다. 코드를 다시 읽어본다. 중복을 없앴더니 모듈 가독성이 크게 높아졌단 사실을 깨달으리라
- 어쩌면 중복은 모든 소프트웨어에서 악의 근원이다. 많은 원칙, 기법이 중복을 없애거나 제어할 목적으로 나왔다
- `객체 지향 프로그래밍은 코드를 부모 클래스로 몰아 중복을 없앤다.` 구조적 프로그래밍, AOP, COP 모두 어떤 면에선 중복 제거 전략이다
- 하위 루틴을 발명한 이래로 소프트웨어 개발에서 지금까지 일어난 혁신은 소스코드에서 중복을 제거하려는 지속적인 노력으로 보인다

### 구조적 프로그래밍

- 어떤 프로그래머는 에츠허르 다익스트라의 구조적 프로그래밍 원칙을 따른다. 다익스트라는 모든 함수와 함수 내 모든 블록에 입구(entry)와 출구(exit)가 하나만 있어야 한다고 했다
- 즉, 함수는 return문이 하나여야 한다는 말이다. 루프 안에서 break, continue를 써서는 안 되며 goto는 **절대로** 안 된다
- 구조적 프로그래밍의 목표, 규율은 공감하지만 함수가 작다면 위 규칙은 별 이익을 제공하지 못한다. 함수가 아주 클 때만 상당한 이익을 제공한다
- 그러므로 함수를 작게 만든다면 간혹 return, break, continue를 여러 차례 써도 괜찮다. 오히려 때론 단일 입/출구 규칙(single entry-exit rule)보다
의도를 표현하기 쉬워진다. 반면 goto 문은 큰 함수에서만 의미가 있으므로, 작은 함수에선 피해야 한다

### 함수를 어떻게 짜죠?

- 소프트웨어를 짜는 행위는 여느 글짓기와 비슷하다. 논문이나 기사를 쓸 때는 먼저 생각을 기록한 후 읽기 좋게 다듬는다. 초안은 대개 서툴고 어수선하므로 원하는 대로 읽힐 때까지
말을 다듬고 문장을 고치고 문단을 정리한다
- 저자가 함수를 짤 때도 마찬가지다. 처음엔 길고 복잡하다. 들여쓰기 단계도 많고 중복된 루프도 많다. 인수 목록도 아주 길다. 이름은 즉흥적이고 코드는 중복된다
- 하지만 `저자는 그 서투른 코드를 빠짐없이 테스트하는 단위 테스트 케이스도 만든다.` 그 다음 저자는 코드를 다듬고, 함수를 만들고, 이름을 바꾸고, 중복을 제거한다. 메서드를 줄이고
순서를 바꾼다. 때로는 전체 클래스를 쪼개기도 한다. **이 와중에도 코드는 항상 단위 테스트를 통과한다.** 최종적으론 이 장에서 설명한 규칙을 따르는 함수가 얻어진다
- 처음부터 탁 짜내지 않는다. 그게 가능한 사람은 없으리라

### 결론

- 모든 시스템은 특정 응용 분야 시스템을 기술할 목적으로 프로그래머가 설계한 도메인 특화 언어(Domain Specific Language, DSL)로 만들어진다
- 함수는 그 언어에서 동사며, 클래스는 명사다. 요구사항 문서에 나오는 명사와 동사를 클래스와 함수 후보로 고려한다는 끔찍한 옛 규칙으로 역행하자는 이야기가 아니다. 이것은
훨씬 더 오래된 진실이다. 프로그래밍의 기술은 언제나 언어 설계의 기술이다. 전에도 그랬고 지금도 마찬가지다
- `대가(master) 프로그래머는 시스템을 (구현할) 프로그램이 아니라 (풀어갈) 이야기로 여긴다.` 프로그래밍 언어라는 수단을 사용해 좀 더 풍부하고 표현력이 강한 언어를 만들어
이야기를 풀어간다. 시스템에서 발생하는 모든 동작을 설명하는 함수 계층이 바로 그 언어에 속한다
- 재귀라는 기교로 각 동작은 그 도메인에 특화된 언어를 사용해 자신만의 이야기를 풀어간다
- 이 장은 함수를 잘 만드는 기교를 소개했다. 여기서 설명한 규칙을 따른다면 길이가 짧고, 이름이 좋고, 체계가 잡힌 함수가 나오리라
- 하지만 `진짜 목표는 시스템이라는 이야기를 풀어가는 데 있단 사실을 명심하기 바란다. 여러분이 작성하는 함수가 분명하고 정확한 언어로 깔끔하게 같이 맞아떨어져야
이야기를 풀어가기가 쉬워진다는 사실을 기억하기 바란다`

```java
// 목록 3-7 SetupTeardownIncluder.java
public class SetupTeardownIncluder {
    private PageData pageData;
    private boolean isSuite;
    private WikiPage testPage;
    private StringBuffer newPageContent;
    private PageCrawler pageCrawler;
    
    public static String render(PageData pageData) throws Exception {
        return render(pageData, false);
    }
    
    public static String render(PageData pageData, boolean isSuite) throws Exception {
        return new SetupTeardownIncluder(pageData).render(isSuite);
    }
    
    private SetupTeardownIncluder(PageData pageData) {
        this.pageData = pageData;
        testPage = pageData.getWikiPage();
        pageCrawler = testPage.getPageCrawler();
        newPageContent = new StringBuffer();
    }
    
    private String render(boolean isSuite) throws Exception {
        this.isSuite = isSuite;
        if (isTestPage())
            includeSetupAndTeardownPages();
        return pageData.getHtml();
    }
    
    private boolean isTestPage() throws Exception {
        return pageData.hasAttribute("Test");
    }
    
    private void includeSetupAndTeardownPages() throws Exception {
        includeSetupPages();
        includePageContent();
        includeTeardownPages();
        updatePageContent();
    }
    
    private void includeSetupPages() throws Exception {
        if (isSuite)
            includeSuiteSetupPage();
        includeSetupPage();
    }
    
    private void includeSuiteSetupPage() throws Exception {
        include(SuiteResponder.SUITE_SETUP_NAME, "-setup");
    }
    
    private void includeSetupPage() throws Exception {
        include("SetUp", "-setup");
    }
    
    private void includePageContent() throws Exception {
        newPageContent.append(pageData.getContent());
    }
    
    private void includeTeardownPages() throws Exception {
        includeTeardownPage();
        if (isSuite)
            includeSuiteTeardownPage();
    }
    
    private void includeTeardownPage() throws Exception {
        include("TearDown", "-teardown");
    }
    
    private void includeSuiteTeardownPage() throws Exception {
        include(SuiteResponder.SUITE_TEARDOWN_NAME, "-teardown");
    }
    
    private void updatePageContent() throws Exception {
        pageData.setContent(newPageContent.toString());
    }
    
    private void include(String pageName, String arg) throws Exception {
        WikiPage inheritedPage = findInheritedPage(pageName);
        if (inheritedPage != null) {
            String pagePathName = getPathNameForPage(inheritedPage);
            buildIncludeDirective(pagePathName, arg);
        }
    }
    
    private WikiPage findInheritedPage(String pageName) throws Exception {
        return PageCrawlerImpl.getInheritedPage(pageName, testPage);
    }
    
    private String getPathNameForPage(WikiPage page) throws Exception {
        WikiPagePath pagePath = pageCrawler.getFullPath(page);
        return PathParser.render(pagePath);
    }
    
    private void buildIncludeDirective(String pagePathName, String arg) {
        newPageContent.append("\n!include")
                .append(arg)
                .append(" .")
                .append(pagePathName)
                .append("\n");
    }
}
```