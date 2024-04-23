# JsonOutputParser

{% hint style="info" %}
이 가이드에서는 LangChain의 JsonOutputParser에 대해 설명합니다. JsonOutputParser는 LLM(Large Language Model)의 출력을 JSON 형식으로 파싱하는 데 사용됩니다. 이를 통해 LLM의 출력을 구조화된 데이터로 변환하여 활용할 수 있습니다.
{% endhint %}

### 설치

가이드를 시작하기 전에 필요한 라이브러리를 설치합니다.

```python
from typing import List

from langchain.prompts import PromptTemplate
from langchain_core.output_parsers import JsonOutputParser
from langchain_core.pydantic_v1 import BaseModel, Field
from langchain_community.chat_models import BedrockChat

llm = BedrockChat(
    model_id="anthropic.claude-3-haiku-20240307-v1:0", region_name="us-east-1"
)
```

* `PromptTemplate`: LLM에 전달할 프롬프트 템플릿을 생성하는 모듈입니다.
* `JsonOutputParser`: LLM의 출력을 JSON 형식으로 파싱하는 모듈입니다.
* `BaseModel`, `Field`: Pydantic 모델을 정의하는 모듈입니다.
* `BedrockChat`: Anthropic의 ChatGPT API를 사용하기 위한 LangChain 모듈입니다.

### Pydantic 객체를 사용하는 경우

#### Pydantic 모델 정의

LLM의 출력을 구조화하기 위해 Pydantic 모델을 정의합니다. Pydantic 모델은 `BaseModel`을 상속받아 정의합니다.

```python
class Topic(BaseModel):
    description: str = Field(description="Concise description about topic")
    hashtags: str = Field(description="Some keywords in hashtag format")
```

* `description`: 주제에 대한 간단한 설명을 나타내는 필드입니다.
* `hashtags`: 주제와 관련된 키워드를 해시태그 형식으로 나타내는 필드입니다.

#### JsonOutputParser 초기화

`JsonOutputParser`를 초기화할 때 `pydantic_object` 인자에 정의한 Pydantic 모델을 전달합니다.

```python
parser = JsonOutputParser(pydantic_object=Topic)
```

#### 프롬프트 템플릿 생성

LLM에 전달할 프롬프트 템플릿을 생성합니다. 프롬프트 템플릿은 LLM에게 수행할 작업과 출력 형식을 지정합니다.

```python
prompt = PromptTemplate(
    template="Answer the user query.\n{format_instructions}\n{query}\n",
    input_variables=["query"],
    partial_variables={
        "format_instructions": parser.get_format_instructions()},
)
```

* `template` 인자에는 LLM에게 전달할 프롬프트 템플릿을 지정합니다. `{format_instructions}`와 `{query}`는 런타임에 실제 값으로 대체됩니다.
* `input_variables`는 프롬프트 템플릿에서 사용되는 입력 변수를 지정합니다.
* `partial_variables`는 프롬프트 템플릿에서 사용되는 부분 변수를 지정합니다. 여기서는 `format_instructions`를 부분 변수로 지정합니다.

#### Chain 생성 및 실행

프롬프트, LLM, 파서를 연결하여 체인을 생성하고 실행합니다.

```python
chain = prompt | llm | parser

result = chain.invoke({"query": "온난화에 대해 한글을 사용하여 알려주세요."})
print(result)
```

* `|` 연산자를 사용하여 프롬프트 템플릿, LLM, 파서를 연결합니다.
* `chain.invoke` 메서드를 사용하여 체인을 실행하고, 입력 변수로 `query`를 전달합니다.
* 출력 결과는 Pydantic 모델에 따라 구조화된 JSON 형식으로 반환됩니다.

### Pydantic 객체를 사용하지 않는 경우

#### JsonOutputParser 초기화

`JsonOutputParser`를 초기화할 때 `pydantic_object` 인자를 생략합니다.

```python
parser = JsonOutputParser()
```

#### 프롬프트 템플릿 생성

LLM에 전달할 프롬프트 템플릿을 생성합니다. 프롬프트 템플릿은 LLM에게 수행할 작업과 출력 형식을 지정합니다.

```python
prompt = PromptTemplate(
    template="Answer the user query.\n{format_instructions}\n{query}\n",
    input_variables=["query"],
    partial_variables={
        "format_instructions": parser.get_format_instructions()},
)
```

* `template` 인자에는 LLM에게 전달할 프롬프트 템플릿을 지정합니다. `{format_instructions}`와 `{query}`는 런타임에 실제 값으로 대체됩니다.
* `input_variables`는 프롬프트 템플릿에서 사용되는 입력 변수를 지정합니다.
* `partial_variables`는 프롬프트 템플릿에서 사용되는 부분 변수를 지정합니다. 여기서는 `format_instructions`를 부분 변수로 지정합니다.

#### Chain 생성 및 실행

프롬프트, LLM, 파서를 연결하여 체인을 생성하고 실행합니다.

```python
chain = prompt | llm | parser

result = chain.invoke({
    "query": "온난화에 대해 알려주세요. 온난화에 대한 설명은 `description`에, 관련 키워드는 `hashtags`에 담아주세요."
})
print(result)
```

* `|` 연산자를 사용하여 프롬프트 템플릿, LLM, 파서를 연결합니다.
* `chain.invoke` 메서드를 사용하여 체인을 실행하고, 입력 변수로 `query`를 전달합니다.
* 출력 결과는 JSON 형식으로 반환되며, 필드 이름과 값은 프롬프트 템플릿에서 지정한 대로 생성됩니다.

JsonOutputParser를 활용하면 LLM의 출력을 JSON 형식으로 구조화하여 활용할 수 있습니다. Pydantic 객체를 사용하면 미리 정의된 모델에 따라 출력을 파싱할 수 있고, 사용하지 않으면 프롬프트 템플릿에서 지정한 대로 출력이 생성됩니다.
