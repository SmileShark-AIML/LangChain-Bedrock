# CommaSeparatedListOutputParser

{% hint style="info" %}
이 가이드에서는 LangChain의 CommaSeparatedListOutputParser에 대해 설명합니다. CommaSeparatedListOutputParser는 LLM(Large Language Model)의 출력을 파싱하여 쉼표로 구분된 리스트로 변환하는 데 사용됩니다. 이를 통해 LLM의 출력을 리스트 형태로 쉽게 활용할 수 있습니다.
{% endhint %}

### 설치

가이드를 시작하기 전에 필요한 라이브러리를 설치합니다.

```python
from langchain_community.chat_models import BedrockChat
from langchain.output_parsers import CommaSeparatedListOutputParser
from langchain.prompts import PromptTemplate

llm = BedrockChat(
    model_id="anthropic.claude-3-haiku-20240307-v1:0", region_name="us-east-1"
)
```

* `BedrockChat`: Anthropic의 ChatGPT API를 사용하기 위한 LangChain 모듈입니다.
* `CommaSeparatedListOutputParser`: LLM의 출력을 쉼표로 구분된 리스트로 파싱하는 모듈입니다.
* `PromptTemplate`: LLM에 전달할 프롬프트 템플릿을 생성하는 모듈입니다.

### CommaSeparatedListOutputParser 생성

`CommaSeparatedListOutputParser`를 생성합니다. 이 파서는 LLM의 출력을 쉼표로 구분된 리스트로 변환합니다.

```python
output_parser = CommaSeparatedListOutputParser()
```

### 프롬프트 템플릿 생성

LLM에 전달할 프롬프트 템플릿을 생성합니다. 프롬프트 템플릿은 LLM에게 수행할 작업과 출력 형식을 지정합니다.

```python
format_instructions = output_parser.get_format_instructions()

prompt = PromptTemplate(
    template="List five {subject}.\n{format_instructions}",
    input_variables=["subject"],
    partial_variables={"format_instructions": format_instructions},
)
```

* `output_parser.get_format_instructions()`를 사용하여 `CommaSeparatedListOutputParser`의 출력 형식을 가져옵니다.
* `PromptTemplate`을 사용하여 프롬프트 템플릿을 생성합니다.
* `template` 인자에는 LLM에게 전달할 프롬프트 템플릿을 지정합니다. `{subject}`와 `{format_instructions}`는 런타임에 실제 값으로 대체됩니다.
* `input_variables`는 프롬프트 템플릿에서 사용되는 입력 변수를 지정합니다.
* `partial_variables`는 프롬프트 템플릿에서 사용되는 부분 변수를 지정합니다. 여기서는 `format_instructions`를 부분 변수로 지정합니다.

### Chain 생성

프롬프트, LLM, 파서를 연결하여 체인을 생성합니다. 체인은 프롬프트 템플릿, LLM, 파서를 연결하여 입력을 받아 출력을 생성하는 일련의 과정입니다.

```python
chain = prompt | llm | output_parser
```

* `|` 연산자를 사용하여 프롬프트 템플릿, LLM, 파서를 연결합니다.

### LLM 실행 및 결과 파싱

LLM을 실행하고 결과를 쉼표로 구분된 리스트로 파싱합니다.

```python
result = chain.invoke(
    {"subject": "대한민국 관광명소"}
)
print(result)
```

{% tabs %}
{% tab title="output" %}
\['서울 시내 명소', '제주도 관광지', '부산 해변', '경주 역사 유적', '설악산 국립공원']
{% endtab %}
{% endtabs %}

* `chain.invoke` 메서드를 사용하여 체인을 실행합니다.
* `subject`를 인자로 전달하여 LLM에게 대한민국 관광명소를 나열하도록 요청합니다.
* 출력 결과는 쉼표로 구분된 리스트로 반환됩니다.

### Stream 모드로 LLM 실행

LLM을 Stream 모드로 실행하여 실시간으로 결과를 받아볼 수 있습니다.

```python
for s in chain.stream({"subject": "대한민국 관광명소"}):
    print(s)
```

{% tabs %}
{% tab title="output" %}
\['경복궁']

\['명동']&#x20;

\['부산 해운대']&#x20;

\['서울 타워']&#x20;

\['제주도']
{% endtab %}
{% endtabs %}

* `chain.stream` 메서드를 사용하여 체인을 Stream 모드로 실행합니다.
* 출력 결과가 실시간으로 생성되며, 각 결과는 쉼표로 구분된 리스트로 반환됩니다.

이상으로 LangChain의 CommaSeparatedListOutputParser 사용 방법에 대해 알아보았습니다. CommaSeparatedListOutputParser를 활용하면 LLM의 출력을 쉼표로 구분된 리스트로 쉽게 변환할 수 있습니다. 이를 통해 LLM의 출력을 리스트 형태로 활용하여 다양한 애플리케이션에 적용할 수 있습니다.
