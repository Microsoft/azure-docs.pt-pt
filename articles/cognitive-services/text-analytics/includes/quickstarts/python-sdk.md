---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.openlocfilehash: cee201c11d0415e1f63e7e6a9157b96a059503ba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765124"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

[v3.1 Documentação](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  de referência [v3.1 Código](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics)  |  fonte da biblioteca [v3.1 Pacote (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [v3.1 Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[v3 Documentação](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  de referência [v3 Código](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics)  |  fonte da biblioteca [v3 Pacote (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [v3 Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

---

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" Crie um recurso Text Analytics crie um recurso Text Analytics no portal "  target="_blank"> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API de Análise de Texto. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Para utilizar a função Analisar, necessitará de um recurso Text Analytics com o nível de preços padrão (S).

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Depois de instalar o Python, pode instalar a biblioteca do cliente com:

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

```console
pip install azure-ai-textanalytics --pre
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)que contém os exemplos de código neste arranque rápido. 

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)que contém os exemplos de código neste arranque rápido. 


---

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie um novo ficheiro Python e crie variáveis para a chave de finalização e subscrição do Azure do seu recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Modelo de objeto

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

O cliente Text Analytics é um `TextAnalyticsClient` objeto que autentica a Azure. O cliente fornece vários métodos para analisar texto. 

Quando o texto de processamento é enviado para a API como uma lista de `documents` , que é como uma lista de cordas, uma lista de representação semelhante a dict, ou como uma lista de `TextDocumentInput/DetectLanguageInput` . Um `dict-like` objeto contém uma combinação de , e `id` `text` `language/country_hint` . O `text` atributo armazena o texto a ser analisado na origem , e `country_hint` pode ser qualquer `id` valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

O cliente Text Analytics é um `TextAnalyticsClient` objeto que autentica a Azure usando a sua chave. O cliente fornece vários métodos para analisar o texto como um lote. 

Quando o texto de processamento de lote é enviado para a API como uma lista de `documents` , que são `dictionary` objetos que contêm uma combinação de `id` , e `text` `language` atributos dependendo do método utilizado. O `text` atributo armazena o texto a ser analisado na origem , e `language` pode ser qualquer `id` valor. 

O objeto de resposta é uma lista que contém a informação analisada para cada documento. 

---

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Text Analytics para Python:

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis)
* [Mineração de opinião](#opinion-mining)
* [Deteção linguística](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-ner) 
* [Reconhecimento de informação pessoalmente identificável](#personally-identifiable-information-recognition) 
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)


# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis)
* [Deteção linguística](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-ner) 
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>Autenticar o cliente

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Crie uma função para instantaneaizar o `TextAnalyticsClient` objeto com o seu E criado `key` `endpoint` acima. Então crie um novo cliente. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie uma função para instantaneaizar o `TextAnalyticsClient` objeto com o seu E criado `key` `endpoint` acima. Então crie um novo cliente. Tenha em atenção que `api_version=TextAnalyticsApiVersion.V3_0` deve ser definido para a utilização da versão 3.0.

```python
# use this code if you're using SDK version is 5.0.0
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential) 
    return text_analytics_client

client = authenticate_client()
```

Se instalou v5.1.0 da biblioteca cliente `pip install azure-ai-textanalytics --pre` utilizando, pode especificar v3.0 da API Text Analytics com o parâmetro do `api_version` cliente. Utilize apenas o seguinte `authenticate_client()` método se o seu cliente for v5.1.0 ou mais tarde.

```python
# Only use the following code sample if you're using v5.1.0 of the client library, 
# and are looking to specify v3.0 of the Text Analytics API for your client
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiVersion
from azure.core.credentials import AzureKeyCredential
def authenticate_client():
   ta_credential = AzureKeyCredential(key)
   text_analytics_client = TextAnalyticsClient(
     endpoint=endpoint,
     credential=ta_credential,
     api_version=TextAnalyticsApiVersion.V3_0
   )
   
client = authenticate_client()
```

--- 

## <a name="sentiment-analysis"></a>Análise de sentimentos

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Criar uma nova função chamada `sentiment_analysis_example()` que leva o cliente como um argumento, e depois chama a `analyze_sentiment()` função. O objeto de resposta devolvido conterá o rótulo de sentimento e a pontuação de todo o documento de entrada, bem como uma análise de sentimento para cada frase.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Saída

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>Mineração de opinião

Para fazer análise de sentimento com a mineração de opinião, crie uma nova função chamada `sentiment_analysis_with_opinion_mining_example()` que leva o cliente como argumento, e depois chama a `analyze_sentiment()` função com bandeira de opção `show_opinion_mining=True` . O objeto de resposta devolvido conterá não só o rótulo de sentimento e a pontuação de todo o documento de entrada com análise de sentimento para cada frase, mas também aspeto e análise de sentimento de nível de opinião.


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                target = mined_opinion.target
                print("......'{}' target '{}'".format(target.sentiment, target.text))
                print("......Target score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    target.confidence_scores.positive,
                    target.confidence_scores.negative,
                ))
                for assessment in mined_opinion.assessments:
                    print("......'{}' assessment '{}'".format(assessment.sentiment, assessment.text))
                    print("......Assessment score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        assessment.confidence_scores.positive,
                        assessment.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>Saída

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' target 'food'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'negative' target 'service'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'positive' target 'concierge'
......Target score:
......Positive=1.00
......Negative=0.00

......'positive' assessment 'nice'
......Assessment score:
......Positive=1.00
......Negative=0.00





Press any key to continue . . .

```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Criar uma nova função chamada `sentiment_analysis_example()` que leva o cliente como um argumento, e depois chama a `analyze_sentiment()` função. O objeto de resposta devolvido conterá o rótulo de sentimento e a pontuação de todo o documento de entrada, bem como uma análise de sentimento para cada frase.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Saída

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

---

## <a name="language-detection"></a>Deteção de idioma

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Criar uma nova função chamada `language_detection_example()` que leva o cliente como um argumento, e depois chama a `detect_language()` função. O objeto de resposta devolvido conterá a linguagem detetada se `primary_language` for bem sucedida e se não `error` for.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar o `country_hint` parâmetro para especificar um código de 2 letras do país. Por predefinição, a API está a usar os "EUA" como país padrãoHint, para remover este comportamento pode redefinir este parâmetro definindo este valor para cadeia vazia `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Saída

```console
Language:  French
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Criar uma nova função chamada `language_detection_example()` que leva o cliente como um argumento, e depois chama a `detect_language()` função. O objeto de resposta devolvido conterá a linguagem detetada se `primary_language` for bem sucedida e se não `error` for.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar o `country_hint` parâmetro para especificar um código de 2 letras do país. Por predefinição, a API está a usar os "EUA" como país padrãoHint, para remover este comportamento pode redefinir este parâmetro definindo este valor para cadeia vazia `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Saída

```console
Language:  French
```


---

## <a name="named-entity-recognition-ner"></a>Reconhecimento de entidade nomeada (NER)

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

> [!NOTE]
> Na `3.1` versão: 
> * A ligação da entidade é um pedido separado do que o NER.

Criar uma nova função chamada `entity_recognition_example` que leva o cliente como argumento, em seguida, chama a `recognize_entities()` função e itera através dos resultados. O objeto de resposta devolvido conterá a lista de entidades detetadas `entity` se for bem sucedido, e um se `error` não. Para cada entidade detetada, imprima a sua categoria e Sub-Category se existir.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Saída

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

### <a name="entity-linking"></a>Ligar à Entidade

Criar uma nova função chamada `entity_linking_example()` que leva o cliente como argumento, em seguida, chama a `recognize_linked_entities()` função e itera através dos resultados. O objeto de resposta devolvido conterá a lista de entidades detetadas `entities` se for bem sucedido, e um se `error` não. Uma vez que as entidades ligadas são identificadas de forma única, as ocorrências da mesma entidade são agrupadas sob um `entity` objeto como uma lista de `match` objetos.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Saída

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

### <a name="personally-identifiable-information-recognition"></a>Reconhecimento de informação pessoalmente identificável

Criar uma nova função chamada `pii_recognition_example` que leva o cliente como argumento, em seguida, chama a `recognize_pii_entities()` função e itera através dos resultados. O objeto de resposta devolvido conterá a lista de entidades detetadas `entity` se for bem sucedido, e um se `error` não. Para cada entidade detetada, imprima a sua categoria e Sub-Category se existir.

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>Saída

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

> [!NOTE]
> Na `3.0` versão: 
> * A ligação da entidade é um pedido separado do que o NER.

Criar uma nova função chamada `entity_recognition_example` que leva o cliente como argumento, em seguida, chama a `recognize_entities()` função e itera através dos resultados. O objeto de resposta devolvido conterá a lista de entidades detetadas `entity` se for bem sucedido, e um se `error` não. Para cada entidade detetada, imprima a sua categoria e Sub-Category se existir.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Saída

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

### <a name="entity-linking"></a>Ligar à Entidade

Criar uma nova função chamada `entity_linking_example()` que leva o cliente como argumento, em seguida, chama a `recognize_linked_entities()` função e itera através dos resultados. O objeto de resposta devolvido conterá a lista de entidades detetadas `entities` se for bem sucedido, e um se `error` não. Uma vez que as entidades ligadas são identificadas de forma única, as ocorrências da mesma entidade são agrupadas sob um `entity` objeto como uma lista de `match` objetos.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Saída

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

---

### <a name="key-phrase-extraction"></a>Extração de expressões-chave

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Criar uma nova função chamada `key_phrase_extraction_example()` que leva o cliente como um argumento, e depois chama a `extract_key_phrases()` função. O resultado conterá a lista de frases-chave detetadas `key_phrases` se for bem sucedida, e se não `error` for. Imprima quaisquer frases-chave detetadas.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Saída

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Criar uma nova função chamada `key_phrase_extraction_example()` que leva o cliente como um argumento, e depois chama a `extract_key_phrases()` função. O resultado conterá a lista de frases-chave detetadas `key_phrases` se for bem sucedida, e se não `error` for. Imprima quaisquer frases-chave detetadas.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Saída

```console
    Key Phrases:
         cat
         veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-batch-analyze-operation"></a>Use a API assíncroneamente com a operação de análise de lote

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Criar uma nova função chamada `analyze_batch_actions_example()` que leva o cliente como um argumento, e depois chama a `begin_analyze_batch_actions()` função. O resultado será uma operação de longa duração que será sondada para obter resultados.

```python
from azure.ai.textanalytics import (
    RecognizeEntitiesAction
)

def analyze_batch_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = client.begin_analyze_batch_actions(
            documents,
            display_name="Sample Text Analysis",
            actions=[RecognizeEntitiesAction()]
        )

        result = poller.result()
        action_results = [action_result for action_result in list(result) if not action_result.is_error]

        entities_recognition_task_result = action_results[0]
        print("Results of Entities Recognition action:")
        docs = [doc for doc in entities_recognition_task_result.document_results if not doc.is_error]

        for idx, doc in enumerate(docs):
            print("\nDocument text: {}".format(documents[idx]))
            for entity in doc.entities:
                print("Entity: {}".format(entity.text))
                print("...Category: {}".format(entity.category))
                print("...Confidence Score: {}".format(entity.confidence_score))
                print("...Offset: {}".format(entity.offset))
            print("------------------------------------------")

analyze_batch_example(client)
```

### <a name="output"></a>Saída

```console
Results of Entities Recognition task:
Document text: Microsoft was founded by Bill Gates and Paul Allen.
Entity: Microsoft
...Category: Organization
...Confidence Score: 0.83
...Offset: 0
Entity: Bill Gates
...Category: Person
...Confidence Score: 0.85
...Offset: 25
Entity: Paul Allen
...Category: Person
...Confidence Score: 0.9
...Offset: 40
------------------------------------------
```

Também pode utilizar a operação de análise de lote para detetar PII e executar a extração de frases-chave. Veja a [amostra de análise](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_batch_actions.py) de lote no GitHub.

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Esta funcionalidade não está disponível na versão 3.0.

---
