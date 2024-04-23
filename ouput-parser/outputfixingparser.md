# OutputFixingParser

{% hint style="info" %}
이 가이드에서는 LangChain의 OutputFixingParser에 대해 설명합니다. OutputFixingParser는 잘못된 형식의 출력을 수정하여 원하는 형식으로 변환하는 데 사용됩니다. 이를 통해 LLM(Large Language Model)의 출력이 항상 완벽하지 않더라도 파싱 가능한 형태로 변환할 수 있습니다.
{% endhint %}

### 설치

가이드를 시작하기 전에 필요한 라이브러리를 설치합니다.

```python
from langchain_community.chat_models import BedrockChat
from langchain.output_parsers import PydanticOutputParser
from langchain_core.pydantic_v1 import BaseModel, Field
from typing import List

llm = BedrockChat(
    model_id="anthropic.claude-3-haiku-20240307-v1:0", region_name="us-east-1"
)
```

* `BedrockChat`: Anthropic의 ChatGPT API를 사용하기 위한 LangChain 모듈입니다.
* `PydanticOutputParser`: LLM의 출력을 Pydantic 모델로 파싱하는 모듈입니다.
* `BaseModel`, `Field`: Pydantic 모델을 정의하는 모듈입니다.
* `List`: 리스트 타입 힌트를 위한 모듈입니다.

### Pydantic 모델 정의

출력 결과를 파싱할 Pydantic 모델을 정의합니다.

```python
class Actor(BaseModel):
    name: str = Field(description="name of an actor")
    film_names: List[str] = Field(
        description="list of names of films they starred in")
```

* `Actor` 클래스는 배우의 이름과 출연 영화 목록을 나타내는 Pydantic 모델입니다.

### PydanticOutputParser 초기화

`PydanticOutputParser`를 초기화하고 Pydantic 모델을 전달합니다.

```python
parser = PydanticOutputParser(pydantic_object=Actor)
```

### 잘못된 형식의 출력 예시

LLM이 잘못된 형식으로 출력한 예시를 살펴보겠습니다.

```python
misformatted = "{'name': 'Tom Hanks', 'film_names': ['Forrest Gump']}"
```

{% tabs %}
{% tab title="output" %}
"{'name': 'Tom Hanks', 'film\_names': \['Forrest Gump']}"
{% endtab %}
{% endtabs %}

* `misformatted` 변수에는 JSON 형식이 잘못된 문자열이 저장되어 있습니다.

### 잘못된 형식의 출력 파싱 시도

`PydanticOutputParser`를 사용하여 잘못된 형식의 출력을 파싱하려고 시도합니다.

```python
parser.parse(misformatted)
```

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

* 잘못된 형식의 출력을 파싱하려고 시도하면 `OutputParserException` 오류가 발생합니다.

### OutputFixingParser 초기화

`OutputFixingParser`를 초기화하고 기존 파서와 LLM을 전달합니다.

```python
from langchain.output_parsers import OutputFixingParser

new_parser = OutputFixingParser.from_llm(parser=parser, llm=llm)
```

* `OutputFixingParser.from_llm` 메서드를 사용하여 `OutputFixingParser`를 초기화합니다.
* `parser` 인자에는 기존의 `PydanticOutputParser`를 전달합니다.
* `llm` 인자에는 사용할 LLM을 전달합니다.

### 잘못된 형식의 출력 수정 및 파싱

`OutputFixingParser`를 사용하여 잘못된 형식의 출력을 수정하고 파싱합니다.

```python
actor = new_parser.parse(misformatted)
print(actor)
```

{% tabs %}
{% tab title="output" %}
Actor(name='Tom Hanks', film\_names=\['Forrest Gump'])
{% endtab %}
{% endtabs %}

* `new_parser.parse` 메서드를 사용하여 잘못된 형식의 출력을 수정하고 파싱합니다.
* 수정된 출력은 `Actor` 객체로 반환됩니다.

OutputFixingParser를 활용하면 LLM의 출력이 항상 완벽하지 않더라도 파싱 가능한 형태로 변환할 수 있습니다. 이를 통해 LLM을 보다 안정적으로 활용하고, 다양한 애플리케이션에 통합할 수 있습니다.
