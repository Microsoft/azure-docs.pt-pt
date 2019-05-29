---
title: 'Início rápido: Chamar o serviço de análise de texto com o SDK de Python'
titleSuffix: Azure Cognitive Services
description: Exemplos de código e informações de GET para ajudá-lo a rapidamente começar a utilizar a API de análise de texto nos serviços cognitivos do Azure.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: b319abf22f9aa4cdd9a5fef91be0628672d47bd4
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297789"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Início rápido: Chamar o serviço de análise de texto com o SDK de Python 
<a name="HOLTop"></a>

Utilize este guia de introdução para começar a analisar o idioma com o SDK de análise de texto para Python. Embora a API de REST de análise de texto é compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço nas suas aplicações sem serializando e desserializando o JSON. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)

* A análise de texto [SDK para python](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) pode instalar o pacote com:

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Também tem de ter o [chave de acesso e de ponto final](../How-tos/text-analytics-how-to-access-key.md) que foram gerados para durante a inscrição.

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Crie uma aplicação Python nova no seu editor favorito ou IDE. Em seguida, adicione as seguintes declarações de importação ao ficheiro.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>Autenticar as suas credenciais

> [!Tip]
> Para Implantações seguras de segredos em sistemas de produção, recomendamos utilizar [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

Depois de fazer uma variável para a sua chave de subscrição de análise de texto, instanciar um `CognitiveServicesCredentials` objeto com o mesmo.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Criar um cliente de análise de texto

Criar uma nova `TextAnalyticsClient` objeto com `credentials` e `text_analytics_url` como um parâmetro. Utilize a região do Azure correta para a sua subscrição de análise de texto (por exemplo `westcentralus`).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Análise de sentimentos

O payload para a API consiste numa lista de `documents`, que são dicionários que contém um `id` e um `text` atributo. O `text` o texto a ser analisados, os arquivos de atributo e o `id` pode ser qualquer valor. 

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

Chamar o `sentiment()` de função e obter o resultado. Em seguida, iterar os resultados e imprimir o ID de cada documento e classificação de sentimento. Uma pontuação mais próximo de 0 indica um sentimento negativo, enquanto uma pontuação mais perto de 1 indica um sentimento positivo.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
     print("Document Id: ", document.id, ", Sentiment Score: ", "{:.2f}".format(document.score))
```

### <a name="output"></a>Resultado

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Deteção de idioma

Criar uma lista de dicionários, cada um deles contendo o documento que pretende analisar. O `text` o texto a ser analisados, os arquivos de atributo e o `id` pode ser qualquer valor. 

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

Utilizar o cliente criado anteriormente, chamar `detect_language()` e obter o resultado. Em seguida, iterar os resultados e imprimir o ID de cada documento e o primeiro idioma retornado.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id , ", Language: ", document.detected_languages[0].name)
```

### <a name="output"></a>Resultado

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidades

Criar uma lista de dicionários, que contém os documentos que pretende analisar. O `text` o texto a ser analisados, os arquivos de atributo e o `id` pode ser qualquer valor. 


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

Utilizar o cliente criado anteriormente, chamar `entities()` de função e obter o resultado. Em seguida, iterar os resultados e imprimir o ID de cada documento e as entidades contidas no mesmo.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ",entity.name, "\tType: ", entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Resultado

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

Criar uma lista de dicionários, que contém os documentos que pretende analisar. O `text` o texto a ser analisados, os arquivos de atributo e o `id` pode ser qualquer valor. 


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

Utilizar o cliente criado anteriormente, chamar `key_phrases()` de função e obter o resultado. Em seguida, iterar os resultados e imprimir o ID de cada documento e as expressões-chave contidas no mesmo.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t",phrase)
```

### <a name="output"></a>Resultado

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também

* [O que é a API de análise de texto?](../overview.md)
* [Cenários de utilizador de exemplo](../text-analytics-user-scenarios.md)
* [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)
