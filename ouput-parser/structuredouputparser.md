# StructuredOuputParser

{% hint style="info" %}
이 가이드에서는 LangChain의 StructuredOutputParser에 대해 설명합니다. StructuredOutputParser는 LLM(Large Language Model)의 출력을 구조화된 형식으로 파싱하는 데 사용됩니다. 이를 통해 LLM의 출력을 미리 정의된 스키마에 따라 구조화하여 활용할 수 있습니다.
{% endhint %}

### 설치

가이드를 시작하기 전에 필요한 라이브러리를 설치합니다.

```python
from langchain.output_parsers import ResponseSchema, StructuredOutputParser
from langchain.prompts import PromptTemplate
from langchain_community.chat_models import BedrockChat

llm = BedrockChat(
    model_id="anthropic.claude-3-haiku-20240307-v1:0", region_name="us-east-1"
)
```

* `ResponseSchema`: 응답 스키마를 정의하는 모듈입니다.
* `StructuredOutputParser`: 응답 스키마를 기반으로 LLM의 출력을 구조화하는 모듈입니다.
* `PromptTemplate`: LLM에 전달할 프롬프트 템플릿을 생성하는 모듈입니다.
* `BedrockChat`: Anthropic의 ChatGPT API를 사용하기 위한 LangChain 모듈입니다.

### 응답 스키마 정의

LLM의 출력을 구조화하기 위해 응답 스키마를 정의합니다. 응답 스키마는 `ResponseSchema` 객체를 사용하여 정의합니다.

```python
response_schemas = [
    ResponseSchema(name="answer", description="사용자의 질문에 대한 답변"),
    ResponseSchema(
        name="source",
        description="사용자의 질문에 답하기 위해 사용된 출처, 웹사이트 이여야 합니다.",
    ),
]
```

* `name`: 응답 스키마의 이름을 지정합니다.
* `description`: 응답 스키마에 대한 설명을 지정합니다.

### StructuredOutputParser 초기화

응답 스키마를 기반으로 `StructuredOutputParser`를 초기화합니다.

```python
output_parser = StructuredOutputParser.from_response_schemas(response_schemas)
```

* `from_response_schemas` 메서드를 사용하여 응답 스키마를 기반으로 `StructuredOutputParser`를 초기화합니다.

### 프롬프트 템플릿 생성

LLM에 전달할 프롬프트 템플릿을 생성합니다. 프롬프트 템플릿은 LLM에게 수행할 작업과 출력 형식을 지정합니다.

```python
format_instructions = output_parser.get_format_instructions()
prompt = PromptTemplate(
    template="answer the users question as best as possible.\n{format_instructions}\n{question}",
    input_variables=["question"],
    partial_variables={"format_instructions": format_instructions},
)
```

* `output_parser.get_format_instructions()`를 사용하여 `StructuredOutputParser`의 출력 형식을 가져옵니다.
* `PromptTemplate`을 사용하여 프롬프트 템플릿을 생성합니다.
* `template` 인자에는 LLM에게 전달할 프롬프트 템플릿을 지정합니다. `{format_instructions}`와 `{question}`은 런타임에 실제 값으로 대체됩니다.
* `input_variables`는 프롬프트 템플릿에서 사용되는 입력 변수를 지정합니다.
* `partial_variables`는 프롬프트 템플릿에서 사용되는 부분 변수를 지정합니다. 여기서는 `format_instructions`를 부분 변수로 지정합니다.

### Chain 생성

프롬프트, LLM, 파서를 연결하여 체인을 생성합니다. 체인은 프롬프트 템플릿, LLM, 파서를 연결하여 입력을 받아 출력을 생성하는 일련의 과정입니다.

```python
chain = prompt | llm | output_parser
```

* `|` 연산자를 사용하여 프롬프트 템플릿, LLM, 파서를 연결합니다.

### LLM 실행 및 결과 파싱

LLM을 실행하고 결과를 구조화된 형식으로 파싱합니다.

```python
result = chain.invoke({"question": "강남역에서 2시에 출발하는 지하철목록을 알려주세요"})
print(result)
```

{% tabs %}
{% tab title="output" %}
{'answer': '강남역에서 2시에 출발하는 지하철은 2호선이 있습니다. 2호선은 1년 365일 끊임없이 운행되며, 평일 기준 오후 2시 기준 약 4-5분 간격으로 운행됩니다.', 'source': 'https://www.seoulmetro.co.kr/kr/cyberStation.do'}
{% endtab %}
{% endtabs %}

* `chain.invoke` 메서드를 사용하여 체인을 실행합니다.
* `question`을 인자로 전달하여 LLM에게 질문을 합니다.
* 출력 결과는 응답 스키마에 따라 구조화된 형식으로 반환됩니다.

### Stream 모드로 LLM 실행

LLM을 Stream 모드로 실행하여 실시간으로 결과를 받아볼 수 있습니다.

```python
for s in chain.stream({"question": "세종대왕의 업적은 무엇인가요?"}):
    print(s)
```

{% tabs %}
{% tab title="output" %}
{'answer': '세종대왕은 한글 창제, 농업 기술 발전, 천문학 및 지리학 발전, 역법 개혁 등 많은 업적을 남겼습니다. 특히 한글 창제는 가장 큰 업적으로 꼽힙니다. 그는 기존의 한자 문자가 어려워 일반 백성들이 배우기 힘들다는 것을 인식하고, 쉽고 편리한 새로운 문자인 한글을 만들어 내었습니다. 이를 통해 문화와 학문, 행정 등 여러 분야에서 큰 발전을 이루었습니다.', 'source': 'https://ko.wikipedia.org/wiki/%EC%84%B8%EC%a2%85%EB%8c%80%ec%99%95'}
{% endtab %}
{% endtabs %}

* `chain.stream` 메서드를 사용하여 체인을 Stream 모드로 실행합니다.
* 출력 결과가 실시간으로 생성되며, 각 결과는 응답 스키마에 따라 구조화된 형식으로 반환됩니다.

StructuredOutputParser를 활용하면 LLM의 출력을 미리 정의된 스키마에 따라 구조화하여 활용할 수 있습니다. 이를 통해 LLM의 출력을 보다 체계적이고 일관된 방식으로 처리할 수 있습니다.
