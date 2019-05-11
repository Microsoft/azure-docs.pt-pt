---
title: 'Início rápido: Com o Python para chamar a API de análise de texto'
titleSuffix: Azure Cognitive Services
description: Exemplos de código e informações de GET para ajudá-lo a rapidamente começar a utilizar a API de análise de texto nos serviços cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/09/2019
ms.author: aahi
ms.openlocfilehash: fb411b7e36d8658c5f46294a3b7025c3e93928e7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540119"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Início rápido: Com a API de REST do Python para chamar o serviço cognitivos de análise de texto 
<a name="HOLTop"></a>

Utilize este guia de introdução para começar a analisar a linguagem com a API de REST de análise de texto e Python. Este artigo mostra-lhe como ao [detetar o idioma](#Detect), [analisar sentimentos](#SentimentAnalysis), [extrair expressões-chave](#KeyPhraseExtraction), e [identificar entidades associadas](#Entities).

Veja as [definições de API](//go.microsoft.com/fwlink/?LinkID=759346) para ter acesso à documentação técnica sobre APIs.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://python.org)

* O [chave de acesso e de ponto final](../How-tos/text-analytics-how-to-access-key.md) que foi gerado para durante a inscrição.

* Biblioteca de pedidos de Python
    
    Pode instalar a biblioteca com este comando:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Crie uma aplicação Python nova no seu editor favorito ou IDE. Adicione as seguintes importações ao seu ficheiro.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
from IPython.display import HTML
```

Crie variáveis para a sua chave de subscrição e o ponto final para a API de REST de análise de texto. Certifique-se de que a região em que o ponto de extremidade corresponde ao utilizado quando se inscreveu no (por exemplo `westcentralus`). Se estiver a utilizar uma chave de avaliação gratuita, não precisa alterar nada.
    
```python
subscription_key = "<ADD YOUR KEY HERE>"
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
```

As secções seguintes descrevem como chamar cada uma das funcionalidades da API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Detetar idiomas

Acrescentar `languages` para o ponto final base de análise de texto para formar o URL de deteção de idioma. Por exemplo: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
    
```python
language_api_url = text_analytics_base_url + "languages"
```

O payload para a API consiste numa lista de `documents`, que são as tuplas que contém um `id` e um `text` atributo. O `text` o texto a ser analisados, os arquivos de atributo e o `id` pode ser qualquer valor. 

```python
documents = { "documents": [
    { "id": "1", "text": "This is a document written in English." },
    { "id": "2", "text": "Este es un document escrito en Español." },
    { "id": "3", "text": "这是一个用中文写的文件" }
]}
```

Utilize a biblioteca de pedidos para enviar documentos para a API. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho e enviar o pedido com `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Resultado

```json
{
"documents":[
    {
        "detectedLanguages":[
        {
            "iso6391Name":"en",
            "name":"English",
            "score":1.0
        }
        ],
        "id":"1"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"es",
            "name":"Spanish",
            "score":1.0
        }
        ],
        "id":"2"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"zh_chs",
            "name":"Chinese_Simplified",
            "score":1.0
        }
        ],
        "id":"3"
    }
],
"errors":[]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analisar sentimento

Para detetar o sentimento (que varia entre positivo ou negativo) de um conjunto de documentos, acrescente `sentiment` para o ponto final base de análise de texto para formar o URL de deteção de idioma. Por exemplo: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
    
```python
sentiment_url = text_analytics_base_url + "sentiment"
```

Como com o exemplo de deteção de idioma, criar um dicionário com uma `documents` chave que consiste numa lista de documentos. Cada documento é uma cadeia de identificação que consiste no `id`, no `text` a ser analisado e no `language` do texto. 

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Utilize a biblioteca de pedidos para enviar documentos para a API. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho e enviar o pedido com `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Resultado

A classificação de sentimento de um documento é entre 0,0 e 1,0, com uma pontuação superior que indicam um sentimento positivo mais.

```json
{
  "documents":[
    {
      "id":"1",
      "score":0.9708490371704102
    },
    {
      "id":"2",
      "score":0.0019068121910095215
    },
    {
      "id":"3",
      "score":0.7456425428390503
    },
    {
      "id":"4",
      "score":0.334433376789093
    }
  ],
  "errors":[

  ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrair frases-chave
 
Para extrair expressões-chave a partir de um conjunto de documentos, acrescente `keyPhrases` para o ponto final base de análise de texto para formar o URL de deteção de idioma. Por exemplo: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
keyphrase_url = text_analytics_base_url + "keyPhrases"
```

Esta coleção de documentos é o mesmo usado no exemplo de análise de sentimentos.

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

Utilize a biblioteca de pedidos para enviar documentos para a API. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho e enviar o pedido com `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Resultado

```json
{
  "documents":[
    {
      "keyPhrases":[
        "wonderful experience",
        "staff",
        "rooms"
      ],
      "id":"1"
    },
    {
      "keyPhrases":[
        "food",
        "terrible time",
        "hotel",
        "staff"
      ],
      "id":"2"
    },
    {
      "keyPhrases":[
        "Monte Rainier",
        "caminos"
      ],
      "id":"3"
    },
    {
      "keyPhrases":[
        "carretera",
        "tráfico",
        "día"
      ],
      "id":"4"
    }
  ],
  "errors":[

  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identificar as entidades

Para identificar as entidades conhecidas (pessoas, lugares e coisas) em documentos de texto, acrescente `keyPhrases` para o ponto final base de análise de texto para formar o URL de deteção de idioma. Por exemplo: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
entities_url = text_analytics_base_url + "keyPhrases"
```

Crie uma coleção de documentos, como nos exemplos anteriores. 

```python
documents = {"documents" : [
  {"id": "1", "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."}
]}
```

Utilize a biblioteca de pedidos para enviar documentos para a API. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho e enviar o pedido com `requests.post()`.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
```

### <a name="output"></a>Resultado

```json
{
  "documents":[
    {
      "id":"1",
      "keyPhrases":[
        "Bill Gates",
        "Paul Allen",
        "BASIC interpreters",
        "Altair",
        "Microsoft"
      ]
    }
  ],
  "errors":[]
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também 

 [Descrição Geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)
