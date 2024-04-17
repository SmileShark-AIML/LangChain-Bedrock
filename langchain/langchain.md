---
description: LangChain 통합환경을 위한 세팅을 기술합니다.
---

# LangChain 환경 구성하기

### 필요 라이브러리 설치

다음 명령을 사용하여 langchain의 최신버전을 설치하도록 하겠습니다.

```python
%pip install -U langchain langchain-community langchain-experimental langchain-core langsmith
```

### Bedrock 랭체인 통합 테스트

langchain community 라이브러리의 chat\_models를 사용하여 Bedrock을 불러오도록 하겠습니다.

```python
from langchain_community.chat_models import BedrockChat
llm = BedrockChat(
    model_id="anthropic.claude-3-haiku-20240307-v1:0", region_name="us-east-1"
)
```

```python
question = "대한민국의 수도는?"
llm.invoke(question)
```

{% tabs %}
{% tab title="output" %}
AIMessage(content='대한민국의 수도는 서울입니다.\n\n서울은 대한민국의 정치, 경제, 문화의 중심지로, 약 1000년의 역사를 가진 도시입니다. 1394년 조선 왕조의 수도로 지정된 이래 현재까지 대한민국의 수도 역할을 하고 있습니다. 서울은 한강을 중심으로 널리 발달한 거대 도시로, 면적은 약 605㎢이며 인구는 약 950만 명입니다.', response\_metadata={'model\_id': 'anthropic.claude-3-haiku-20240307-v1:0', 'usage': {'prompt\_tokens': 18, 'completion\_tokens': 175, 'total\_tokens': 193\}})
{% endtab %}
{% endtabs %}

### 프롬프트 템플릿 테스트

```python
from langchain.prompts import PromptTemplate
template = "{country}의 수도는 뭐야?"

prompt = PromptTemplate.from_template(template=template)
```

```python
prompt
```

{% tabs %}
{% tab title="output" %}
PromptTemplate(input\_variables=\['country'], template='{country}의 수도는 뭐야?')
{% endtab %}
{% endtabs %}

```python
from langchain.chains import LLMChain

llm_chain = LLMChain(prompt=prompt, llm=llm)
```

```python
llm_chain.invoke({"country": "대한민국"})
```

{% tabs %}
{% tab title="output" %}
{'country': '대한민국', 'text': '대한민국의 수도는 서울입니다.\n\n서울은 대한민국의 정치, 경제, 문화, 교육의 중심지로서 1394년부터 도읍으로 사용되어 왔습니다. 현재 약 1,000만 명의 인구가 살고 있는 국내 최대 도시이자 세계적인 메가시티 중 하나입니다.'}
{% endtab %}
{% endtabs %}

```python
input_list = [{"country": "호주"}, {"country": "중국"}, {"country": "네덜란드"}]

generated_result = llm_chain.generate(input_list)
```

{% tabs %}
{% tab title="output" %}
&#x20; generations=\[\[ChatGeneration(text='호주의 수도는 캔버라입니다.\n\n캔버라는 호주 수도 특별 지구에 위치한 내륙 도시로, 1913년 수도로 지정되었습니다. 호주의 최대 도시인 시드니나 멜버른이 아닌 별도의 도시를 건설하여 수도로 삼기로 결정했는데, 이는 주요 도시들 사이의 갈등을 피하기 위함이었습니다.\n\n캔버라는 계획도시로 건설되었으며, 의회 건물, 대사관, 국립 박물관 등 주요 기관들이 위치해 있습니다. 호주 수도권 통계구역에는 약 42만 명이 거주하고 있습니다. 캔버라는 높은 생활수준과 공원, 자연환경이 잘 보존되어 있는 것으로 잘 알려져 있습니다.', message=AIMessage(content='호주의 수도는 캔버라입니다.\n\n캔버라는 호주 수도 특별 지구에 위치한 내륙 도시로, 1913년 수도로 지정되었습니다. 호주의 최대 도시인 시드니나 멜버른이 아닌 별도의 도시를 건설하여 수도로 삼기로 결정했는데, 이는 주요 도시들 사이의 갈등을 피하기 위함이었습니다.\n\n캔버라는 계획도시로 건설되었으며, 의회 건물, 대사관, 국립 박물관 등 주요 기관들이 위치해 있습니다. 호주 수도권 통계구역에는 약 42만 명이 거주하고 있습니다. 캔버라는 높은 생활수준과 공원, 자연환경이 잘 보존되어 있는 것으로 잘 알려져 있습니다.', response\_metadata={'model\_id': 'anthropic.claude-3-sonnet-20240229-v1:0', 'usage': {'prompt\_tokens': 21, 'completion\_tokens': 301, 'total\_tokens': 322\}}))], \[ChatGeneration(text='중국의 수도는 베이징입니다. 베이징은 중국 북부 화북평원에 위치한 도시로, 정식 명칭은 베이징시(北京市)입니다. 베이징은 600년 이상 중국의 수도 역할을 해왔으며, 인구도 약 2,000만 명에 달하는 대도시입니다. 천안문 광장, 자금성, 만리장성 등 유명한 관광지가 많이 있는 도시이기도 합니다.', message=AIMessage(content='중국의 수도는 베이징입니다. 베이징은 중국 북부 화북평원에 위치한 도시로, 정식 명칭은 베이징시(北京市)입니다. 베이징은 600년 이상 중국의 수도 역할을 해왔으며, 인구도 약 2,000만 명에 달하는 대도시입니다. 천안문 광장, 자금성, 만리장성 등 유명한 관광지가 많이 있는 도시이기도 합니다.', response\_metadata={'model\_id': 'anthropic.claude-3-sonnet-20240229-v1:0', 'usage': {'prompt\_tokens': 21, 'completion\_tokens': 168, 'total\_tokens': 189\}}))], \[ChatGeneration(text='네덜란드의 수도는 암스테르담입니다.\n\n암스테르담은 네덜란드에서 가장 큰 도시이자 수도로, 아름다운 운하 도시로 유명합니다. 하지만 헤이그가 네덜란드 정부와 의회, 대법원 등 주요 행정기관이 위치한 행정수도 역할을 하고 있습니다. 암스테르담은 문화, 금융, 상업의 중심지 역할을 하는 실질적인 수도라고 할 수 있습니다.', message=AIMessage(content='네덜란드의 수도는 암스테르담입니다.\n\n암스테르담은 네덜란드에서 가장 큰 도시이자 수도로, 아름다운 운하 도시로 유명합니다. 하지만 헤이그가 네덜란드 정부와 의회, 대법원 등 주요 행정기관이 위치한 행정수도 역할을 하고 있습니다. 암스테르담은 문화, 금융, 상업의 중심지 역할을 하는 실질적인 수도라고 할 수 있습니다.', response\_metadata={'model\_id': 'anthropic.claude-3-sonnet-20240229-v1:0', 'usage': {'prompt\_tokens': 24, 'completion\_tokens': 185, 'total\_tokens': 209\}}))]] llm\_output={'model\_id': 'anthropic.claude-3-sonnet-20240229-v1:0', 'usage': defaultdict(\<class 'int'>, {'prompt\_tokens': 66, 'completion\_tokens': 654, 'total\_tokens': 720})} run=\[RunInfo(run\_id=UUID('dc0ed6cd-1f95-4dc1-a6fe-8e8f890f1656')), RunInfo(run\_id=UUID('be5d7fa7-a12b-4ea5-8ce7-cf11ab8bfbb9')), RunInfo(run\_id=UUID('df8ffbf1-f829-42f4-bfa5-71de71158bff'))]
{% endtab %}
{% endtabs %}
