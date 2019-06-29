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
ms.date: 06/28/2019
ms.author: aahi
ms.openlocfilehash: 835dc8d25ad1d6a30020408636b556c3f247200d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478365"
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

### <a name="output"></a>Output

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

### <a name="output"></a>Output

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

## <a name="extract-key-phrases"></a>Extrair expressões-chave
 
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

### <a name="output"></a>Output

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

Para identificar as entidades conhecidas (pessoas, lugares e coisas) em documentos de texto, acrescente `entities` para o ponto final base de análise de texto para formar o URL de deteção de idioma. Por exemplo: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`
    
```python
entities_url = text_analytics_base_url + "entities"
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

### <a name="output"></a>Output

```json
{'documents': [{'id': '1',
   'entities': [{'name': 'Microsoft',
     'matches': [{'wikipediaScore': 0.502357972145024,
       'entityTypeScore': 1.0,
       'text': 'Microsoft',
       'offset': 0,
       'length': 9}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Microsoft',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Microsoft',
     'bingId': 'a093e9b9-90f5-a3d5-c4b8-5855e1b01f85',
     'type': 'Organization'},
    {'name': 'Bill Gates',
     'matches': [{'wikipediaScore': 0.5849375085784292,
       'entityTypeScore': 0.999847412109375,
       'text': 'Bill Gates',
       'offset': 25,
       'length': 10}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Bill Gates',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Bill_Gates',
     'bingId': '0d47c987-0042-5576-15e8-97af601614fa',
     'type': 'Person'},
    {'name': 'Paul Allen',
     'matches': [{'wikipediaScore': 0.5314163053043621,
       'entityTypeScore': 0.9988409876823425,
       'text': 'Paul Allen',
       'offset': 40,
       'length': 10}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Paul Allen',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Paul_Allen',
     'bingId': 'df2c4376-9923-6a54-893f-2ee5a5badbc7',
     'type': 'Person'},
    {'name': 'April 4',
     'matches': [{'wikipediaScore': 0.37312706493069636,
       'entityTypeScore': 0.8,
       'text': 'April 4',
       'offset': 54,
       'length': 7}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'April 4',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/April_4',
     'bingId': '52535f87-235e-b513-54fe-c03e4233ac6e',
     'type': 'Other'},
    {'name': 'April 4, 1975',
     'matches': [{'entityTypeScore': 0.8,
       'text': 'April 4, 1975',
       'offset': 54,
       'length': 13}],
     'type': 'DateTime',
     'subType': 'Date'},
    {'name': 'BASIC',
     'matches': [{'wikipediaScore': 0.35916049097766867,
       'entityTypeScore': 0.8,
       'text': 'BASIC',
       'offset': 89,
       'length': 5}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'BASIC',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/BASIC',
     'bingId': '5b16443d-501c-58f3-352e-611bbe75aa6e',
     'type': 'Other'},
    {'name': 'Altair 8800',
     'matches': [{'wikipediaScore': 0.8697256853652899,
       'entityTypeScore': 0.8,
       'text': 'Altair 8800',
       'offset': 116,
       'length': 11}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Altair 8800',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Altair_8800',
     'bingId': '7216c654-3779-68a2-c7b7-12ff3dad5606',
     'type': 'Other'}]}],
 'errors': []}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também 

 [Descrição Geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)
