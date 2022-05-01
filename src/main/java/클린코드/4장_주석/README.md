> 나쁜 코드에 주석을 달지 마라. 새로 짜라

- 잘 달린 주석은 그 어떤 정보보다 유용하다. 경솔하고 근거 없는 주석은 코드를 이해하기 어렵게 만든다
- 오래되고 조잡한 주석은 거짓과 잘못된 정보를 퍼뜨려 해악을 미친다  
- 주석은 쉰들러 리스트가 아니다. 주석은 순수하게 선하지 못하다. 사실상 주석은 기껏해야 필요악이다. 프로그래밍 언어 자체가 표현력이 풍부하다면, 내게 프로그래밍 언어를 
치밀하게 써서 의도를 표현할 능력이 있다면 주석은 거의 필요하지 않을 것이다
- 우리는 코드를 코드로 표현하지 못해서, 실패를 만회하기 위해서 주석을 쓴다. 주석은 언제나 실패를 의미한다. 때때로 주석 없이는 자신을 표현할 방법을 찾지 못해 할 수 없이
주석을 사용한다
- 주석이 필요한 상황에 처하면 생각해라. 상황을 역전해서 코드로 의도를 표현할 방법은 없나? 주석을 달 때마다 자신에게 표현력이 없단 사실을 푸념해야 마땅하다
- 주석을 무시하는 이유 : 거짓말을 하기 때문이다. 주석은 오래될수록 코드에서 멀어진다. 완전히 그릇될 가능성도 커진다. 프로그래머들이 주석을 유지보수하는 건 현실적으로
불가능하다
- 코드는 변하고 진화한다. 일부가 옮겨지기도 한다. 주석이 언제나 코드를 따라가지는 못한다. 아래에서 주석과 원래 주석을 달았던 행이 어떻게 됐는지 보라

```java
Mockrequest request;
private final String HTTP_DATE_REGEXP = "[SMTWF][a-z]{2}\\,\\s[0-9]{2}\\s[JFMASOND][a-z]{2}\\s" + "[0-9]{4}\\s[0-9]{2}\\:[0-9]{2}\\:[0-9]{2}\\sGMT";
private Response response;
private FitNesseContext context;
private FileResponder responder;
private Locale saveLocale;
// Example : "Tue, 02 Apr 2003 22:18:49 GMT"
```

- HTTP_DATE_REGEXP 상수와 주석 사이에 다른 인스턴스 변수를 추가했을 가능성이 높다
- 프로그래머들이 주석을 엄격 관리해야 한다고, 그래서 복구성과 관련성, 정확성이 언제나 높아야 한다고 주장할지도 모른다. 그러나 저자라면 코드를 깔끔하게 정리하고
표현력을 강화하는 방향으로, 그래서 애초에 주석이 필요없는 방향으로 에너지를 쏟겠다
- 부정확한 주석은 아예 없는 주석보다 훨씬 더 나쁘다. 부정확한 주석은 독자를 현혹하고 오도한다. 부정확한 주석은 이뤄지지 않을 기대를 심어준다. 더 이상 지킬 필요가 없는
규칙이나 지켜선 안 되는 규칙을 명시한다
- 진실은 코드 한 곳에만 존재한다. 코드만이 자기가 하는 일을 진실되게 말한다. 코드만이 정확한 정보를 제공하는 유일한 출처다. 그래서 우린 주석을 가능한 줄이도록 노력해야 한다

### 주석은 나쁜 코드를 보완하지 못한다

- 코드에 주석을 추가하는 일반적인 이유는 코드 품질이 나쁘기 때문이다
- 표현력이 풍부하고 깔끔하며 주석이 거의 없는 코드가 복잡하고 어수선하며 주석이 많이 달린 코드보다 훨씬 좋다. 자신이 저지른 난장판을 주석으로 설명하려 애쓰는 대신 그
난장판을 깨끗이 치우는 데 시간을 보내라

### 코드로 의도를 표현하라

- 확실히 코드만으로 의도를 설명하기 어려운 경우가 존재한다. 불행하게도 많은 개발자가 이를 코드는 훌륭한 수단이 아니란 의미로 해석한다. 분명 잘못된 생각이다
- 아래 코드 예제 중 뭐가 더 나은가?
````java
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다
if ((employee.flags & HOURLY_FLAG) && employee.age > 65)
````
````java
if (employee.isEligibleForFullBenefits())
````

- 몇 초만 더 생각하면 코드로 대다수 의도를 표현할 수 있다. 많은 경우 주석으로 달려는 설명을 함수로 만들어 표현해도 충분하다

### 좋은 주석

- 어떤 주석은 필요하거나 유익하다. 지금부터 글자 값을 한다고 생각하는 주석 몇 개를 소개한다. 하지만 정말 좋은 주석은 주석을 달지 않을 방법을 찾아낸 주석이라는 걸 명심하라


#### 법적인 주석

- 때로는 회사가 정립한 구현 표준에 맞춰 법적인 이유로 특정 주석을 너흥라고 명시한다. 예를 들어 각 소스파일 첫머리에 주석으로 들어가는 저작권, 소유권 정보는 필요하고 타당하다
- 아래는 FitNess에서 모든 소스 파일 첫머리에 추가한 표준 주석 헤더다. 요즘 IDE는 주석 헤더를 자동 축소해 코드만 깔끔하게 표시한다
````java
// Copyright (C) 2003, 2004, 2005 by Object Mentor, Inc. All rights reserved
// GNU General Public License 버전 2 이상을 따르는 조건으로 배포한다
````

- 소스파일 첫머리에 들어가는 주석이 반드시 계약 조건, 법적 정보일 필요는 없다. 모든 조항, 조건을 열거하는 대신 가능하다면 표준 라이선스나 외부 문서를 참조해도 되겠다

#### 정보를 제공하는 주석

- 때로는 기본 정보를 주석으로 제공하면 편하다. 아래 주석은 추상 메서드가 리턴할 값을 설명한다

```java
// 테스트 중인 Responder 인스턴스를 리턴한다
protected abstract Responder responderInstance();
```
- 때로 위와 같은 주석이 유용하더라도 가능하다면 함수명에 정보를 담는 편이 더 좋다. 예를 들어 위 코드는 함수명을 responderBeingTested로 바꾸면 주석이 필요없어진다

````java
// kk:mm:ss EEE, MMM dd, yyyy 형식이다
Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d* \\w*, \\w* \\d* \\d*");
````

- 위에 제시한 주석은 코드에서 쓴 정규식이 시각, 날짜를 뜻한다고 설명한다
- 구체적으론 주어진 형식 문자열을 써서 SimpleDateFormat.format()이 리턴하는 시각, 날짜를 뜻한다. 이왕이면 시각, 날짜를 리턴하는 클래스를 만들어 코드로 옮기면
더 깔끔하고 주석이 필요없어진다

#### 의도를 설명하는 주석

- 때로 주석은 구현을 이해하게 도와주는 선을 넘어 결정에 깔린 의도까지 설명한다. 아래는 주석으로 흥미로운 결정을 기록한 예제다
- 두 객체를 비교할 때 저자는 다른 어떤 객체보다 자기 객체에 높은 순위를 주기로 결정했다

```java
public int compareTo(Object o) {
    if (o instnaceof WikiPagePath) {
        WikiPagePath p = (WikiPagePath) o;
        String compressedName = StringUtil.join(names, "");
        String compressedArgumentName = StringUtil.join(p.names, "");
        return compressedName.compareTo(compressedArgumentName);
    }
    return 1; // 오른쪽 유형이므로 정렬 순위가 더 높다
}
```

- 아래는 더 나은 예제다. 저자가 문제를 해결한 방식에 동의하지 않을지도 모르지만 어쨌든 저자 의도는 분명히 드러난다

````java
public void testConcurrentAddWidgets() throws Exception {
    WidgetBuilder widgetBuilder = new WidgetBuilder(new Class[]{BodyWidget.class});
    String text = "'''bold text'''";
    ParentWidget parent = new BoldWidget(new MockWidgetRoot(), "'''bold text'''");
    AtomicBoolean failFlag = new AtomicBoolean();
    failFlag.set(false);
    
    // 쓰레드를 대량 생성하는 방법으로 어떻게든 경쟁 조건을 만들려 시도한다
    for (int i = 0; i < 25000; i++) {
        WidgetBuilderThread widgetBuilderThread = new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
        Thread thread = new Thread(widgetBuilderThread);
        thread.start();
    }
    assertEquals(false, failFlag.get());
}
````

#### 의미를 명료하게 밝히는 주석

- 때로 모호한 인수나 리턴값은 그 의미를 읽기 좋게 표현하면 이해하기 쉬워진다
- 일반적으론 인수나 리턴값 자체를 명화갛게 만들면 더 좋겠지만 인수나 리턴값이 표준 라이브러리나 변경하지 못하는 코드에 속한다면 의미를 명료하게 밝히는 주석이 유용하다
````java
public void testCompareTo() throws Exception {
    WikiPagePath a = PathParser.parse("PageA");
    WikiPagePath ab = PathParser.parse("PageA.PageB");
    WikiPagePath b = PathParser.parse("PageB");
    WikiPagePath aa = PathParser.parse("PageA.PageA");
    WikiPagePath bb = PathParser.parse("PageB.PageB");
    WikiPagePath ba = PathParser.parse("PageB.PageA");
    
    assertTrue(a.compareTo(a) == 0);        // a == a
    assertTrue(a.compareTo(b) != 0);        // a != b
    assertTrue(ab.compareTo(ab) == 0);      // ab == ab
    assertTrue(a.compareTo(b) == -1);       // a < b
    assertTrue(aa.compareTo(ab) == -1);     // aa < ab
    assertTrue(ba.compareTo(bb) == -1);     // ba < bb
    assertTrue(b.compareTo(a) == 1);        // b > a
    assertTrue(ab.compareTo(aa) == 1);      // ab > aa
    assertTrue(bb.compareTo(ba) == 1);      // bb > ba
}
````

- 물론 그릇된 주석을 달 위험은 높다. 주석이 올바른지 검증하는 건 쉽지 않다. 이것이 의미를 명료히 밝히는 주석이 필요한 이유인 동시에 주석이 위험한 이유기도 하다
- 위 같은 주석을 달 때는 더 나은 방법이 없는지 고민하고 정확히 달게 주의한다

#### 결과를 경고하는 주석

- 때로 다른 프로그래머에게 결과를 경고할 목적으로 주석을 쓴다
- 아래는 특정 테스트 케이스를 꺼야 하는 이유를 설명하는 주석이다

````java
// 여유 시간이 충분하지 않다면 실행하지 마라
public void _testWithReallyBigFile() {
    writeLinesToFile(10000000);
    
    response.setBody(testFile);
    response.readyToSend(this);
    String responseString = output.toString();
    assertSubString("Content-Length: 1000000000", responseString);
    assertTrue(bytesSent > 1000000000);
}
````

- 요즘엔 @Ignore 속성으로 테스트 케이스를 끈다. 구체적인 설명은 @Ignore 속성에 문자열로 넣어준다
- 예를 들어 @Ignore("실행이 너무 오래 걸린다") 라고 쓴다. JUnit4가 나오기 전엔 메서드명 앞에 _ 기호를 붙이는 게 일반적인 관례였다
- 위에 제시한 주석은 경박하지만 적절한 지적이다. 아래는 주석이 적절한 예시다

````java
public static SimpleDateFormat makeStandardHttpDateFormat() {
    // SimpleDateFormat은 쓰레드에 안전하지 못하다. 따라서 각 인스턴스를 독립적으로 생성해야 한다
    SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM  yyyy HH:mm:ss z");
    df.setTimeZone(TimeZone.getTimeZone("GMT"));
    return df;
}
````

- 더 나은 해결책이 있다고 불평할 수 있다. 하지만 여기선 주석이 합리적이다. 프로그램 효율을 높이기 위해 정적 초기화 함수를 쓰려던 프로그래머가 주석 때문에 실수를 면한다

#### TODO 주석

- 앞으로 할 일을 //TODO 주석으로 남기면 편하다. 아래는 함수를 구현하지 않은 이유와 미래 모습을 //TODO 주석으로 설명한 예제다

````java
// TODO-MdM 현재 필요하지 않다
// 체크아웃 모델을 도입하면 함수가 필요없다
protected VersionInfo makeVersion() throws Exception {
    return null;
}
````

- TODO 주석은 프로그래머가 필요하다 여기지만 당장 구현하기 어려운 업무를 기술한다
- 필요없는 기능을 삭제하란 알림, 누군가에게 문제를 봐달란 요청, 더 좋은 이름을 떠올려달란 부탁, 앞으로 발생할 이벤트에 맞춰 코드를 고치란 주의 등에 유용하다
- 하지만 어떤 용도로 쓰든 시스템에 나쁜 코드를 남겨 놓는 핑계가 되서는 안 된다
- 요즘 대다수 IDE는 TODO 주석 전부를 찾아 보여주는 기능을 제공하므로 주석을 잊어버릴 염려는 없다. 그래도 TODO로 떡칠한 코드는 바람직하지 않다. 그래서 주기적으로
TODO 주석을 점검해서 없어도 괜찮은 주석은 없애라고 권한다

#### 중요성을 강조하는 주석

- 대수롭지 않다고 여겨질 뭔가의 중요성을 강조하기 위해서도 주석을 쓴다

````java
String listItemContent = match.group(3).trim();
// 여기서 trim은 정말 중요하다. trim()은 문자열에서 시작 공백을 제거한다
// 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다
new ListItemWidget(this, listItemContent, this.level + 1);
return buildList(text.subString(match.end()));
````

#### 공개 API에서 Javadocs

- 설명이 잘 된 공개 API는 유용하고 만족스럽다. 표준 자바 라이브러리에서 사용한 javadocs가 좋은 예다. 이게 없다면 자바 프로그램 짜기가 매우 어려울 것이다
- 공개 API를 구현한다면 훌륭한 Javadocs를 작성한다. 하지만 이 장에서 제시하는 나머지 충고도 명심하라. 여느 주석과 마찬가지로 Javadocs 역시 독자를 오도하거나, 잘못
위치하거나, 그릇된 정보를 전달할 가능성이 존재한다

### 나쁜 주석

- 대다수 주석이 이 범주에 속한다. 일반적으로 대다수 주석은 허술한 코드를 지탱하거나, 엉성한 코드를 변명하거나, 미숙한 결정을 합리화하는 등 프로그래머가 주절거리는
독백에서 크게 벗어나지 못한다

#### 주절거리는 주석

- 특별한 이유 없이 의무감으로 혹은 프로세스에서 하라고 하니까 마지못해 주석을 단다면 전적으로 시간낭비다. 주석을 달기로 결정했다면 충분한 시간을 들여 최고의 주석을 달게 노력한다
- 아래는 FitNess에서 발견한 코드로 주석을 제대로 달았으면 상당히 유용했을 코드다. 하지만 저자가 서둘렀거나 부주의했다. 그냥 주절거려 놓아서 판독이 불가능하다

````java
public void loadProperties() {
    try {
        String propertiesPath = propertiesLocation + "/" + PROPERTIES_FILE;
        FileInputStream propertiesStream = new FileInputStream(propertiesPath);
        loadedProperties.load(propertiesStream);
    }   catch(IOException e) {
        // 속성 파일이 없다면 기본값을 모두 메모리로 읽어들였다는 의미다
    }
}
````

- catch 블록에 있는 주석은 무슨 뜻인가? 저자에겐 의미가 있겠지만 그 의미가 다른 사람들에겐 전해지지 않는다
- IOException이 발생하면 속성 파일이 없다는 뜻이란다. 그럼 모든 기본값을 메모리로 읽어들인 상태라고 한다. 하지만 누가 모든 기본값을 읽어들이는가?
- loadProperties.load()를 호출하기 전에 읽어들이는지, 아니면 loadProperties.load()가 예외를 잡아 기본값을 읽어들인 후 예외를 던지는가? 아니면 loadProperties.load()가
파일을 읽어들이기 전에 모든 기본값부터 읽어 들이는가? 저자가 catch 블록을 비워놓기 뭐해 몇 마디 붙인 건가? 아니면 나중에 돌아와서 기본값을 읽어들이는 코드를 구현하려 했는가?
- 답을 알아내려면 다른 코드를 뒤져보는 수밖에 없다. 이해가 안 되서 다른 모듈까지 뒤져야 하는 주석은 독자와 제대로 소통 못 하는 주석이다. 그런 주석은 바이트만 낭비할 뿐이다

### 같은 이야기를 중복하는 주석

- 아래(4-1)는 간단한 함수로 헤더에 달린 주석이 같은 코드 내용을 그대로 중복한다. 자칫 코드보다 주석 보는 시간이 더 오래 걸린다

````java
// this.closed가 true일 때 반환되는 유틸리티 메서드다. 타임아웃에 도달되면 예외를 던진다
// 4-1
public synchronized void waitForClose(final long timeoutMillis) throws Exception {
    if (!closed) {
        wait(timeoutMillis);
        if (!closed)
            throw new Exception("MockResponseSender could not be closed");
    }
}
````

- 위 같은 주석을 달아놓는 목적이 무엇인가? 주석이 코드보다 많은 정보를 제공하지 못한다. 코드를 정당화하는 주석도 아니고 의도나 근거를 설명하는 주석도 아니다
- 코드보다 읽기 쉽지도 않다. 실제로 코드보다 부정확해서 독자가 함수를 대충 이해하고 넘어가게 만든다. 엔진 후드를 열어볼 필요가 없다며 고객에 아양떠는 중고차 판매원과 비슷하다
- 아래는 톰캣에서 가져온 코드다. 쓸모없고 중복된 Javadocs가 매우 많다. 아래 주석은 코드만 지저분하고 정신없게 만든다. 기록이란 목적엔 전혀 기여하지 못한다

````java
public abstract class ContainerBase implements Container, LifeCycle, PipeLine, MBeanRegistration, Serializable {
    /**
     * 이 컴포넌트의 프로세서 지연값
     */
    protected int backgroundProcessorDelay = -1;

    /**
     * 이 컴포넌트를 지원하기 위한 생명주기 이벤트
     */
    protected LifeCycleSupport lifecycle = new LifeCycleSupport(this);

    /**
     * 이 컴포넌트를 위한 컨테이너 이벤트 Listener
     */
    protected ArrayList listeners = new ArrayList();

    /**
     * 컨테이너와 관련된 Loader 구현
     */
    protected Loader loader = null;

    /**
     * 컨테이너와 관련된 Logger 구현
     */
    protected Log logger = null;

    /**
     * 관련된 logger 이름
     */
    protected String logName = null;

    /**
     * 컨테이너와 관련된 Manager 구현
     */
    protected Manager manager = null;

    /**
     * 컨테이너와 관련된 Cluster
     */
    protected Cluster cluster = null;

    /**
     * 사람이 읽을 수 있는 컨테이너 이름
     */
    protected String name = null;

    /**
     * 컨테이너의 부모 컨테이너
     */
    protected Container parent = null;
}
````

#### 오해할 여지가 있는 주석

- 의도는 좋았으나 프로그래머가 딱 맞을 정도로 엄밀하겐 주석을 달지 못하기도 한다. 4-1에서 본 주석을 떠올려보라. 4-1 주석은 중복이 많으면서도 오해할 여지가 살짝 있다
- 4-1의 주석이 어째서 오해의 여지가 있는지 알겠는가? this.closed가 true로 변하는 순간에 메서드는 리턴되지 않는다. this.closed가 true여야 메서드가 리턴된다
- 아니면 무조건 타임아웃을 기다렸다가 this.closed가 그래도 true가 아니면 예외를 던진다
- (코드보다 읽기도 어려운) 주석에 담긴 '살짝 잘못된 정보'로 인해 this.closed가 true로 변하는 순간에 함수가 리턴되리란 생각으로 어느 프로그래머가 경솔하게 함수를 호출
할지도 모른다. 그래놓고 그 프로그래머는 자기 코드가 굼벵이 기어가듯 돌아가는 이유를 찾느라 골머리를 앓는다

#### 의무적으로 다는 주석

- 모든 함수에 Javadocs를 달거나 모든 변수에 주석을 달아야 한다는 규칙은 어리석다. 이런 주석은 코드를 복잡하게 만들며 거짓말을 퍼뜨리고 혼동, 무질서를 초래한다
- 아래(4-3)은 모든 함수에 Javadocs를 넣으라는 규칙이 낳은 괴물이다. 아래 주석은 아무 가치도 없다. 코드만 헷갈리게 만들고 거짓말할 가능성을 높이며 잘못된 정보를 제공할
여지만 만든다

````java
/**
 * @param title CD 제목
 * @param author CD 저자
 * @param tracks CD 트랙 숫자
 * @param durationInMinutes CD 길이(단위 : 분)
 */
public void addCd(String title, String author, int tracks, int durationInMinutes) {
    CD cd = new CD();
    cd.title = title;
    cd.author = author;
    cd.tracks = tracks;
    cd.duration = durationInMinutes;
    cdList.add(cd);
}
````

#### 이력을 기록하는 주석

- 때로 사람들은 모듈을 편집할 때마다 모듈 첫머리에 주석을 추가한다. 그래서 모듈 첫머리 주석은 지금까지 모듈에 가한 변경을 모두 기록하는 일종의 일지 혹은 로그가 된다
- 첫머리 주석만 10여 쪽을 넘는 모듈도 봤다

````java
/**
 * 변경 이력(11-Oct-2001부터)
 * ----------------------------------------------------------------------
 * 11-Oct-2001 : 클래스를 다시 정리하고 새 패키지인 com.jrefinery.date로 옮겼다 (DG);
 * 05-Dov-2001 : getDescription()을 추가하고 NotableDate class를 제거했다 (DG);
 * .
 * .
 * .
 */
````

- 예전엔 모든 모듈 첫머리에 변경 이력을 기록하고 관리하는 관례가 바람직했다. 소스코드 관리 시스템이 있는 이제는 혼란만 가중할 뿐이다. 완전히 제거하는 게 낫다

#### 있으나 마나 한 주석

- 너무 당연한 사실을 언급하며 새 정보를 제공 못하는 주석을 말한다

````java
/**
 * 기본 생성자
 */
protected AnnualDateRule() {}
````

- 그렇단다. 다음은 어떤가?

````java
/**
 * 월 중 일자
 */
private int dayOfMonth;
````

- 이번엔 전형적인 중복을 보여준다

````java
/**
 * 월 중 일자
 * 
 * @return 월 중 일자
 */
public int getDayOfMonth() { return dayOfMonth; }
````

- 이런 주석은 지나친 참견이라 개발자가 주석을 무시하는 습관에 빠진다. 코드를 보면서 자동으로 주석을 건너뛴다. 결국 코드가 바뀌면서 거짓말로 변한다
- 아래의 1번째 주석은 적절해 보인다. catch 블록을 무시해도 괜찮은 이유를 설명하는 주석이지만 2번 주석은 쓸모가 없다

````java
private void startSending() {
    try {
        doSending();
    }   catch(SocketException e) {
        // 정상. 누군가 요청을 멈췄다
    }   catch(Exception e) {
        try {
            response.add(ErrorResponder.makeExceptionString(e));
            response.closeAll();
        }   catch(Exception e1) {
            // 이게 뭐임?
        }
    }
}
````

- 프로그래머가 코드 구조를 개선했다면 짜증낼 필요가 없었을 것이다. 아래 코드에서 보듯이 try-catch 블록을 독자적인 함수로 만드는 노력을 했어야 한다

````java
private void startSending() {
    try {
        doSending();
    }   catch(SocketException e) {
        // 정상. 누군가 요청을 멈췄다
    }   catch(Exception e) {
        addExceptionAndCloseResponse(e);
    }
}

private void addExceptionAndCloseResponse(Exception e) {
    try {
        response.add(ErrorResponder.makeExceptionString(e));
        response.closeAll();
    }   catch(Exception e1) {
        //
    }
}
````

- 있으나 마나 한 주석을 달려는 유혹에서 벗어나 코드를 정리하라. 더 낫고 행복한 프로그래머가 되는 지름길이다

#### 무서운 잡음

- 때로는 Javadocs도 잡음이다. 아래는 잘 알려진 오픈소스 라이브러리에서 가져온 코드다. 아래 나오는 Javadocs는 어떤 목적을 수행하는가? 답은 없다
- 단지 문서를 제공해야 한다는 욕심으로 탄생한 잡음일 뿐이다

````java
/** The name */
private String name;

/** The version */
private String version;

/** The licenceName */
private String licenceName;

/** The version */
private String info;
````

- 복붙 오류가 보인다. 주석을 작성한(혹은 붙여넣은) 저자가 주의를 기울이지 않았다면 독자가 여기서 무슨 이익을 얻겠는가?

#### 함수나 변수로 표현할 수 있다면 주석을 달지 마라

- 아래 코드를 보자

````java
// 전역 목록 <smodule>에 속하는 모듈이 내가 속한 하위 시스템에 의존하는가?
if (smodule.getDependSubSystems().contains(subSysMod.getSubSystem()))
````

- 여기서 주석을 없애고 다시 표현하면 아래와 같다

````java
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
````

- 코드를 쓴 저자는 주석을 먼저 달고 주석에 맞춰 코드를 썼을지도 모른다. 하지만 위처럼 주석이 필요없도록 코드를 개선하는 편이 더 좋았다

#### 위치를 표시하는 주석

- 때로 프로그래머는 소스파일에서 특정 위치를 표시하려고 주석을 쓴다. 하지만 일반적으로 위와 같은 주석은 가독성만 낮추므로 제거해야 마땅하다
- 너무 자주 쓰지 않는다면 배너는 눈에 띄며 주의를 환기한다. 그래서 반드시 필요할 때만 드물에 쓰는 편이 좋다. 배너를 남용하면 독자가 흔한 잡음으로 여겨 무시한다

#### 닫는 괄호에 다는 주석

- 때로 프로그래머들이 닫는 괄호에 특수 주석을 달아놓는다. 아래가 좋은 예다. 중첩이 심하고 장황한 함수라면 의미 있을지도 모르지만 작고 캡슐화된 함수에는 잡음일 뿐이다
- 닫는 괄호에 주석을 달아야겠단 생각이 들면 함수를 줄이려고 시도하라

````java
public class wc {
    public static void main(String[] args) {
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        String line;
        int lineCount = 0;
        int charCount = 0;
        int wordCount = 0;
        try {
            while ((line = in.readLine()) != null) {
                lineCount++;
                charCount += line.length();
                String[] words = line.split("\\W");
                wordCount += words.length;
            } // while
            System.out.println("wordCount = " + wordCount);
            System.out.println("lineCount = " + lineCount);
            System.out.println("charCount = " + charCount);
        } // try
        catch (IOException e) {
            System.out.println("Error : " + e.getMessage());
        }
    }
}
````

#### 공로를 돌리거나 저자를 표시하는 주석

- 소스코드 관리 시스템은 언제 누가 뭘 추가했는지 기억한다. 저자명으로 코드를 오염시킬 필요가 없다. 이런 주석은 오랫동안 코드에 방치되서 부정확하고 쓸모없는 정보로 변하기 쉽다

#### 주석 처리한 코드

- 주석으로 처리한 코드만큼 미운 관행도 드물다. 주석 처리된 코드는 다른 사람들이 지우기 주저한다. 이유가 있어 남겨놓았을 거라고, 중요하니까 지우면 안 된다고 생각한다
- 그래서 쓸모없는 코드가 점차 쌓여간다. 1960년대 쯤에는 주석 처리한 코드가 유용했다. 하지만 소스코드 관리 시스템이 날 대신해서 코드를 기억해준다. 그냥 삭제하라

#### HTML 주석

- 소스코드에서 HTML 주석은 혐오 그 자체다. HTML 주석은 편집기, IDE에서도 읽기 힘들다

#### 전역 정보

- 주석을 달아야 한다면 근처에 있는 코드만 기술하라. 코드 일부에 주석을 달면서 시스템의 전반적인 정보를 기술하지 마라
- 아래 Javadocs 주석은 심하게 중복됐다는 사실 외에도 주석은 기본 포트 정보를 기술한다. 하지만 함수 자체는 포트 기본값을 전혀 통제하지 못한다
- 그래서 아래 주석은 바로 아래 함수가 아닌 시스템 어딘가에 있는 다른 함수를 설명한다는 말이다. 즉 포트 기본값을 설정하는 코드가 변해도 아래 주석이 변하리란 보장은 전혀 없다

````java
/**
 * 적합성 테스트가 동작하는 포트. 기본값은 <b>8082</b>
 * 
 * @param fitnessPort
 */
public void setFitnessPort(int fitnessPort) {
    this.fitnessPort = fitnessPort
}
````

#### 너무 많은 정보

- 주석에 역사, 관련없는 정보를 장황하게 늘어놓지 마라

#### 모호한 관계

- 주석과 주석이 설명하는 코드는 둘 사이 관계가 명백해야 한다. 공들여 주석을 달았다면 적어도 독자가 주석, 코드를 보고 뭔 소린지 알아야 한다

#### 함수 헤더

- 짧은 함수는 긴 설명이 필요 없다. 짧고 한 가지만 수행하며 이름을 잘 붙인 함수가 주석으로 헤더를 추가한 함수보다 좋다

#### 비공개 코드에서 Javadocs

- 공개 API는 Javadocs가 유용하지만 비공개 코드면 Javadocs는 쓸모가 없다. 시스템 내부에 속한 클래스, 함수에 Javadocs를 생성할 필요는 없다
- 유용하지 않을 뿐 아니라 Javadocs 주석이 요구하는 형식으로 인해 코드만 보기 싫고 산만해질 뿐이다