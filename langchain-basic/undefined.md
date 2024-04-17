# 스키마와 체인 호출

{% hint style="info" %}
랭체인(LangChain)에서는 체인을 호출하는 다양한 방법을 제공합니다. 동기 방법과 비동기 방법으로 구분할 수 있으며, 각각의 방법은 다음과 같은 특징을 가지고 있습니다.
{% endhint %}

#### 동기적 체인 호출

`stream`: 체인을 호출하고 응답의 청크를 스트리밍합니다. 이 방법은 응답이 크거나 실시간으로 처리해야 할 때 유용합니다. 응답의 각 부분은 생성되는 즉시 사용할 수 있습니다.

```python
text = ""
for s in chain.stream({"country": "대한민국"}):
    text += s

print("\n\n\n\n최종 출력물 : \n" + text)
```

{% tabs %}
{% tab title="output" %}
대한민국의 수도는 서울입니다.

서울은 대한민국의 정치, 경제, 문화의 중심지로서 1394년부터 대한민국 수도의 역할을 해왔습니다. 서울은 인구 약 1,000만 명 이상이 거주하는 대도시이며, 국회, 청와대, 대법원 등 주요 국가기관들이 위치해 있습니다.

최종 출력물 :&#x20;

대한민국의 수도는 서울입니다.

서울은 대한민국의 정치, 경제, 문화의 중심지로서 1394년부터 대한민국 수도의 역할을 해왔습니다. 서울은 인구 약 1,000만 명 이상이 거주하는 대도시이며, 국회, 청와대, 대법원 등 주요 국가기관들이 위치해 있습니다.
{% endtab %}
{% endtabs %}

`invoke`: 단일 입력에 대해 체인을 호출합니다. 이 방법은 체인을 동기적으로 실행하고 완료될 때까지 기다립니다. 응답은 한 번에 반환됩니다.

```python
invoke_text = chain.invoke({"country": "호주"})
invoke_text
```

{% tabs %}
{% tab title="output" %}
'호주의 수도는 캔버라(Canberra)입니다.\n\n캔버라는 1927년에 수도로 선정되었으며, 시드니와 멜버른 사이에 위치해 있습니다. 인구는 약 42만 명으로, 호주에서 8번째로 큰 도시입니다. 호주 수도권 행정 중심지이자, 정부 기관과 대사관이 소재해 있는 정치적 중심지입니다.'
{% endtab %}
{% endtabs %}

`batch`: 입력 목록에 대해 체인을 호출합니다. 이 방법은 여러 입력을 한 번에 처리할 때 유용합니다. 체인은 각 입력에 대해 순차적으로 실행되고 모든 응답이 반환됩니다.

```python
# 주어진 토픽 리스트를 batch 처리하는 함수 호출
batch_text = chain.batch(
    [
        {"country": "중국"},
        {"country": "말레이시아"}
    ],
    config={
        "max_concurrency": 1
    }
    )
batch_text
```

{% tabs %}
{% tab title="output" %}
\['중국의 수도는 베이징(北京)입니다.\n\n베이징은 중국의 정치, 문화, 교육, 경제의 중심지로, 세계에서 가장 큰 도시 중 하나입니다. 과거 중국의 수도인 이곳은 황제의 궁전인 자Forbidden City와 천안문광장 등 유명한 역사적 유적들이 있습니다. 베이징은 또한 중국의 주요 정치, 경제, 문화 기관들이 있는 중심 도시이기도 합니다.', '말레이시아의 수도는 쿠알라룸푸르(Kuala Lumpur)입니다.\n\n쿠알라룸푸르는 말레이시아의 경제, 정치, 문화의 중심지로 이 도시에는 말레이시아 정부의 주요 기관들이 위치해 있습니다. 또한 높은 빌딩, 현대적인 인프라, 다양한 문화와 맛있는 음식으로 유명한 관광도시이기도 합니다.\n\n말레이시아의 행정수도는 푸트라자야(Putrajaya)인데, 이는 쿠알라룸푸르에서 남쪽으로 약 25km 떨어진 곳에 위치해 있습니다. 하지만 쿠알라룸푸르가 여전히 말레이시아의 실질적인 수도 역할을 하고 있습니다.']
{% endtab %}
{% endtabs %}

#### 비동기적 체인 호출

`ainvoke`: 단일 입력에 대해 체인을 비동기적으로 호출합니다. 이 방법은 `invoke`의 비동기 버전으로, 체인이 백그라운드에서 실행되는 동안 다른 작업을 수행할 수 있습니다.

```python
ainvoke_text = await chain.ainvoke({"country": "로마"})
ainvoke_text
```

{% tabs %}
{% tab title="output" %}
'로마의 수도는 이탈리아의 수도 로마입니다.\n\n로마는 역사적으로 서로마제국의 수도였으며, 현재도 이탈리아의 정치, 경제, 문화의 중심지 역할을 하고 있습니다. 유명한 관광 명소로는 콜로세움, 바티칸 시국, 트레비 분수 등이 있습니다.'
{% endtab %}
{% endtabs %}

`abatch`: 입력 목록에 대해 체인을 비동기적으로 호출합니다. 이 방법은 `batch`의 비동기 버전으로, 여러 입력을 동시에 처리할 수 있습니다.

```python
# 주어진 토픽 리스트를 batch 처리하는 함수 호출
abatch_text = await chain.abatch(
    [
        {"country": "중국"},
        {"country": "말레이시아"}
    ],
    config={
        "max_concurrency": 1
    }
    )
batch_text
```

{% tabs %}
{% tab title="output" %}
\['중국의 수도는 베이징(北京)입니다.\n\n- 베이징은 중국의 정치, 문화, 교육의 중심지로 중국에서 가장 큰 도시입니다.\n\n- 중국의 수도는 1949년 건국 이후 베이징이 되었습니다. 그 전에는 중국의 수도가 난징(南京)이었습니다.\n\n- 베이징은 중국 북부 화북평원에 위치하고 있으며, 중국의 13개 직辖시 중 하나입니다.\n\n- 베이징은 역사적으로 중국 수도의 지위를 오랫동안 유지해왔으며, 천안문광장, 자금성 등 유명한 관광지가 있는 도시입니다.', '말레이시아의 수도는 쿠알라룸푸르(Kuala Lumpur)입니다.\n\n쿠알라룸푸르는 말레이시아의 정치, 경제, 문화의 중심지로 1957년 말레이시아 독립 이후 수도 역할을 해왔습니다. \n\n주요 특징은 다음과 같습니다:\n\n- 말레이반도 서부 중부 지역에 위치\n- 인구 약 180만명(2020년 기준)\n- 다국적 기업 본사와 각종 연방정부 청사가 소재\n- 말레이시아의 상징적인 건축물인 쌍둥이 고층 빌딩(Petronas Twin Towers)이 있음\n- 다양한 문화와 종교가 공존하는 다인종 도시\n\n따라서 말레이시아의 수도이자 행정, 경제, 문화의 중심지는 쿠알라룸푸르라고 말할 수 있습니다.']
{% endtab %}
{% endtabs %}

#### 스키마

1. `input_schema`: Runnable의 구조에서 자동으로 생성된 입력 Pydantic 모델입니다. 이 스키마는 체인의 입력 매개변수를 정의하고 유효성을 검사하는 데 사용됩니다.
2. `output_schema`: Runnable의 구조에서 자동으로 생성된 출력 Pydantic 모델입니다. 이 스키마는 체인의 출력 형식을 정의하고 유효성을 검사하는 데 사용됩니다.

랭체인에서 제공하는 이러한 다양한 체인 호출 방법과 스키마를 활용하면 체인의 실행을 유연하게 제어할 수 있습니다.

동기 방법은 체인의 실행을 순차적으로 처리하고 결과를 즉시 받을 수 있는 반면, 비동기 방법은 체인을 병렬로 실행하고 다른 작업을 동시에 수행할 수 있습니다.

입력과 출력 스키마를 사용하여 체인의 입출력 데이터를 검증하고 일관성을 유지할 수 있습니다.

```python
chain.input_schema.schema()
```

{% tabs %}
{% tab title="output" %}
{'title': 'PromptInput', 'type': 'object', 'properties': {'country': {'title': 'Country', 'type': 'string'\}}}
{% endtab %}
{% endtabs %}

```python
import json

def print_schema(schema):
    print(json.dumps(schema, indent=4))

input_schema = {
    "type": "object",
    "properties": {
        "name": {"type": "string"},
        "age": {"type": "integer", "minimum": 0},
    },
    "required": ["name", "age"],
}

print_schema(input_schema)
```

{% tabs %}
{% tab title="output" %}
{ "type": "object", "properties": { "name": { "type": "string" }, "age": { "type": "integer", "minimum": 0 } }, "required": \[ "name", "age" ] }
{% endtab %}
{% endtabs %}

```python
chain.output_schema.schema()
```

{% tabs %}
{% tab title="output" %}
{'title': 'StrOutputParserOutput', 'type': 'string'}
{% endtab %}
{% endtabs %}
