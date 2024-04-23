# DatetimeOutputParser

{% hint style="info" %}
이 가이드에서는 LangChain의 DatetimeOutputParser에 대해 설명합니다. DatetimeOutputParser는 LLM(Large Language Model)의 출력을 파싱하여 날짜 및 시간 형식으로 변환하는 데 사용됩니다. 이를 통해 LLM의 출력을 날짜 및 시간 관련 작업에 활용할 수 있습니다.
{% endhint %}

### 설치

가이드를 시작하기 전에 필요한 라이브러리를 설치합니다.

```python
from langchain.output_parsers import DatetimeOutputParser
from langchain.prompts import PromptTemplate

from langchain_community.chat_models import BedrockChat

llm = BedrockChat(
    model_id="anthropic.claude-3-haiku-20240307-v1:0", region_name="us-east-1"
)
```

* `DatetimeOutputParser`: LLM의 출력을 날짜 및 시간 형식으로 파싱하는 모듈입니다.
* `PromptTemplate`: LLM에 전달할 프롬프트 템플릿을 생성하는 모듈입니다.
* `BedrockChat`: Anthropic의 ChatGPT API를 사용하기 위한 LangChain 모듈입니다.

### DatetimeOutputParser 초기화

`DatetimeOutputParser`를 초기화합니다. 이 파서는 LLM의 출력을 날짜 및 시간 형식으로 변환합니다.

```python
output_parser = DatetimeOutputParser()
```

### 프롬프트 템플릿 생성

LLM에 전달할 프롬프트 템플릿을 생성합니다. 프롬프트 템플릿은 LLM에게 수행할 작업과 출력 형식을 지정합니다.

```python
template = """Answer the users question:

{question}

{format_instructions}"""

prompt = PromptTemplate.from_template(
    template,
    partial_variables={
        "format_instructions": output_parser.get_format_instructions()
    },
)
```

{% tabs %}
{% tab title="output" %}
PromptTemplate(input\_variables=\['question'], partial\_variables={'format\_instructions': "Write a datetime string that matches the following pattern: '%Y-%m-%dT%H:%M:%S.%fZ'.\n\nExamples: 1233-12-14T10:16:09.701691Z, 1428-06-11T03:40:16.087669Z, 0186-03-07T21:56:14.839633Z\n\nReturn ONLY this string, no other words!"}, template='Answer the users question:\n\n{question}\n\n{format\_instructions}')
{% endtab %}
{% endtabs %}

* `template`은 사용자의 질문에 대한 답변 템플릿을 정의합니다.
* `PromptTemplate.from_template` 메서드를 사용하여 프롬프트 템플릿을 생성합니다.
* `partial_variables`에는 `format_instructions`를 지정하여 `DatetimeOutputParser`의 포맷 지침을 템플릿에 적용합니다.

### Chain 생성

프롬프트, LLM, 파서를 연결하여 체인을 생성합니다. 체인은 프롬프트 템플릿, LLM, 파서를 연결하여 입력을 받아 출력을 생성하는 일련의 과정입니다.

```python
chain = prompt | llm | output_parser
```

* `|` 연산자를 사용하여 프롬프트 템플릿, LLM, 파서를 연결합니다.

### LLM 실행 및 결과 파싱

LLM을 실행하고 결과를 날짜 및 시간 형식으로 파싱합니다.

```python
output = chain.invoke({"question": "Google 이 창업한 연도는?"})
print(output)
```

{% tabs %}
{% tab title="output" %}
1998-09-04 00:00:00
{% endtab %}
{% endtabs %}

* `chain.invoke` 메서드를 사용하여 체인을 실행합니다.
* `question`에는 LLM에게 질문할 내용을 지정합니다.
* 출력 결과는 `DatetimeOutputParser`에 의해 날짜 및 시간 형식으로 파싱되어 반환됩니다.

DatetimeOutputParser를 활용하면 LLM의 출력을 날짜 및 시간 형식으로 변환하여 관련 작업에 활용할 수 있습니다. 이를 통해 LLM을 날짜 및 시간 관련 애플리케이션에 통합하고, 보다 정확하고 유용한 정보를 추출할 수 있습니다.
