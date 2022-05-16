- 프로그래머라면 형식을 깔끔하게 맞춰 코드를 짜야 한다. 코드 형식을 맞추기 귀한 규칙을 정하고 착실히 따라야 한다
- 팀으로 일한다면 팀이 합의해 규칙을 정하고 모두가 따라야 한다. 필요하다면 규칙을 자동 적용하는 도구를 활용한다

### 형식을 맞추는 목적

- 코드 형식은 `중요하다!` 너무 중요해서 무시하기 어렵다. 너무 중요하므로 융통성 없이 맹목적으로 따르면 안 된다
- 코드 형식은 의사소통의 일환이다. 의사소통은 전문 개발자의 1차적인 의무다
- 오늘 구현한 기능이 다음 버전에서 바뀔 확률은 아주 높다. 오늘 구현한 코드의 가독성은 앞으로 바뀔 코드의 품질에 지대한 영향을 미친다
- 오랜 시간이 지나 원래 코드의 흔적을 더 이상 찾아보기 어려울 정도로 코드가 바뀌어도 맨 처음 잡아놓은 구현 스타일, 가독성 수준은 유지보수 용이성과 확장성에
계속 영향을 미친다
- 원래 코드는 사라질지라도 개발자의 스타일과 규율은 사라지지 않는다
- 원활한 소통을 장려하는 코드 형식은 뭘까?

### 적절한 행 길이를 유지하라

- 세로 길이부터 보라. 소스코드는 얼마나 길어야 적당한가? 자바에서 파일 크기는 클래스 크기와 밀접하다
- 500줄을 넘지 않고 대부분 200줄 정도인 파일로도 큰 시스템을 구축할 수 있다. 반드시 지킬 엄격한 규칙은 아니지만 바람직한 규칙으로 삼으면 좋겠다
- 일반적으로 큰 파일보다 작은 파일이 이해하기 쉽다

### 신문 기사처럼 작성하라

- 아주 좋은 신문 기사를 떠올려라. 독자는 위에서 아래로 기사를 읽는다. 최상단에 기사를 몇 마디로 요약하는 표제가 나온다
- 독자는 표제를 보고 기사를 읽을지 말지 결정한다. 첫 문단은 전체 기사 내용을 요약한다. 세세한 사실은 숨기고 큰 그림을 보여준다
- 소스파일도 신문 기사와 비슷하게 쓴다. 이름은 간단하면서 설명 가능하게 짓는다. 이름만 보고도 올바른 모듈을 보고 있는지를 판단할 정도로 신경써서 짓는다
- 소스파일 첫 부분은 고차원 개념, 알고리즘을 설명한다. 아래로 갈수록 의도를 세세하게 묘사한다. 마지막에는 가장 저차원 함수와 세부 내역이 나온다

### 개념은 빈 행으로 분리하라

- 거의 모든 코드는 왼쪽에서 오른쪽, 위에서 아래로 읽힌다. 각 행은 수식이나 절을 나타내고 일련의 행 묶음은 완결된 생각 하나를 표현한다. 생각 사이는 빈 행을 넣어 분리해야 마땅하다
- 목록 5-1을 본다. 패키지 선언부, import문, 각 함수 사이에 빈 행이 들어간다. 간단한 규칙이지만 코드의 세로 레이아웃에 심오한 영향을 미친다
- 빈 행은 새 개념을 시작한다는 시각적 단서다. 코드를 읽어 내려가다 보면 빈 행 바로 다음 줄에 눈길이 멈춘다
```java
// 목록 5-1
package fitnesse.wikitext.widgets;

import java.util.regex.*;

public class BoldWidget extends ParentWidget {
    public static final String REGEXP = "'''.+?'''";
    private static final Pattern pattern = Pattern.compile("'''(.+?)'''", Pattern.MULTILINE + Pattern.DOTALL);
    
    public BoldWidget(ParentWidget parent, String text) throws Exception {
        super(parent);
        Matcher match = pattern.matcher(text);
        match.find();
        addChildWidgets(match.group(1));
    }
    
    public String render() throws Exception {
        StringBuffer html = new StringBuffer("<b>");
        html.append(childHtml()).append("</b>");
        return html.toString();
    }
}
```

- 목록 5-2는 5-1에서 빈 행을 뺀 코드다. 코드 가독성이 떨어져서 암호처럼 보인다

```java
package fitnesse.wikitext.widgets;
import java.util.regex.*;
public class BoldWidget extends ParentWidget {
    public static final String REGEXP = "'''.+?'''";
    private static final Pattern pattern = Pattern.compile("'''(.+?)'''", Pattern.MULTILINE + Pattern.DOTALL);
    public BoldWidget(ParentWidget parent, String text) throws Exception {
        super(parent);
        Matcher match = pattern.matcher(text);
        match.find();
        addChildWidgets(match.group(1));
    }
    public String render() throws Exception {
        StringBuffer html = new StringBuffer("<b>");
        html.append(childHtml()).append("</b>");
        return html.toString();
    }
}
```

### 수직 거리

- 함수 연관 관계와 동작 방식을 이해하려고 이 함수에서 저 함수로 오가며 소스파일을 위아래로 뒤지는 등 뺑뺑이를 돌았으나 결국 미로 같은 코드 때문에 혼란만 가중된 경험이 있는가?
- 함수나 변수가 정의된 코드를 찾으려 상속 관계를 거슬러 올라간 경험이 있는가? 결코 달갑지 않은 경험이다. 시스템이 뭘 하는지 이해하고 싶은데 이 조각 저 조각이 어디 있는지
찾고 기억하느라 시간, 노력을 소모한다
- 서로 밀접한 개념은 세로로 가까이 둬야 한다. 물론 두 개념이 서로 다른 파일에 속한다면 규칙이 통하지 않는다
- 하지만 타당한 근거가 없다면 서로 밀접한 개념은 한 파일에 속해야 마땅하다. 이게 바로 protected 변수를 피해야 하는 이유 중 하나다
- 같은 파일에 속할 정도로 밀접한 두 개념은 세로 거리로 연관성을 표현한다. 여기서 연관성은 한 개념을 이해하는데 다른 개념이 중요한 정도다
- 연관성이 깊은 두 개념이 멀리 떨어져 있으면 코드를 읽는 사람이 소스파일, 클래스를 뒤지게 된다

### 변수 선언

- 변수는 쓰는 위치에 최대한 가까이 선언한다. 만든 함수는 매우 짧으므로 지역변수는 각 함수 맨 처음에 선언한다