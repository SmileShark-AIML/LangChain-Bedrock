# EnumOutputParser

{% hint style="info" %}
이 가이드에서는 LangChain의 EnumOutputParser에 대해 설명합니다. EnumOutputParser는 LLM(Large Language Model)의 출력을 파싱하여 사전에 정의된 열거형(Enum) 값으로 변환하는 데 사용됩니다. 이를 통해 LLM의 출력을 제한된 범위 내에서 해석하고 활용할 수 있습니다.
{% endhint %}

### 설치

가이드를 시작하기 전에 필요한 라이브러리를 설치합니다.

```python
from langchain.output_parsers.enum import EnumOutputParser
from langchain_community.chat_models import BedrockChat

llm = BedrockChat(
    model_id="anthropic.claude-3-haiku-20240307-v1:0", region_name="us-east-1"
)
```

* `EnumOutputParser`: LLM의 출력을 열거형 값으로 파싱하는 모듈입니다.
* `BedrockChat`: Anthropic의 ChatGPT API를 사용하기 위한 LangChain 모듈입니다.

### 열거형 정의

EnumOutputParser에서 사용할 열거형을 정의합니다. 여기서는 색상을 나타내는 `Colors` 열거형을 정의합니다.

```python
from enum import Enum

class Colors(Enum):
    RED = "빨간색"
    GREEN = "초록색" 
    BLUE = "파란색"
```

* `Enum` 클래스를 상속받아 `Colors` 열거형을 정의합니다.
* 각 열거형 멤버에는 해당 색상의 한글 이름을 값으로 할당합니다.

### EnumOutputParser 초기화

`EnumOutputParser`를 초기화할 때 `enum` 인자에 정의한 열거형을 전달합니다.

```python
parser = EnumOutputParser(enum=Colors)
```

### 프롬프트 템플릿 생성

LLM에 전달할 프롬프트 템플릿을 생성합니다. 프롬프트 템플릿은 LLM에게 수행할 작업과 출력 형식을 지정합니다.

```python
from langchain_core.prompts import PromptTemplate

prompt = PromptTemplate.from_template(
    """다음의 물체는 어떤 색깔인가요?

Object: {object}

Instructions: {instructions}"""
).partial(instructions=parser.get_format_instructions())
```

* `PromptTemplate.from_template` 메서드를 사용하여 프롬프트 템플릿을 생성합니다.
* `object`는 색상을 판단할 물체를 나타내는 입력 변수입니다.
* `instructions`는 `EnumOutputParser`의 형식 지침을 나타내는 부분 변수입니다.

### Chain 생성

프롬프트, LLM, 파서를 연결하여 체인을 생성합니다. 체인은 프롬프트 템플릿, LLM, 파서를 연결하여 입력을 받아 출력을 생성하는 일련의 과정입니다.

```python
chain = prompt | llm | parser
```

* `|` 연산자를 사용하여 프롬프트 템플릿, LLM, 파서를 연결합니다.

### LLM 실행 및 결과 파싱

LLM을 실행하고 결과를 열거형 값으로 파싱합니다.

```python
response = chain.invoke({"object": "하늘"})
print(response)
```

{% tabs %}
{% tab title="output" %}
Colors.BLUE
{% endtab %}
{% endtabs %}

* `chain.invoke` 메서드를 사용하여 체인을 실행합니다.
* `object`에는 색상을 판단할 물체를 지정합니다.
* 출력 결과는 `EnumOutputParser`에 의해 열거형 값으로 파싱되어 반환됩니다.

EnumOutputParser를 활용하면 LLM의 출력을 사전에 정의된 열거형 값으로 변환하여 제한된 범위 내에서 해석하고 활용할 수 있습니다. 이를 통해 LLM의 출력을 보다 명확하고 일관되게 처리할 수 있습니다.
