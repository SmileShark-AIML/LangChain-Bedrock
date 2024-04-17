---
description: Foundation Model 테스트를 기술합니다.
---

# Foundation Model 테스트

{% hint style="info" %}
해당 작업은 aws cli 설정이 완료되어 profile이 존재하는 환경에서 진행해야합니다.
{% endhint %}

## 사용 가능한 Foundation Model 리스트 불러오기

Foundation Model의 액세스가 완료되면, boto3 라이브러리를 활용하여 사용가능한 파운데이션 모델 리스트를 불러올 수 있습니다.

```python
import boto3
bedrock_client = boto3.client(service_name="bedrock", region_name="us-east-1")
fm_list = bedrock_client.list_foundation_models()
fm_list = fm_list['modelSummaries']
```

```python
for fm in fm_list:
    print(fm)
```

{% tabs %}
{% tab title="output" %}
{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-tg1-large', 'modelId': 'amazon.titan-tg1-large', 'modelName': 'Titan Text Large', 'providerName': 'Amazon', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-image-generator-v1:0', 'modelId': 'amazon.titan-image-generator-v1:0', 'modelName': 'Titan Image Generator G1', 'providerName': 'Amazon', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['IMAGE'], 'customizationsSupported': \['FINE\_TUNING'], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-image-generator-v1', 'modelId': 'amazon.titan-image-generator-v1', 'modelName': 'Titan Image Generator G1', 'providerName': 'Amazon', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['IMAGE'], 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-embed-g1-text-02', 'modelId': 'amazon.titan-embed-g1-text-02', 'modelName': 'Titan Text Embeddings v2', 'providerName': 'Amazon', 'inputModalities': \['TEXT'], 'outputModalities': \['EMBEDDING'], 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-text-lite-v1:0:4k', 'modelId': 'amazon.titan-text-lite-v1:0:4k', 'modelName': 'Titan Text G1 - Lite', 'providerName': 'Amazon', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \['FINE\_TUNING', 'CONTINUED\_PRE\_TRAINING'], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-text-lite-v1', 'modelId': 'amazon.titan-text-lite-v1', 'modelName': 'Titan Text G1 - Lite', 'providerName': 'Amazon', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-text-express-v1:0:8k', 'modelId': 'amazon.titan-text-express-v1:0:8k', 'modelName': 'Titan Text G1 - Express', 'providerName': 'Amazon', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \['FINE\_TUNING', 'CONTINUED\_PRE\_TRAINING'], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-text-express-v1', 'modelId': 'amazon.titan-text-express-v1', 'modelName': 'Titan Text G1 - Express', 'providerName': 'Amazon', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-embed-text-v1:2:8k', 'modelId': 'amazon.titan-embed-text-v1:2:8k', 'modelName': 'Titan Embeddings G1 - Text', 'providerName': 'Amazon', 'inputModalities': \['TEXT'], 'outputModalities': \['EMBEDDING'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-embed-text-v1', 'modelId': 'amazon.titan-embed-text-v1', 'modelName': 'Titan Embeddings G1 - Text', 'providerName': 'Amazon', 'inputModalities': \['TEXT'], 'outputModalities': \['EMBEDDING'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-embed-image-v1:0', 'modelId': 'amazon.titan-embed-image-v1:0', 'modelName': 'Titan Multimodal Embeddings G1', 'providerName': 'Amazon', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['EMBEDDING'], 'customizationsSupported': \['FINE\_TUNING'], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-embed-image-v1', 'modelId': 'amazon.titan-embed-image-v1', 'modelName': 'Titan Multimodal Embeddings G1', 'providerName': 'Amazon', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['EMBEDDING'], 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/stability.stable-diffusion-xl', 'modelId': 'stability.stable-diffusion-xl', 'modelName': 'SDXL 0.8', 'providerName': 'Stability AI', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['IMAGE'], 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'LEGACY'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/stability.stable-diffusion-xl-v0', 'modelId': 'stability.stable-diffusion-xl-v0', 'modelName': 'SDXL 0.8', 'providerName': 'Stability AI', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['IMAGE'], 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'LEGACY'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/stability.stable-diffusion-xl-v1:0', 'modelId': 'stability.stable-diffusion-xl-v1:0', 'modelName': 'SDXL 1.0', 'providerName': 'Stability AI', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['IMAGE'], 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/stability.stable-diffusion-xl-v1', 'modelId': 'stability.stable-diffusion-xl-v1', 'modelName': 'SDXL 1.0', 'providerName': 'Stability AI', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['IMAGE'], 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/ai21.j2-grande-instruct', 'modelId': 'ai21.j2-grande-instruct', 'modelName': 'J2 Grande Instruct', 'providerName': 'AI21 Labs', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/ai21.j2-jumbo-instruct', 'modelId': 'ai21.j2-jumbo-instruct', 'modelName': 'J2 Jumbo Instruct', 'providerName': 'AI21 Labs', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/ai21.j2-mid', 'modelId': 'ai21.j2-mid', 'modelName': 'Jurassic-2 Mid', 'providerName': 'AI21 Labs', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/ai21.j2-mid-v1', 'modelId': 'ai21.j2-mid-v1', 'modelName': 'Jurassic-2 Mid', 'providerName': 'AI21 Labs', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/ai21.j2-ultra', 'modelId': 'ai21.j2-ultra', 'modelName': 'Jurassic-2 Ultra', 'providerName': 'AI21 Labs', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/ai21.j2-ultra-v1', 'modelId': 'ai21.j2-ultra-v1', 'modelName': 'Jurassic-2 Ultra', 'providerName': 'AI21 Labs', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-instant-v1:2:100k', 'modelId': 'anthropic.claude-instant-v1:2:100k', 'modelName': 'Claude Instant', 'providerName': 'Anthropic', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-instant-v1', 'modelId': 'anthropic.claude-instant-v1', 'modelName': 'Claude Instant', 'providerName': 'Anthropic', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-v2:0:18k', 'modelId': 'anthropic.claude-v2:0:18k', 'modelName': 'Claude', 'providerName': 'Anthropic', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-v2:0:100k', 'modelId': 'anthropic.claude-v2:0:100k', 'modelName': 'Claude', 'providerName': 'Anthropic', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-v2:1:18k', 'modelId': 'anthropic.claude-v2:1:18k', 'modelName': 'Claude', 'providerName': 'Anthropic', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-v2:1:200k', 'modelId': 'anthropic.claude-v2:1:200k', 'modelName': 'Claude', 'providerName': 'Anthropic', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-v2:1', 'modelId': 'anthropic.claude-v2:1', 'modelName': 'Claude', 'providerName': 'Anthropic', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-v2', 'modelId': 'anthropic.claude-v2', 'modelName': 'Claude', 'providerName': 'Anthropic', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-sonnet-20240229-v1:0:28k', 'modelId': 'anthropic.claude-3-sonnet-20240229-v1:0:28k', 'modelName': 'Claude 3 Sonnet', 'providerName': 'Anthropic', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-sonnet-20240229-v1:0:200k', 'modelId': 'anthropic.claude-3-sonnet-20240229-v1:0:200k', 'modelName': 'Claude 3 Sonnet', 'providerName': 'Anthropic', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-sonnet-20240229-v1:0', 'modelId': 'anthropic.claude-3-sonnet-20240229-v1:0', 'modelName': 'Claude 3 Sonnet', 'providerName': 'Anthropic', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-haiku-20240307-v1:0:48k', 'modelId': 'anthropic.claude-3-haiku-20240307-v1:0:48k', 'modelName': 'Claude 3 Haiku', 'providerName': 'Anthropic', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-haiku-20240307-v1:0:200k', 'modelId': 'anthropic.claude-3-haiku-20240307-v1:0:200k', 'modelName': 'Claude 3 Haiku', 'providerName': 'Anthropic', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-haiku-20240307-v1:0', 'modelId': 'anthropic.claude-3-haiku-20240307-v1:0', 'modelName': 'Claude 3 Haiku', 'providerName': 'Anthropic', 'inputModalities': \['TEXT', 'IMAGE'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/cohere.command-text-v14:7:4k', 'modelId': 'cohere.command-text-v14:7:4k', 'modelName': 'Command', 'providerName': 'Cohere', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \['FINE\_TUNING'], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/cohere.command-text-v14', 'modelId': 'cohere.command-text-v14', 'modelName': 'Command', 'providerName': 'Cohere', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/cohere.command-light-text-v14:7:4k', 'modelId': 'cohere.command-light-text-v14:7:4k', 'modelName': 'Command Light', 'providerName': 'Cohere', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \['FINE\_TUNING'], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/cohere.command-light-text-v14', 'modelId': 'cohere.command-light-text-v14', 'modelName': 'Command Light', 'providerName': 'Cohere', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/cohere.embed-english-v3:0:512', 'modelId': 'cohere.embed-english-v3:0:512', 'modelName': 'Embed English', 'providerName': 'Cohere', 'inputModalities': \['TEXT'], 'outputModalities': \['EMBEDDING'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/cohere.embed-english-v3', 'modelId': 'cohere.embed-english-v3', 'modelName': 'Embed English', 'providerName': 'Cohere', 'inputModalities': \['TEXT'], 'outputModalities': \['EMBEDDING'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/cohere.embed-multilingual-v3:0:512', 'modelId': 'cohere.embed-multilingual-v3:0:512', 'modelName': 'Embed Multilingual', 'providerName': 'Cohere', 'inputModalities': \['TEXT'], 'outputModalities': \['EMBEDDING'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/cohere.embed-multilingual-v3', 'modelId': 'cohere.embed-multilingual-v3', 'modelName': 'Embed Multilingual', 'providerName': 'Cohere', 'inputModalities': \['TEXT'], 'outputModalities': \['EMBEDDING'], 'responseStreamingSupported': False, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/meta.llama2-13b-chat-v1:0:4k', 'modelId': 'meta.llama2-13b-chat-v1:0:4k', 'modelName': 'Llama 2 Chat 13B', 'providerName': 'Meta', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['PROVISIONED'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/meta.llama2-13b-chat-v1', 'modelId': 'meta.llama2-13b-chat-v1', 'modelName': 'Llama 2 Chat 13B', 'providerName': 'Meta', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/meta.llama2-70b-chat-v1:0:4k', 'modelId': 'meta.llama2-70b-chat-v1:0:4k', 'modelName': 'Llama 2 Chat 70B', 'providerName': 'Meta', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \[], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/meta.llama2-70b-chat-v1', 'modelId': 'meta.llama2-70b-chat-v1', 'modelName': 'Llama 2 Chat 70B', 'providerName': 'Meta', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/meta.llama2-13b-v1:0:4k', 'modelId': 'meta.llama2-13b-v1:0:4k', 'modelName': 'Llama 2 13B', 'providerName': 'Meta', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \['FINE\_TUNING'], 'inferenceTypesSupported': \[], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/meta.llama2-13b-v1', 'modelId': 'meta.llama2-13b-v1', 'modelName': 'Llama 2 13B', 'providerName': 'Meta', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \[], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/meta.llama2-70b-v1:0:4k', 'modelId': 'meta.llama2-70b-v1:0:4k', 'modelName': 'Llama 2 70B', 'providerName': 'Meta', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \['FINE\_TUNING'], 'inferenceTypesSupported': \[], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/meta.llama2-70b-v1', 'modelId': 'meta.llama2-70b-v1', 'modelName': 'Llama 2 70B', 'providerName': 'Meta', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \[], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/mistral.mistral-7b-instruct-v0:2', 'modelId': 'mistral.mistral-7b-instruct-v0:2', 'modelName': 'Mistral 7B Instruct', 'providerName': 'Mistral AI', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/mistral.mixtral-8x7b-instruct-v0:1', 'modelId': 'mistral.mixtral-8x7b-instruct-v0:1', 'modelName': 'Mixtral 8x7B Instruct', 'providerName': 'Mistral AI', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}

{'modelArn': 'arn:aws:bedrock:us-east-1::foundation-model/mistral.mistral-large-2402-v1:0', 'modelId': 'mistral.mistral-large-2402-v1:0', 'modelName': 'Mistral Large', 'providerName': 'Mistral AI', 'inputModalities': \['TEXT'], 'outputModalities': \['TEXT'], 'responseStreamingSupported': True, 'customizationsSupported': \[], 'inferenceTypesSupported': \['ON\_DEMAND'], 'modelLifecycle': {'status': 'ACTIVE'\}}
{% endtab %}
{% endtabs %}

## Claude v3 Haiku 모델 요청 테스트

사용가능한 FM중 Claude v3 Haiku 모델에 인퍼런스 테스트를 진행해보겠습니다.

```python
import json

bedrock_client = boto3.client( service_name="bedrock-runtime", region_name="us-east-1")
model_id = "anthropic.claude-3-haiku-20240307-v1:0"
prompt = "대한민국의 수도는?"

response = bedrock_client.invoke_model(
                modelId=model_id,
                body=json.dumps(
                    {
                        "anthropic_version": "bedrock-2023-05-31",
                        "max_tokens": 1024,
                        "messages": [
                            {
                                "role": "user",
                                "content": [{"type": "text", "text": prompt}],
                            }
                        ],
                    }
                ),
            )

result = json.loads(response.get("body").read())
```

```python
result
```

{% tabs %}
{% tab title="outputd" %}
{'id': 'msg\_015LNTFiwoRWkLq1a2SCzxFc', 'type': 'message', 'role': 'assistant', 'content': \[{'type': 'text', 'text': '대한민국의 수도는 서울입니다.\n\n서울은 대한민국의 행정, 경제, 문화의 중심지로 자리잡고 있습니다. 서울은 1392년 조선 왕조의 수도로 지정되었으며, 대한제국 시대와 대한민국 건국 이후에도 지속적으로 수도 기능을 수행해 왔습니다.\n\n주요 행정 기관들과 국회, 대법원 등 주요 국가기관들이 서울에 위치해 있으며, 대기업 본사와 금융기관, 미디어 등 대한민국의 주요 산업이 집중되어 있는 것도 서울의 수도로서의 위상을 보여줍니다.'}], 'model': 'claude-3-haiku-48k-20240307', 'stop\_reason': 'end\_turn', 'stop\_sequence': None, 'usage': {'input\_tokens': 18, 'output\_tokens': 243\}}
{% endtab %}
{% endtabs %}
