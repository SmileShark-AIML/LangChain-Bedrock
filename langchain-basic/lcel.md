---
description: LCEL(LangChain Expression Language) 기본사항에 대해 기술합니다.
---

# LCEL 기본요소

#### LCEL (LangChain Expression Language)의 기본인 프롬프트, 모델, 출력파서에 대해 알아봅시다

#### 프롬프트

```python
from langchain.prompts import PromptTemplate

template = "{country}의 수도는 어디인가요?"

prompt_template = PromptTemplate.from_template(template)
prompt_template
```

{% tabs %}
{% tab title="output" %}
PromptTemplate(input\_variables=\['country'], template='{country}의 수도는 어디인가요?')
{% endtab %}
{% endtabs %}

```python
prompt = prompt_template.format(country="대한민국")
prompt
```

{% tabs %}
{% tab title="output" %}
'대한민국의 수도는 어디인가요?'
{% endtab %}
{% endtabs %}

#### LLM (모델)

모델로는, 아마존 베드락 Anthropic 사의 Claude3 Haiku를 사용하도록 하겠습니다.

```python
from langchain_community.chat_models import BedrockChat
from langchain.callbacks.streaming_stdout import StreamingStdOutCallbackHandler

sonnet_arn = "anthropic.claude-3-haiku-20240307-v1:0"

model = BedrockChat(
    region_name="us-east-1",
    provider="anthropic",
    model_id=sonnet_arn,
    model_kwargs={
        # 'max_tokens_to_sample': 2048,
        'temperature': 1.0,
                #   'top_k': 250,
                #   'top_p': 0.999
                  },
    streaming=True,
    callbacks=[StreamingStdOutCallbackHandler()],
)
```

| 파라미터                   | 필수 여부                   | 설명                                                                                                                                  | 예시                                                                         |
| ---------------------- | ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| `max_tokens_to_sample` | Required (Streaming 제외) | 모델이 생성할 수 있는 최대 토큰 수를 지정합니다. 이 값을 초과하면 생성이 중단됩니다.                                                                                   | `max_tokens_to_sample=100`은 최대 100개의 토큰을 생성하도록 설정합니다.                      |
| `temperature`          | Required                | 0에서 1 사이의 값으로, 모델의 출력 확률 분포의 무작위성을 조절합니다. 값이 높을수록 더 다양하고 창의적인 출력을 생성하지만, 일관성은 떨어질 수 있습니다. 값이 낮을수록 더 정확하고 일관된 출력을 생성하지만, 다양성은 줄어듭니다. | `temperature=0.7`은 확률 분포의 무작위성을 적당한 수준으로 유지합니다.                            |
| `top_k`                | Optional                | 확률이 높은 상위 k개의 토큰만 고려하여 출력을 생성합니다. 이 값을 설정하면 확률이 낮은 토큰은 제외되어 더 관련성 높은 출력을 얻을 수 있습니다.                                                 | `top_k=50`은 확률이 높은 상위 50개의 토큰만 고려하여 출력을 생성합니다.                             |
| `top_p`                | Optional                | 0에서 1 사이의 값으로, 누적 확률이 p가 될 때까지의 토큰만 고려하여 출력을 생성합니다. 이 값을 설정하면 확률이 낮은 토큰은 제외되어 더 관련성 높은 출력을 얻을 수 있습니다.                               | `top_p=0.95`는 누적 확률이 0.95가 될 때까지의 토큰만 고려하여 출력을 생성합니다.                      |
| `stop`                 | Optional                | 생성을 중단할 문자열이나 문자열 리스트를 지정합니다. 모델이 이 값을 출력하면 생성이 중단됩니다.                                                                              | `stop=[".", "?", "!"]`은 마침표, 물음표, 느낌표가 나오면 생성을 중단합니다.                      |
| `frequency_penalty`    | Optional                | 0 이상의 값으로, 이미 생성된 토큰의 빈도에 따라 패널티를 부과하여 반복을 줄입니다. 값이 클수록 이전에 나온 토큰이 다시 나올 확률이 낮아집니다.                                                 | `frequency_penalty=0.5`는 이미 생성된 토큰의 빈도에 따라 적당한 수준의 패널티를 부과합니다.             |
| `presence_penalty`     | Optional                | 0 이상의 값으로, 이미 생성된 토큰의 존재 여부에 따라 패널티를 부과하여 반복을 줄입니다. 값이 클수록 이전에 나온 토큰이 다시 나올 확률이 낮아집니다.                                              | `presence_penalty=0.3`은 이미 생성된 토큰의 존재 여부에 따라 적당한 수준의 패널티를 부과합니다.           |
| `logit_bias`           | Optional                | 특정 토큰의 확률을 조정할 수 있는 dic입니다. 양수 값은 해당 토큰의 확률을 높이고, 음수 값은 낮춥니다.                                                                       | `logit_bias={1234: 1.0, 5678: -1.0}`은 토큰 1234의 확률을 높이고, 토큰 5678의 확률을 낮춥니다. |

#### Output 파서

출력형태를 지정하는 아웃풋 파서입니다.

```python
from langchain_core.output_parsers import StrOutputParser

output_parser = StrOutputParser()
```

#### 체인생성

LECL을 사용하여 생성한 프롬프트, 모델, 아웃풋 파서를 연결하고 실행해보도록 하겠습니다.

```python
chain = prompt_template | model | output_parser
```

```python
chain.invoke({"country" : "대한민국"})
```

{% tabs %}
{% tab title="output" %}
대한민국의 수도는 서울입니다.

서울은 대한민국의 행정, 외교, 경제, 문화의 중심지로, 1394년부터 조선왕조의 수도로 기능해왔습니다. 현재 대한민국의 정치, 경제, 사회, 문화의 중심지 역할을 하고 있습니다.

서울은 한반도의 중앙에 위치하고 있으며, 인구 약 1,000만 명의 메가시티로 성장했습니다. 한강을 중심으로 한 도시경관과 다양한 문화유산, 현대 건축물 등이 어우러진 글로벌 도시입니다.
{% endtab %}
{% endtabs %}

