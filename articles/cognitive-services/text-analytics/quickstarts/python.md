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
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: 6edcb4501feb0ac2911fed075ed4866aa267a80e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893083"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>Início rápido: Com o Python para chamar o serviço cognitivos de análise de texto 
<a name="HOLTop"></a>

Este tutorial explica como [detetar idiomas](#Detect), [analisar sentimentos](#SentimentAnalysis) e [extrair expressões-chave](#KeyPhraseExtraction) através das [API de Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759711) com o Python.

Pode executar este exemplo da linha de comando ou como um bloco de notas do Jupyter no [MyBinder](https://mybinder.org) clicando no lançamento associador de destaque:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

### <a name="command-line"></a>Linha de comandos

Poderá ter de atualizar [IPython](https://ipython.org/install.html), o kernel para o Jupyter:
```bash
pip install --upgrade IPython
```

Poderá ter de atualizar o [pedidos](http://docs.python-requests.org/en/master/) biblioteca:
```bash
pip install requests
```

Veja as [definições de API](//go.microsoft.com/fwlink/?LinkID=759346) para ter acesso à documentação técnica sobre APIs.

## <a name="prerequisites"></a>Pré-requisitos

* [!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

* O [chave de acesso e de ponto final](../How-tos/text-analytics-how-to-access-key.md) que foi gerado para durante a inscrição.

* As seguintes importações, a chave de subscrição, e `text_analytics_base_url` são utilizados para todos os inícios rápidos de abaixo. Adicione as importações.

    ```python
    import requests
    # pprint is pretty print (formats the JSON)
    from pprint import pprint
    from IPython.display import HTML
    ```
    
    Adicione estas linhas, em seguida, substitua `subscription_key` com uma chave de subscrição válido que obteve anteriormente.
    
    ```python
    subscription_key = '<ADD KEY HERE>'
    assert subscription_key
    ```
    
    Em seguida, adicione esta linha, em seguida, certifique-se de que a região em `text_analytics_base_url` corresponde ao utilizado ao configurar o serviço. Se estiver a utilizar uma chave de avaliação gratuita, não precisa alterar nada.
    
    ```python
    text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/"
    ```

<a name="Detect"></a>

## <a name="detect-languages"></a>Detetar idiomas

A API Deteção de Idioma deteta o idioma de um documento de texto através do [método Detetar Idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7). O ponto final do serviço da API de deteção de idioma para a sua região está disponível através do URL seguinte:

```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

```url
https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/languages
```

O payload para a API consiste numa lista de `documents`, cada um dos quais, por sua vez, contém um `id` e um atributo `text`. O atributo `text` armazena o texto a ser analisado. 

Substitua o `documents` dicionário por qualquer outro texto para deteção de idioma.

```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

As linhas de código seguintes chamam a API de deteção de idioma através da biblioteca `requests` do Python para determinar o idioma dos documentos.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

As linhas de código seguintes compõem os dados JSON como uma tabela HTML.

```python
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

Resposta JSON com êxito:

```json
    {'documents': [{'detectedLanguages': [{'iso6391Name': 'en',
                                           'name': 'English',
                                           'score': 1.0}],
                    'id': '1'},
                   {'detectedLanguages': [{'iso6391Name': 'es',
                                           'name': 'Spanish',
                                           'score': 1.0}],
                    'id': '2'},
                   {'detectedLanguages': [{'iso6391Name': 'zh_chs',
                                           'name': 'Chinese_Simplified',
                                           'score': 1.0}],
                    'id': '3'}],
     'errors': []}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analisar sentimento

A API de análise de sentimentos Deteta o sentimento (intervalo entre positivo ou negativo) de um conjunto de registos de texto, a utilizar o [método de sentimento](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). O seguinte exemplo classifica dois documentos, um em inglês e outro em espanhol.

O ponto final de serviço para análise de sentimentos está disponível para a sua região através do URL seguinte:

```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```
    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment

Como com o exemplo de deteção de idioma, o serviço é fornecido com um dicionário com uma chave `documents` que consiste numa lista de documentos. Cada documento é uma cadeia de identificação que consiste no `id`, no `text` a ser analisado e no `language` do texto. Pode utilizar a API de deteção de idioma da seção anterior para preencher este campo.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

A API de sentimento pode agora ser utilizada para analisar os sentimentos nos documentos.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

Resposta JSON com êxito:

```json
{'documents': [{'id': '1', 'score': 0.7673527002334595},
                {'id': '2', 'score': 0.18574094772338867},
                {'id': '3', 'score': 0.5}],
    'errors': []}
```

A classificação de sentimento de um documento é entre 0,0 e 1,0, com uma pontuação superior que indicam um sentimento positivo mais.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrair expressões-chave

A API de Extração de Expressões-Chave extrai expressões-chave de um documento de texto através do [método Expressões-Chave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Esta secção do tutorial extrai expressões-chave dos documentos em inglês e em espanhol.

É possível aceder ao ponto final de serviço do serviço de extração de frases-chave através do URL seguinte:

```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```
    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases

A coleção de documentos é a mesma que foi utilizada para a análise de sentimentos.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

O objeto JSON pode ser composto como uma tabela HTML usando as seguintes linhas de código:

```python
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

As linhas de código seguintes chamam a API de deteção de idioma através da biblioteca `requests` do Python para determinar o idioma dos documentos.
```python
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

Resposta JSON com êxito:
```json
{'documents': [
    {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
    {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
    {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
    {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
    'errors': []
}
```

## <a name="identify-entities"></a>Identificar as entidades

A API de Entidades identifica entidades conhecidas num documento de texto através do [método Entidades](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634). O seguinte exemplo identifica as entidades dos documentos em inglês.

É possível aceder ao ponto final de serviço do serviço de associação de entidades através do URL seguinte:

```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities

A coleção de documentos é apresentada abaixo:

```python
documents = {'documents' : [
  {'id': '1', 'text': 'Jeff bought three dozen eggs because there was a 50% discount.'},
  {'id': '2', 'text': 'The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.'}
]}
```
Agora, os documentos podem ser enviados para a API de Análise de Texto para receber a resposta.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```

Resposta JSON com êxito:
```json
{
    "Documents": [
        {
            "Id": "1",
            "Entities": [
                {
                    "Name": "Jeff",
                    "Matches": [
                        {
                            "Text": "Jeff",
                            "Offset": 0,
                            "Length": 4
                        }
                    ],
                    "Type": "Person"
                },
                {
                    "Name": "three dozen",
                    "Matches": [
                        {
                            "Text": "three dozen",
                            "Offset": 12,
                            "Length": 11
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50",
                    "Matches": [
                        {
                            "Text": "50",
                            "Offset": 49,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50%",
                    "Matches": [
                        {
                            "Text": "50%",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        },
        {
            "Id": "2",
            "Entities": [
                {
                    "Name": "Great Depression",
                    "Matches": [
                        {
                            "Text": "The Great Depression",
                            "Offset": 0,
                            "Length": 20
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Great Depression",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                    "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                },
                {
                    "Name": "1929",
                    "Matches": [
                        {
                            "Text": "1929",
                            "Offset": 30,
                            "Length": 4
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "By 1933",
                    "Matches": [
                        {
                            "Text": "By 1933",
                            "Offset": 36,
                            "Length": 7
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "Gross domestic product",
                    "Matches": [
                        {
                            "Text": "GDP",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Gross domestic product",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                    "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                },
                {
                    "Name": "United States",
                    "Matches": [
                        {
                            "Text": "America",
                            "Offset": 56,
                            "Length": 7
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "United States",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                    "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                    "Type": "Location"
                },
                {
                    "Name": "25",
                    "Matches": [
                        {
                            "Text": "25",
                            "Offset": 72,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "25%",
                    "Matches": [
                        {
                            "Text": "25%",
                            "Offset": 72,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        }
    ],
    "Errors": []
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também 

 [Descrição Geral da Análise de Texto](../overview.md)  
 [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)
