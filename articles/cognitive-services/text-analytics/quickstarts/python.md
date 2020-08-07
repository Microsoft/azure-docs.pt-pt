---
title: 'Início rápido: Utilizar o Python para chamar a API de Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Este quickstart mostra como obter informações e amostras de código para ajudá-lo a começar rapidamente a usar a API text Analytics em Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 34d1b62ed97b966c000ff81e8f7676c30338b6a1
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876772"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Quickstart: Utilização da API Python REST para chamar o Serviço Cognitivo text Analytics 
<a name="HOLTop"></a>

Utilize este quickstart para começar a analisar a linguagem com a API E Python do Text Analytics REST. Este artigo mostra-lhe como detetar a [linguagem,](#Detect) [analisar sentimentos,](#SentimentAnalysis) [extrair frases-chave,](#KeyPhraseExtraction)e [identificar entidades ligadas.](#Entities)

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://python.org)

* O Python pede biblioteca
    
    Pode instalar a biblioteca com este comando:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Crie uma nova aplicação Python no seu editor favorito ou IDE. Adicione as seguintes importações ao seu arquivo.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Crie variáveis para a chave de base e subscrição Azure do seu recurso.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

As secções seguintes descrevem como chamar cada uma das funcionalidades da API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Detetar idiomas

Anexar `/text/analytics/v3.0/languages` ao ponto final da base text Analytics para formar o URL de deteção de idiomas. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

A carga útil para a API consiste numa lista de `documents` tuples que contêm `id` um atributo e um `text` atributo. O `text` atributo armazena o texto a ser analisado, e o pode ser qualquer `id` valor. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

Utilize a biblioteca pedidos para enviar os documentos para a API. Adicione a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho e envie o pedido com `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Saída

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analisar sentimento

Para detetar o sentimento (que varia entre positivo ou negativo) de um conjunto de documentos, `/text/analytics/v3.0/sentiment` apencha-se ao ponto final base text Analytics para formar o URL de deteção de idiomas. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

Tal como acontece com o exemplo de deteção de idiomas, crie um dicionário com uma `documents` chave que consiste numa lista de documentos. Cada documento é uma cadeia de identificação que consiste no `id`, no `text` a ser analisado e no `language` do texto. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

Utilize a biblioteca pedidos para enviar os documentos para a API. Adicione a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho e envie o pedido com `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Saída

A pontuação de sentimento para um documento é entre 0.0 e 1.0, com uma pontuação mais alta indicando um sentimento mais positivo.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrair Expressões-Chave
 
Para extrair as frases-chave de um conjunto de documentos, `/text/analytics/v3.0/keyPhrases` apencha-se ao ponto final base text Analytics para formar o URL de deteção de idiomas. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

Esta recolha de documentos é a mesma utilizada para o exemplo de análise de sentimento.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

Utilize a biblioteca pedidos para enviar os documentos para a API. Adicione a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho e envie o pedido com `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Saída

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identificar Entidades

Para identificar entidades conhecidas (pessoas, lugares e coisas) em documentos de texto, `/text/analytics/v3.0/entities/recognition/general` anexa-se ao ponto final base text Analytics para formar o URL de deteção de idiomas. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general"
```

Crie uma coleção de documentos, como nos exemplos anteriores. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

Utilize a biblioteca pedidos para enviar os documentos para a API. Adicione a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho e envie o pedido com `requests.post()` .

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>Saída

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Ver também 

 [Descrição geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)
