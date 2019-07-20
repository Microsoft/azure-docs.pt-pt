---
title: 'Início rápido: Chamar o serviço de Análise de Texto usando o SDK do Python'
titleSuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar rapidamente a usar o API de Análise de Texto nos serviços cognitivas do Azure.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: c24979d9aef74b6cc840427a010b9ce70f2c0b8a
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356948"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Início rápido: Chamar o serviço de Análise de Texto usando o SDK do Python 
<a name="HOLTop"></a>

Use este guia de início rápido para começar a analisar a linguagem com o SDK do Análise de Texto para Python. Embora a API REST do Análise de Texto seja compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos sem serializar e desserializar o JSON. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3. x](https://www.python.org/)

* O SDK do Análise de Texto [para Python](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) você pode instalar o pacote com:

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Você também deve ter o [ponto de extremidade e a chave de acesso](../How-tos/text-analytics-how-to-access-key.md) que foram gerados para você durante a inscrição.

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Crie um novo aplicativo Python em seu editor ou IDE favorito. Em seguida, adicione as seguintes instruções de importação ao arquivo.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>Autenticar suas credenciais

> [!Tip]
> Para a implantação segura de segredos em sistemas de produção, é recomendável usar [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

Depois de criar uma variável para sua chave de assinatura do análise de texto `CognitiveServicesCredentials` , crie uma instância de um objeto com ela.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Criar um cliente Análise de Texto

Crie um novo `TextAnalyticsClient` objeto com `credentials` e `text_analytics_url` como um parâmetro. Use a região do Azure correta para sua assinatura do Análise de Texto ( `westcentralus`por exemplo).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Análise de sentimentos

A carga para a API consiste em uma lista de `documents`, que são dicionários contendo `id` um atributo `text` e um. O `text` atributo armazena o texto a ser analisado `id` e pode ser qualquer valor. 

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "I had the best day of my life."
    },
    {
        "id": "2",
        "language": "en",
        "text": "This was a waste of my time. The speaker put me to sleep."
    },
    {
        "id": "3",
        "language": "es",
        "text": "No tengo dinero ni nada que dar..."
    },
    {
        "id": "4",
        "language": "it",
        "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    }
]
```

Chame a `sentiment()` função e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e a pontuação de sentimentos. Uma pontuação mais próxima de 0 indica uma observação negativa, enquanto uma pontuação mais próxima de 1 indica uma observação positiva.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Sentiment Score: ",
          "{:.2f}".format(document.score))
```

### <a name="output"></a>Output

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Deteção de idioma

Crie uma lista de dicionários, cada um contendo o documento que você deseja analisar. O `text` atributo armazena o texto a ser analisado `id` e pode ser qualquer valor. 

```python
documents = [
    {
        'id': '1',
        'text': 'This is a document written in English.'
    },
    {
        'id': '2',
        'text': 'Este es un document escrito en Español.'
    },
    {
        'id': '3',
        'text': '这是一个用中文写的文件'
    }
]
```

Usando o cliente criado anteriormente, chame `detect_language()` e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e o primeiro idioma retornado.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Language: ",
          document.detected_languages[0].name)
```

### <a name="output"></a>Output

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidade

Crie uma lista de dicionários, contendo os documentos que você deseja analisar. O `text` atributo armazena o texto a ser analisado `id` e pode ser qualquer valor. 


```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es",
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

Usando o cliente criado anteriormente, chame `entities()` a função e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e as entidades contidas nele.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ", entity.name, "\tType: ",
              entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Output

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>Extração de expressões chave

Crie uma lista de dicionários, contendo os documentos que você deseja analisar. O `text` atributo armazena o texto a ser analisado `id` e pode ser qualquer valor. 


```python
documents = [
    {
        "id": "1",
        "language": "ja",
        "text": "猫は幸せ"
    },
    {
        "id": "2",
        "language": "de",
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3",
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4",
        "language": "es",
        "text": "A mi me encanta el fútbol!"
    }
]
```

Usando o cliente criado anteriormente, chame `key_phrases()` a função e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e as frases-chave contidas nele.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t", phrase)
```

### <a name="output"></a>Output

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também

* [O que é o API de Análise de Texto?](../overview.md)
* [Cenários de usuário de exemplo](../text-analytics-user-scenarios.md)
* [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)
