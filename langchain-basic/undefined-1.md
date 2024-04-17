# 프롬프트

## LangChain 프롬프트 템플릿 활용하기

이 문서에서는 LangChain의 프롬프트 템플릿을 활용하여 질문을 생성하고, 생성된 질문을 언어 모델에 전달하여 답변을 얻는 과정을 살펴보겠습니다.

### 1. 언어 모델 초기화

* Anthropic의 Claude 모델을 사용하기 위해 `BedrockChat` 클래스를 활용하여 언어 모델을 초기화합니다.

```python
from langchain_community.chat_models import BedrockChat

llm = BedrockChat(
    model_id="anthropic.claude-3-haiku-20240307-v1:0", region_name="us-east-1"
)
```

### 2. 프롬프트 템플릿 생성 방법

#### 2.1. `PromptTemplate` 클래스 활용

* `PromptTemplate` 클래스를 사용하여 프롬프트 템플릿을 생성합니다.
* 템플릿에는 `{country}` 변수를 포함하여 나라 이름을 입력받을 수 있도록 합니다.

```python
from langchain.prompts import PromptTemplate

template = "{country}의 수도는 어디인가요?"
prompt_template = PromptTemplate.from_template(template)
```

{% tabs %}
{% tab title="output" %}
PromptTemplate(input\_variables=\['country'], template='{country}의 수도는 어디인가요?')
{% endtab %}
{% endtabs %}

#### 2.2. 프롬프트 템플릿 저장 및 로드

* 프롬프트 템플릿을 YAML 파일로 저장하고 로드할 수 있습니다.

**저장**

```python
prompt_template.save("prompts/country.yaml")
```

```yaml
_type: prompt
input_types: {}
input_variables:
- country
metadata: null
name: null
output_parser: null
partial_variables: {}
tags: null
template: "{country}\uC758 \uC218\uB3C4\uB294 \uC5B4\uB514\uC778\uAC00\uC694?"
template_format: f-string
validate_template: false
```

**로드**

```python
from langchain.prompts import load_prompt

prompt_template = load_prompt("prompts/country.yaml")
```

#### 2.3. 동적 변수 활용

* 프롬프트 템플릿에 동적 변수를 활용할 수 있습니다.
* 현재 날짜를 자동으로 입력받는 함수를 정의하고 이를 템플릿에 적용하는 예시입니다.

```python
from datetime import datetime

def get_today():
    now = datetime.now()
    return now.strftime("%B %d")

prompt_template = PromptTemplate(
    template="오늘의 날짜는 {today} 입니다. 오늘이 생일인 유명인 {n}명을 나열해 주세요.",
    input_variables=["n"],
    partial_variables={"today": get_today},
)
```

{% tabs %}
{% tab title="output" %}
PromptTemplate(input\_variables=\['n'], partial\_variables={'today': \<function get\_today at 0x12c7d0c20>}, template='오늘의 날짜는 {today} 입니다. 오늘이 생일인 유명인 {n}명을 나열해 주세요.')
{% endtab %}
{% endtabs %}

### 3. 프롬프트 생성 및 활용

* 생성된 프롬프트 템플릿에 실제 값을 입력하여 프롬프트를 생성합니다.
* 생성된 프롬프트를 언어 모델에 전달하여 답변을 얻을 수 있습니다.

```python
prompt = prompt_template.format(country="대한민국")
(prompt_template | llm).invoke({"n" : 5})
```

{% tabs %}
{% tab title="output" %}
AIMessage(content="2024년 1월 30일에 생일이 있는 유명인은 다음과 같습니다:\n\n1. 켐벨 스콧(배우) - 미국 출신 배우로 대표작으로는 '아메리칸 뷰티', '아너 가버넌스', '시카리오' 등이 있습니다.\n\n2. 크리스티나 리치(배우) - 미국 출신 배우로 '가족 어레인지먼트', '아다멘트', '눈물의 스물네 시간' 등에 출연했습니다.\n\n3. 토마스 울프(작가) - 미국의 저명한 소설가로 대표작으로 '고향을 잃어' 등이 있습니다.\n\n4. 프란체스카 아니스턴(배우) - 영국 출신 배우로 '안티크라이스트', '지금 만나러 갑니다' 등에 출연했습니다.\n\n5. 빌리 조 암스트롱(음악가) - 미국의 록밴드 Green Day의 보컬 겸 기타리스트입니다.", response\_metadata={'model\_id': 'anthropic.claude-3-haiku-20240307-v1:0', 'usage': {'prompt\_tokens': 55, 'completion\_tokens': 347, 'total\_tokens': 402\}})
{% endtab %}
{% endtabs %}

이렇게 LangChain의 프롬프트 템플릿을 다양한 방법으로 생성하고 활용하여 효과적인 질문을 만들고, 언어 모델을 통해 원하는 답변을 얻을 수 있습니다.
