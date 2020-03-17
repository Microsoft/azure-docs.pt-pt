---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 0803a847e9e864b361917df9f1a9c6b059ca2fe9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203433"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

[Documentação de referência v3](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b2/azure.ai.textanalytics.html) | [Código fonte da Biblioteca v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [Pacote v3 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [Exemplos v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[Documentação de referência v2](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Código fonte da Biblioteca v2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Pacote v2 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [Exemplos v2](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Assim que tiver a sua subscrição do Azure, pode <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Criar um recurso de Análise de Texto"  target="_blank">criar um recurso de Análise de Texto <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto final. 
    * Necessitará da chave e do ponto final do recurso criado para ligar a sua aplicação à API de Análise de Texto. Fará isto mais adiante no início rápido.
    * Pode utilizar o escalão de preço gratuito para experimentar o serviço e atualizar mais tarde para um escalão pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Depois de instalar o Python, pode instalar a biblioteca de cliente com:

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Quer ver o todo o ficheiro de código do início rápido de uma vez? Pode encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), que contém os exemplos de código deste início rápido. 

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Quer ver o todo o ficheiro de código do início rápido de uma vez? Pode encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), que contém os exemplos de código deste início rápido. 

---

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação Python

Crie um novo ficheiro Python e crie variáveis para o ponto final e a chave de subscrição do seu recurso do Azure.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Modelo de objeto

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

O cliente de Análise de Texto é um objeto `TextAnalyticsClient` que faz a autenticação no Azure com a sua chave. O cliente fornece vários métodos para analisar texto como um lote. 

No processamento em lotes, o texto é enviado para a API como uma lista de `documents`, que são objetos `dictionary` que contêm uma combinação de atributos `id`, `text` e `language`, consoante o método utilizado. O atributo `text` armazena o texto a analisar no `language`, de origem e o `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

O cliente de Análise de Texto é um objeto [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) que faz a autenticação com o Azure com a sua chave. O cliente fornece vários métodos para analisar o texto, como uma única cadeia ou um lote. 

O texto é enviado para a API como uma lista de `documents`, que são objetos `dictionary` com uma combinação de atributos `id`, `text` e `language` consoante o método utilizado. O atributo `text` armazena o texto a analisar no `language`, de origem e o `id` pode ser qualquer valor. 

---

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como realizar as seguintes tarefas com a biblioteca de cliente de Análise de Texto para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis)
* [Deteção de idioma](#language-detection)
* [Reconhecimento de entidades nomeadas](#named-entity-recognition-ner) 
* [Associação de entidades](#entity-linking)
* [Extração de expressões-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma função para instanciar o objeto `TextAnalyticsClient` com a sua `key` E `endpoint` criados acima. Em seguida, crie um novo cliente. 

```python
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiKeyCredential

def authenticate_client():
    ta_credential = TextAnalyticsApiKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Crie uma função para instanciar o objeto `TextAnalyticsClient` com a sua `key` E `endpoint` criados acima. Em seguida, crie um novo cliente. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Análise de sentimentos

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma nova função chamada `sentiment_analysis_example()` que assume o cliente como um argumento e, em seguida, chama a função `analyze_sentiment()`. O objeto de resposta devolvido incluirá a classificação e a etiqueta de sentimento de todo o documento de entrada, bem como uma análise de sentimentos para cada frase.


```python
def sentiment_analysis_example(client):

    document = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(inputs=document)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Length: {}]".format(sentence.grapheme_length))
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

[Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

[Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Autentique um objeto de cliente e chame a função [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-). Itere os resultados e imprima o ID de cada documento e a classificação de sentimento. Uma classificação próxima de 0 indica um sentimento negativo, enquanto que uma classificação próxima de 1 indica um sentimento positivo.

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

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma nova função chamada `language_detection_example()` que assume o cliente como um argumento e, em seguida, chama a função `detect_language()`. O objeto de resposta devolvido incluirá o idioma detetado em `primary_language` se for bem-sucedido e um `error` se não for.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar os idiomas com base na entrada. Pode utilizar o parâmetro `country_hint` para especificar um código de país de 2 letras. Por predefinição, a API utiliza "US" como countryHint predefinida. Para remover este comportamento, pode repor este parâmetro definindo este valor como uma cadeia vazia `country_hint : ""`. 

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

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Com o cliente criado anteriormente, chame [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) e obtenha o resultado. Em seguida, itere os resultados e imprima o ID de cada documento e o primeiro idioma devolvido.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Saída

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

> [!NOTE]
> Na versão `3.0-preview`:
> * o NER inclui métodos separados para detetar informações pessoais. 
> * A associação de entidades é um pedido separado do NER.

Crie uma nova função chamada `entity_recognition_example` que assume o cliente como um argumento e, em seguida, chama a função `recognize_entities()` e itera os resultados. O objeto de resposta devolvido incluirá a lista de entidades detetadas em `entity` se for bem-sucedido e um `error` se não for. Para cada entidade detetada, imprima a respetiva Categoria e Subcategoria se existirem.

```python
def entity_recognition_example(client):

    try:
        document = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(inputs= document)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Saída

```console
Named Entities:

    Text:    Seattle        Category:        Location       SubCategory:     GPE
    Length:          7      Confidence Score:        0.92

    Text:    last week      Category:        DateTime       SubCategory:     DateRange
    Length:          9      Confidence Score:        0.8
```

## <a name="using-ner-to-detect-personal-information"></a>Utilizar o NER para detetar informações pessoais

Crie uma nova função chamada `entity_pii_example()` que assume o cliente como um argumento e, em seguida, chama a função `recognize_pii_entities()` e obtém o resultado. Em seguida, itere os resultados e imprima as entidades.

```python
def entity_pii_example(client):

        document = ["Insurance policy for SSN on file 123-12-1234 is here by approved."]


        result = client.recognize_pii_entities(inputs= document)[0]
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tCategory: ", entity.category,"\tSubCategory: ", entity.subcategory)
            print("\t\tLength: ", entity.grapheme_length, "\tScore: {0:.2f}".format(entity.score), "\n")
        
entity_pii_example(client)
```

### <a name="output"></a>Saída

```console
Personally Identifiable Information Entities:
    Text:  123-12-1234      Category:  U.S. Social Security Number (SSN)    SubCategory:  None
        Length:  11     Score: 0.85
```


## <a name="entity-linking"></a>Ligar à Entidade

Crie uma nova função chamada `entity_linking_example()` que assume o cliente como um argumento e, em seguida, chama a função `recognize_linked_entities()` e itera os resultados. O objeto de resposta devolvido incluirá a lista de entidades detetadas em `entities` se for bem-sucedido e um `error` se não for. Uma vez que as entidades associadas são identificadas exclusivamente, as ocorrências da mesma entidade são agrupadas num objeto `entity` como uma lista de objetos `match`.

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
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.2f}".format(match.score), "\tLength: {}\n".format(match.grapheme_length))
            
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
        Score: 0.78     Length: 11

    Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.55     Length: 10

        Text: Gates
        Score: 0.55     Length: 5

    Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.53     Length: 10

    Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.47     Length: 9

        Text: Microsoft
        Score: 0.47     Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.25     Length: 7

    Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.28     Length: 5

```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a associação de entidades está incluída na resposta do NER.

Com o cliente criado anteriormente, chame a função [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) e obtenha o resultado. Em seguida, itere os resultados e imprima o ID de cada documento, bem como as entidades nele contidas.

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


#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma nova função chamada `key_phrase_extraction_example()` que assume o cliente como um argumento e, em seguida, chama a função `extract_key_phrases()`. O resultado incluirá a lista de expressões-chave detetadas em `key_phrases` se for bem-sucedido e um `error` se não for. Imprima as expressões-chave detetadas.

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

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Com o cliente criado anteriormente, chame a função [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) e obtenha o resultado. Em seguida, itere os resultados e imprima o ID de cada documento, bem como as expressões-chave nele contidas.

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