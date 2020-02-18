---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2019
ms.author: aahi
ms.openlocfilehash: b553cd2bed68bc8df7241f4f843b32ec13b1a08a
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2020
ms.locfileid: "77372043"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

[v3 Documentação de referência](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b2/azure.ai.textanalytics.html) | [v3 Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | pacote [v3 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

[v2 Documentação de referência](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [código fonte da biblioteca v2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [pacote v2 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2 Amostras](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Depois de instalar a Python, pode instalar a biblioteca do cliente com:

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

---

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie um novo arquivo Python e crie variáveis para a chave de assinatura e o ponto de extremidade do Azure do recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Modelo de objeto

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

O cliente Text Analytics é um objeto `TextAnalyticsClient` que autentica o Azure usando a sua chave. O cliente fornece vários métodos para analisar o texto como um lote. 

Quando o texto de processamento do lote é enviado para a API como uma lista de `documents`, que são `dictionary` objetos que contêm uma combinação de `id`, `text`, e `language` atributos dependendo do método utilizado. O atributo `text` armazena o texto a analisar na origem `language`, e o `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

O cliente Text Analytics é um objeto [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) que autentica o Azure usando a sua chave. O cliente fornece vários métodos para analisar texto, como uma única corda, ou um lote. 

O texto é enviado para a API como uma lista de `documents`, que são `dictionary` objetos que contêm uma combinação de `id`, `text`, e `language` atributos dependendo do método utilizado. O atributo `text` armazena o texto a analisar na origem `language`, e o `id` pode ser qualquer valor. 

---

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Análise de Texto para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis)
* [Deteção de linguagem](#language-detection)
* [Reconhecimento de Entidade Nomeada](#named-entity-recognition-ner) 
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma função para instantaneamente o objeto `TextAnalyticsClient` com o seu `key` e `endpoint` criado acima. Então crie um novo cliente. 

```python
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiKeyCredential

def authenticate_client():
    ta_credential = TextAnalyticsApiKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Crie uma função para instantaneamente o objeto `TextAnalyticsClient` com o seu `key` e `endpoint` criado acima. Então crie um novo cliente. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Análise de sentimentos

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma nova função chamada `sentiment_analysis_example()` que leve o cliente como argumento, e depois chama a função `analyze_sentiment()`. O objeto de resposta retornado conterá o rótulo de sentimentos e a pontuação de todo o documento de entrada, bem como uma análise de sentimentos para cada frase.


```python
def sentiment_analysis_example(client):

    document = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(inputs=document)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.sentiment_scores.positive,
        response.sentiment_scores.neutral,
        response.sentiment_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentiment_scores.positive,
            sentence.sentiment_scores.neutral,
            sentence.sentiment_scores.negative,
        ))

            
sentiment_analysis_example(client)
```

### <a name="output"></a>Saída

```console
Document Sentiment: positive
Overall scores: positive=1.000; neutral=0.000; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.000
Neutral=0.000
Negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.210
Neutral=0.770
Negative=0.020
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

Autenticar um objeto de cliente e chamar a função de [sentimento.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) Iterar através dos resultados, e imprimir a identificação de cada documento, e pontuação de sentimento. Uma pontuação mais próxima de 0 indica um sentimento negativo, enquanto uma pontuação mais próxima de 1 indica um sentimento positivo.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Saída

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Deteção de idioma

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma nova função chamada `language_detection_example()` que leve o cliente como argumento, e depois chama a função `detect_language()`. O objeto de resposta devolvido conterá a linguagem detetada em `primary_language` se for bem sucedido, e uma `error` se não for.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar o parâmetro `country_hint` para especificar um código de país de 2 letras. Por padrão, a API está a usar os "EUA" como país padrãoHint, para remover este comportamento pode redefinir este parâmetro definindo este valor para `country_hint : ""`de cadeias vazias . 

```python
def language_detection_example(client):
    try:
        document = ["Ce document est rédigé en Français."]
        response = client.detect_language(inputs = document, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Saída

```console
Language:  French
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

Usando o cliente criado anteriormente, ligue [para detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir a identificação de cada documento, e a primeira linguagem devolvida.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Saída

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidade Seleção (NER)

#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

> [!NOTE]
> Na versão `3.0-preview`:
> * O NER inclui métodos separados para detetar informações pessoais. 
> * A ligação de entidades é um pedido separado do NER.

Crie uma nova função chamada `entity_recognition_example` que leve o cliente como argumento, depois chama a função `recognize_entities()` e iterates através dos resultados. O objeto de resposta devolvido conterá a lista de entidades detetadas em `entity` se for bem sucedido, e uma `error` se não for. Para cada entidade detetada, imprima a sua categoria e subcategoria se existir.

```python
def entity_recognition_example(client):

    try:
        document = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(inputs= document)[0]

        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Saída

```console
Named Entities:

    Text:    Seattle    Category:    Location   SubCategory:     GPE 
    Offset:      26     Length:      26     Confidence Score:    0.92 

    Text:    last week  Category:    DateTime   SubCategory:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="using-ner-to-detect-personal-information"></a>Utilização do NER para detetar informações pessoais

Crie uma nova função chamada `entity_pii_example()` que leve o cliente como argumento, depois chama a função `recognize_pii_entities()` e obtém o resultado. Em seguida, itere os resultados e imprima as entidades.

```python
def entity_pii_example(client):

        document = ["Insurance policy for SSN on file 123-12-1234 is here by approved."]


        result = client.recognize_pii_entities(inputs= document)[0]
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tCategory: ", entity.category,"\tSubCategory: ", entity.subcategory)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(client)
```

### <a name="output"></a>Saída

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Category:  U.S. Social Security Number (SSN)    SubCategory:  None
        Offset:  33     Length:  11     Score: 0.850 
```


## <a name="entity-linking"></a>Ligar à Entidade

Crie uma nova função chamada `entity_linking_example()` que leve o cliente como argumento, depois chama a função `recognize_linked_entities()` e iterates através dos resultados. O objeto de resposta devolvido conterá a lista de entidades detetadas em `entities` se for bem sucedido, e uma `error` se não for. Uma vez que as entidades ligadas são identificadas de forma única, as ocorrências da mesma entidade são agrunadas sob um objeto `entity` como uma lista de objetos `match`.

```python
def entity_linking_example(client):

    try:
        document = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(inputs= document)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Saída

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  125    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  179    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  168    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  98     Length: 5

```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a ligação da entidade está incluída na resposta NER.

Utilizando o cliente criado anteriormente, ligue para a função [das entidades](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir a identificação de cada documento, e as entidades contidas nele.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>Saída

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Extração de expressões-chave


#### <a name="version-30-previewtabversion-3"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma nova função chamada `key_phrase_extraction_example()` que leve o cliente como argumento, e depois chama a função `extract_key_phrases()`. O resultado conterá a lista de frases-chave detetadas em `key_phrases` se for bem sucedida, e uma `error` se não for. Imprima as frases-chave detetadas.

```python
def key_phrase_extraction_example(client):

    try:
        document = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(inputs= document)[0]

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

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

Utilizando o cliente criado anteriormente, ligue para a função [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir o ID de cada documento, e as frases-chave contidas nele.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Saída

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---