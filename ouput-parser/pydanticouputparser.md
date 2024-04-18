# PydanticOuputParser

{% hint style="info" %}
이 가이드에서는 LangChain의 PydanticOutputParser에 대해 설명합니다. PydanticOutputParser는 LLM(Large Language Model)의 출력을 파싱하여 구조화된 데이터로 변환하는 데 사용됩니다. 이를 통해 LLM의 출력을 보다 쉽게 활용할 수 있습니다.
{% endhint %}

### 설치

가이드를 시작하기 전에 필요한 라이브러리를 설치합니다.

```python
from langchain_community.chat_models import BedrockChat
from langchain.prompts import PromptTemplate
from langchain.output_parsers import PydanticOutputParser
from langchain_core.pydantic_v1 import BaseModel, Field

llm = BedrockChat(
    model_id="anthropic.claude-3-haiku-20240307-v1:0", region_name="us-east-1"
)
```

* `BedrockChat`: Anthropic의 ChatGPT API를 사용하기 위한 LangChain 모듈입니다.
* `PromptTemplate`: LLM에 전달할 프롬프트 템플릿을 생성하는 모듈입니다.
* `PydanticOutputParser`: LLM의 출력을 Pydantic 모델로 파싱하는 모듈입니다.
* `BaseModel`, `Field`: Pydantic 모델을 정의하기 위한 모듈입니다.

### 예제 데이터

이메일 대화 내용을 변수에 저장합니다. 이 데이터는 LLM에 전달되어 요약 및 주요 정보 추출에 사용됩니다.

```python
email_conversation = """From: 아마존웹서비스 <aws-korea-event@amazon.com>
To: 김현민 (hm.kim@smileshark.kr)
Subject: AWS Summit Seoul 2024 | 등록이 완료되었습니다.


 
AWS Summit Seoul에 등록해 주셔서 감사합니다.

 
등록하신 행사의 일정은 아래와 같습니다.
행사 일주일 전과 하루 전에 입력하신 이메일과 휴대폰 번호로 입장 시 필요한 QR코드가 발송됩니다. 행사 당일 받으신 QR코드를 보여주셔야 입장이 가능하며, QR코드가 없는 경우 입장이 불가능합니다.

행사 전 다시 한번 주요 행사 내용을 포함하여 입장 관련 자세한 내용을 이메일로 안내 드리겠습니다.
 
 
5월 16일 AWS Summit Seoul
 
 
5월
16
 	
AWS Summit Seoul Day 1 | 강연 소개
날짜:	2024년 5월 16일 (목)
시간:	9:30 – 18:00 (입장 등록 시작: 8:00)
장소:	코엑스 컨벤션 센터
(장소 확인하기)
추가:	Google Cal | iCal or Outlook
 
 
5월 17일 AWS Summit Seoul
 
 
5월
17
 	
AWS Summit Seoul Day 2 | 강연 소개
날짜:	2024년 5월 17일 (금)
시간:	9:30 – 18:00 (입장 등록 시작: 8:00)
장소:	코엑스 컨벤션 센터
(장소 확인하기)
추가:	Google Cal | iCal or Outlook


 
감사합니다.
AWS 드림
"""
```

### Pydantic 모델 정의

출력 결과를 파싱할 Pydantic 모델을 정의합니다. 이 모델은 LLM 출력의 구조를 정의하고, 출력을 파싱하는 데 사용됩니다.

```python
class EmailSummary(BaseModel):
    person: str = Field(description="메일을 보낸 사람")  
    email: str = Field(description="메일을 보낸 사람의 이메일 주소")
    subject: str = Field(description="메일 제목")
    summary: str = Field(description="메일 본문을 요약한 텍스트")
    date: str = Field(description="메일 본문에 언급된 미팅 날짜와 시간")

parser = PydanticOutputParser(pydantic_object=EmailSummary)
```

* `EmailSummary` 클래스는 `BaseModel`을 상속받아 정의됩니다.
* 각 필드는 `Field`를 사용하여 정의되며, `description` 인자를 통해 필드에 대한 설명을 추가할 수 있습니다.
* `PydanticOutputParser`는 `EmailSummary` 클래스를 인자로 받아 생성됩니다.

### 프롬프트 템플릿 생성

LLM에 전달할 프롬프트 템플릿을 생성합니다. 프롬프트 템플릿은 LLM에게 수행할 작업과 출력 형식을 지정합니다.

```python
prompt = PromptTemplate.from_template(
    """
You are a helpful assistant. Please answer the following questions in KOREAN.

QUESTION: 
{question}

EMAIL CONVERSATION:
{email_conversation}

FORMAT:
{format}
"""
)

prompt = prompt.partial(format=parser.get_format_instructions())
```

* `PromptTemplate.from_template` 메서드를 사용하여 프롬프트 템플릿을 생성합니다.
* `question`, `email_conversation`, `format` 변수는 런타임에 실제 값으로 대체됩니다.
* `parser.get_format_instructions()`를 사용하여 `PydanticOutputParser`의 출력 형식을 프롬프트에 추가합니다.

### Chain 생성

프롬프트와 LLM을 연결하여 체인을 생성합니다. 체인은 프롬프트 템플릿과 LLM을 연결하여 입력을 받아 출력을 생성하는 일련의 과정입니다.

```python
chain = prompt | llm
```

* `|` 연산자를 사용하여 프롬프트 템플릿과 LLM을 연결합니다.

### LLM 실행 및 결과 파싱

LLM을 실행하고 결과를 JSON 형태로 출력합니다. 그런 다음 `PydanticOutputParser`를 사용하여 JSON 출력을 `EmailSummary` 객체로 파싱합니다.

```python
response = chain.invoke(
    {
        "email_conversation": email_conversation,
        "question": "이메일 내용중 주요 내용을 추출해 주세요.",
    
    }
)

print(response.content)
```

{% tabs %}
{% tab title="output" %}
{ "person": "김현민", "email": "hm.kim@smileshark.kr", "subject": "AWS Summit Seoul 2024 | 등록이 완료되었습니다.", "summary": "AWS Summit Seoul에 등록해 주셔서 감사합니다. 입장 시 필요한 QR코드가 행사 일주일 전과 하루 전에 발송될 예정이며, 행사 당일 QR코드를 보여주셔야 입장이 가능합니다. 행사 전 주요 행사 내용과 입장 관련 자세한 내용이 이메일로 안내될 것입니다.", "date": "2024년 5월 16일(목) - 17일(금)" }
{% endtab %}
{% endtabs %}

* `chain.invoke` 메서드를 사용하여 체인을 실행합니다.
* `email_conversation`과 `question`을 인자로 전달합니다.
* 출력 결과는 `response.content`에 JSON 형태로 저장됩니다.

```python
parsed_result = parser.parse(response.content)
print(parsed_result)
```

{% tabs %}
{% tab title="output" %}
EmailSummary(person='김현민', email='hm.kim@smileshark.kr', subject='AWS Summit Seoul 2024 | 등록이 완료되었습니다.', summary='AWS Summit Seoul에 등록해 주셔서 감사합니다. 입장 시 필요한 QR코드가 행사 일주일 전과 하루 전에 발송될 예정이며, 행사 당일 QR코드를 보여주셔야 입장이 가능합니다. 행사 전 주요 행사 내용과 입장 관련 자세한 내용이 이메일로 안내될 것입니다.', date='2024년 5월 16일(목) - 17일(금)')
{% endtab %}
{% endtabs %}

* `parser.parse` 메서드를 사용하여 JSON 출력을 `EmailSummary` 객체로 파싱합니다.

```python
print(parsed_result.email)  
```

* 파싱된 객체의 개별 필드에 접근할 수 있습니다.

### Chain에 Parser 추가

체인 생성 시 파서를 추가하여 LLM 실행 결과를 바로 `EmailSummary` 객체로 받을 수 있습니다. 이렇게 하면 별도의 파싱 과정 없이 구조화된 데이터를 직접 얻을 수 있습니다.

```python
chain = prompt | llm | parser

response = chain.invoke(
    {
        "email_conversation": email_conversation, 
        "question": "이메일 내용중 주요 내용을 추출해 주세요.",
    }
)
```

* 체인 생성 시 `parser`를 추가합니다.
* `chain.invoke` 메서드를 사용하여 체인을 실행하면, 출력 결과가 `EmailSummary` 객체로 반환됩니다.

```python
import json

dat = json.loads(response.content)
dat["email"]
```

{% tabs %}
{% tab title="output" %}
'hm.kim@smileshark.kr'
{% endtab %}
{% endtabs %}

PydanticOutputParser를 활용하면 LLM의 출력을 보다 구조적이고 활용하기 쉬운 형태로 변환할 수 있습니다. 이를 통해 LLM을 다양한 애플리케이션에 통합하고, 출력 결과를 효과적으로 처리할 수 있습니다.
