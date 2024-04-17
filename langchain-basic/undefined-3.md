# 프롬프트허브

{% hint style="info" %}
LangChain의 프롬프트 허브는 사용자들이 프롬프트를 공유하고 검색할 수 있는 플랫폼입니다. 이 가이드에서는 프롬프트 허브에서 프롬프트를 가져오는 방법과 직접 작성한 프롬프트를 업로드하는 방법에 대해 설명합니다.
{% endhint %}

### 프롬프트 가져오기 (Pull)

#### 1. LLM 설정

* BedrockChat을 사용하여 LLM(Language Model)을 설정합니다.
* `model_id`와 `region_name`을 지정하여 BedrockChat 객체를 생성합니다.

```python
from langchain_community.chat_models import BedrockChat

llm = BedrockChat(
    model_id="anthropic.claude-3-haiku-20240307-v1:0", region_name="us-east-1"
)
```

#### 2. 프롬프트 가져오기

* LangChain 허브에서 프롬프트를 가져옵니다.
* `hub.pull()` 메서드를 사용하여 원하는 프롬프트를 가져옵니다.
* 프롬프트의 소유자, 저장소 이름, API URL을 지정합니다.

```python
from langchain import hub

prompt = hub.pull("rlm/rag-prompt", api_url="https://api.hub.langchain.com")
print(prompt)
```

{% tabs %}
{% tab title="output" %}
input\_variables=\['context', 'question'] metadata={'lc\_hub\_owner': 'rlm', 'lc\_hub\_repo': 'rag-prompt', 'lc\_hub\_commit\_hash': '50442af133e61576e74536c6556cefe1fac147cad032f4377b60c436e6cdcb6e'} messages=\[HumanMessagePromptTemplate(prompt=PromptTemplate(input\_variables=\['context', 'question'], template="You are an assistant for question-answering tasks. Use the following pieces of retrieved context to answer the question. If you don't know the answer, just say that you don't know. Use three sentences maximum and keep the answer concise.\nQuestion: {question} \nContext: {context} \nAnswer:"))]
{% endtab %}
{% endtabs %}

이렇게 가져온 프롬프트를 사용하여 다양한 태스크를 수행할 수 있습니다.

### 프롬프트 업로드 (Push)

#### 1. 프롬프트 템플릿 생성

* ChatPromptTemplate을 사용하여 프롬프트 템플릿을 생성합니다.
* 템플릿에는 지침(Instructions)과 입력(Inputs)이 포함됩니다.
* 입력된 내용을 바탕으로 요약을 생성하도록 프롬프트를 구성합니다.

```python
from langchain.prompts import ChatPromptTemplate

prompt = ChatPromptTemplate.from_template(
    "<Instructions>주어진 내용을 바탕으로 다음 문장을 요약하세요. 답변은 반드시 한글로 작성하세요</Instructions>\n\n<Inputs>{input}</Inputs>\n\n요약 :"
)
prompt
```

{% tabs %}
{% tab title="output" %}
ChatPromptTemplate(input\_variables=\['context'], messages=\[HumanMessagePromptTemplate(prompt=PromptTemplate(input\_variables=\['context'], template='주어진 내용을 바탕으로 다음 문장을 요약하세요. 답변은 반드시 한글로 작성하세요\n{context}\n\n요약 :'))])
{% endtab %}
{% endtabs %}

#### 2. 프롬프트와 LLM 연결

* 생성한 프롬프트와 설정한 LLM을 연결합니다.
* 연결된 체인을 사용하여 입력 내용을 요약합니다.

```python
context = """
2023년 당근마켓(이하 당근)의 연간 흑자전환 비결을 묻는 질문에 황도연 대표는 이렇게 답했다. 1시간 남짓한 시간 동안 진행된 인터뷰에서 황 대표는 '하이퍼로컬'이라는 단어를 11번 언급했다.

지난해 초까지만 해도 당근을 바라보는 외부의 시선은 부정적이었다. 2021년 1800억원의 투자유치를 통해 3조원의 기업가치를 인정 받은 이후 2021년 352억원, 2022년 464억원으로 적자폭이 나날이 늘었기 때문이다. 일각에서는 '플랫폼의 저주'라는 얘기까지 돌았다.

우려 섞인 외부 평가와 달리 내부는 활기가 넘쳤다. 2023년 1분기부터 흑자를 기록했다. 연초부터 이어진 흑자는 연말까지 이어져 연간 173억원의 영업흑자(별도기준)를 기록했다. 같은 기간 매출액은 499억원에서 1276억원으로 2배 이상 성장했다. 양적·질적 성장을 모두 이뤘다.

황 대표 취임 1년만의 일이다. 2021년 3월 당근 사업부문 총괄 부사장으로 합류한 황 대표는 이듬해 11월 대표로 선임됐다. 지난 1년 동안 당근에는 어떤 변화가 있었을까. 서울 서초구 서초동 당근 본사에서 황 대표를 직접 만나 들어봤다.
"""

chain = prompt | llm
response = chain.invoke({"input": context})
print(response.content)
```

{% tabs %}
{% tab title="output" %}
당근마켓의 황도연 대표는 '하이퍼로컬'이라는 전략을 통해 2023년 연간 173억원의 영업흑자를 달성했다. 당근마켓은 2021년부터 적자 규모가 늘어났지만, 황 대표의 취임 이후 1년 만에 매출이 두 배 이상 성장하며 양적·질적 성장을 이뤘다.
{% endtab %}
{% endtabs %}

#### 3. 환경 변수 설정

* LangChain 허브에 프롬프트를 업로드하기 위해서는 API 키가 필요합니다.
* 환경 변수로 API 키를 설정합니다.

```python
%env LANGCHAIN_HUB_API_KEY="your_api_key"
```

{% tabs %}
{% tab title="output" %}
env: LANGCHAIN\_HUB\_API\_KEY="ls\_\_......"
{% endtab %}
{% endtabs %}

#### 4. 프롬프트 허브에 프롬프트 업로드

* LangChain 허브에 프롬프트를 업로드합니다.
* `hub.push()` 메서드를 사용하여 프롬프트를 업로드합니다.
* 프롬프트의 소유자와 제목을 지정합니다.

```python
from langchain import hub

PROMPT_OWNER = "arciel"
prompt_title = "korean-summary-claude"
hub.push(f"{PROMPT_OWNER}/{prompt_title}", prompt)
```

{% tabs %}
{% tab title="output" %}
'https://smith.langchain.com/hub/arciel/korean-summary-claude/e36a8952'
{% endtab %}
{% endtabs %}

이렇게 작성한 프롬프트를 LangChain 허브에 업로드하여 다른 사용자들과 공유할 수 있습니다.

위의 두 가지 방법을 통해 LangChain 프롬프트 허브를 활용하여 프롬프트를 가져오고 업로드할 수 있습니다. 프롬프트를 업로드할 때는 API 키를 환경 변수로 설정해야 합니다. 이를 통해 다양한 태스크에 적합한 프롬프트를 쉽게 찾고 공유할 수 있습니다.
