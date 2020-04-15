---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: aahi
ms.openlocfilehash: 481fef7121dcbf2939b6e2a296763166dfc9e618
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384882"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

[v3 Documentação de referência](https://aka.ms/azsdk-python-textanalytics-ref-docs) | [v3 Biblioteca código](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | fonte[v3 Pacote (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[v2 Documentação de referência](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [v2 Código fonte](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | da biblioteca[v2 Pacote v2 (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2 Amostras](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Assim que tiver a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="sua subscrição"  target="_blank">Azure, crie um recurso Text Analytics criar um recurso <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics no portal Azure para obter a sua chave e ponto final. Depois de ser implantado, clique em **ir para o recurso**.
    * Necessitará da chave e do ponto final do recurso que cria para ligar a sua aplicação à API textanalytics. Vaicolar a chave e o ponto final no código abaixo no arranque rápido.
    * Você pode usar o nível de preços gratuitos (`F0`) para experimentar o serviço, e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Depois de instalar a Python, pode instalar a biblioteca do cliente com:

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

```console
pip install azure-ai-textanalytics==1.0.0b3
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)que contém os exemplos de código neste arranque rápido. 

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py)que contém os exemplos de código neste arranque rápido. 

---

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie um novo ficheiro Python e crie variáveis para o ponto final do seu recurso Azure e chave de subscrição.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Modelo de objeto

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

O cliente Text `TextAnalyticsClient` Analytics é um objeto que autentica o Azure usando a sua chave. O cliente fornece vários métodos para analisar o texto como um lote. 

Quando o texto de processamento do lote `documents`é enviado `dictionary` para a `id`API como uma lista de , que são objetos que contêm uma combinação de , `text`e `language` atributos dependendo do método utilizado. O `text` atributo armazena o texto `language`a `id` ser analisado na origem, e pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

O cliente Text Analytics é um objeto [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) que autentica o Azure usando a sua chave. O cliente fornece vários métodos para analisar texto, como uma única corda, ou um lote. 

O texto é enviado para a `documents`API `dictionary` como uma `id`lista `text`de, que são objetos que contêm uma combinação de, e `language` atributos dependendo do método utilizado. O `text` atributo armazena o texto `language`a `id` ser analisado na origem, e pode ser qualquer valor. 

---

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes text Analytics para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis)
* [Deteção de linguagem](#language-detection)
* [Reconhecimento de Entidade Nomeada](#named-entity-recognition-ner) 
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma função `TextAnalyticsClient` para `key` instantaneamente o objeto com o seu e `endpoint` criado acima. Então crie um novo cliente. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Crie uma função `TextAnalyticsClient` para `key` instantaneamente o objeto com o seu e `endpoint` criado acima. Então crie um novo cliente. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Análise de sentimentos

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Criar uma nova `sentiment_analysis_example()` função chamada que leve o `analyze_sentiment()` cliente como argumento, em seguida, chama a função. O objeto de resposta devolvido conterá o rótulo de sentimento e a pontuação de todo o documento de entrada, bem como uma análise de sentimento para cada frase.


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

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Criar uma nova `language_detection_example()` função chamada que leve o `detect_language()` cliente como argumento, em seguida, chama a função. O objeto de resposta devolvido conterá a linguagem detetada em `primary_language` caso de sucesso, e um `error` se não.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar `country_hint` o parâmetro para especificar um código de país de 2 letras. Por padrão, a API está a usar os "EUA" como país padrãoHint, para remover este `country_hint : ""`comportamento pode redefinir este parâmetro definindo este valor para uma cadeia vazia . 

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

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

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

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

> [!NOTE]
> Na `3.0-preview`versão: 
> * A ligação de entidades é um pedido separado do NER.

Crie uma `entity_recognition_example` nova função chamada que leve `recognize_entities()` o cliente como argumento, depois chama a função e iterates através dos resultados. O objeto de resposta devolvido conterá a `entity` lista de `error` entidades detetadas em caso de sucesso, e um se não. Para cada entidade detetada, imprima a sua categoria e subcategoria se existir.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

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

## <a name="entity-linking"></a>Ligar à Entidade

Crie uma `entity_linking_example()` nova função chamada que leve `recognize_linked_entities()` o cliente como argumento, depois chama a função e iterates através dos resultados. O objeto de resposta devolvido conterá a `entities` lista de `error` entidades detetadas em caso de sucesso, e um se não. Uma vez que as entidades ligadas são identificadas de `entity` forma única, `match` as ocorrências da mesma entidade são agrunadas sob um objeto como uma lista de objetos.

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
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score), "\tLength: {}\n".format(match.grapheme_length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Saída

```console
Linked Entities:

    Name:  Altair 8800     Id:  Altair 8800     Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Confidence Score: 0.00     Length: 11

    Name:  Bill Gates     Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Confidence Score: 0.00     Length: 10

        Text: Gates
        Confidence Score: 0.00     Length: 5

    Name:  Paul Allen     Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Confidence Score: 0.00     Length: 10

    Name:  Microsoft     Id:  Microsoft     Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Confidence Score: 0.00     Length: 9

        Text: Microsoft
        Confidence Score: 0.00     Length: 9

    Name:  April 4     Id:  April 4     Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Confidence Score: 0.00     Length: 7

    Name:  BASIC     Id:  BASIC     Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Confidence Score: 0.00     Length: 5
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

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


#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Criar uma nova `key_phrase_extraction_example()` função chamada que leve o `extract_key_phrases()` cliente como argumento, em seguida, chama a função. O resultado conterá a lista de `key_phrases` frases-chave `error` detetadas em caso de sucesso, e um se não. Imprima as frases-chave detetadas.

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

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

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