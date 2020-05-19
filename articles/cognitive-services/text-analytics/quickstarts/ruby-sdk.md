---
title: 'Quickstart: Biblioteca de clientes de Text Analytics para ruby Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, detete a linguagem utilizando a biblioteca cliente Ruby Text Analytics da Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 039a52c9ab0bfc460116e48086c854f4d7e8efb4
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996974"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Quickstart: Use a biblioteca de clientes text Analytics para ruby

Começar com a biblioteca de clientes text Analytics. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Utilize a biblioteca de clientes Text Analytics para executar:

* Análise de sentimentos
* Deteção de idioma
* Reconhecimento de entidades
* Extração de expressões-chave

> [!NOTE]
> Este quickstart aplica-se apenas à versão 2.1 do Text Analytics. Atualmente, uma biblioteca de clientes V3 para ruby não está disponível.

Código fonte [da biblioteca](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics)  |  [Pacote (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual de [Ruby](https://www.ruby-lang.org/)
* Assim que tiver a sua subscrição Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" crie um recurso Text "  target="_blank"> Analytics criar um recurso Text Analytics no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. 
    * Necessitará da chave e do ponto final do recurso que cria para ligar a sua aplicação à API textanalytics. Vais fazer isto mais tarde, no início.
    * Você pode usar o nível de preços gratuitos para experimentar o serviço, e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-ruby-application"></a>Criar uma nova aplicação Ruby

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela. Em seguida, crie um ficheiro chamado `GemFile` e um ficheiro Ruby para o seu código.

```console
mkdir myapp && cd myapp
```

Na sua `GemFile` , adicione as seguintes linhas para adicionar a biblioteca do cliente como uma dependência.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

No seu ficheiro Ruby, importe os seguintes pacotes.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Crie variáveis para o ponto final e chave azure do seu recurso. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Modelo de objeto 

O cliente Text Analytics autentica o Azure usando a sua chave. O cliente fornece vários métodos para analisar texto, como uma única corda, ou um lote. 

O texto é enviado para a API como uma lista `documents` de, que são `dictionary` objetos que contêm uma combinação `id` `text` de, e `language` atributos dependendo do método utilizado. O `text` atributo armazena o texto a ser analisado na `language` origem, e pode `id` ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes text Analytics para ruby:

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis)
* [Deteção de linguagem](#language-detection)
* [Reconhecimento de entidades](#entity-recognition)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Criar uma classe `TextAnalyticsClient` chamada. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

Nesta aula, crie uma função chamada `initialize` para autenticar o cliente usando a sua chave e ponto final. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Fora da aula, use a função do cliente `new()` para instantaneaizá-lo.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Análise de sentimentos

No objeto do cliente, crie uma função chamada `AnalyzeSentiment()` que recorra a uma lista de documentos de entrada que serão criados mais tarde. Ligue para a função do cliente `sentiment()` e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir a identificação de cada documento, e pontuação de sentimento. Uma pontuação mais próxima de 0 indica um sentimento negativo, enquanto uma pontuação mais próxima de 1 indica um sentimento positivo.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Fora da função do cliente, crie uma nova função chamada `SentimentAnalysisExample()` que leve o objeto criado `TextAnalyticsClient` anteriormente. Crie uma lista de `MultiLanguageInput` objetos, contendo os documentos que pretende analisar. Cada objeto conterá um `id` `Language` atributo e um `text` atributo. O `text` atributo armazena o texto a ser analisado, `language` é a linguagem do documento, e pode ser qualquer `id` valor. Então ligue para a função do `AnalyzeSentiment()` cliente.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Ligue para a `SentimentAnalysisExample()` função.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Saída

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Deteção de idioma

No objeto do cliente, crie uma função chamada `DetectLanguage()` que recorra a uma lista de documentos de entrada que serão criados mais tarde. Ligue para a função do cliente `detect_language()` e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir a identificação de cada documento, e linguagem detetada.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Fora da função do cliente, crie uma nova função chamada `DetectLanguageExample()` que leve o objeto criado `TextAnalyticsClient` anteriormente. Crie uma lista de `LanguageInput` objetos, contendo os documentos que pretende analisar. Cada objeto conterá um `id` atributo e um `text` atributo. O `text` atributo armazena o texto a ser analisado, e o `id` pode ser qualquer valor. Então ligue para a função do `DetectLanguage()` cliente.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Ligue para a `DetectLanguageExample()` função.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Saída

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Reconhecimento de entidades

No objeto do cliente, crie uma função chamada `RecognizeEntities()` que recorra a uma lista de documentos de entrada que serão criados mais tarde. Ligue para a função do cliente `entities()` e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir o ID de cada documento, e as entidades reconhecidas.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Fora da função do cliente, crie uma nova função chamada `RecognizeEntitiesExample()` que leve o objeto criado `TextAnalyticsClient` anteriormente. Crie uma lista de `MultiLanguageInput` objetos, contendo os documentos que pretende analisar. Cada objeto conterá um `id` `language` atributo e um `text` atributo. O `text` atributo armazena o texto a ser analisado, `language` é a linguagem do texto, e pode ser qualquer `id` valor. Então ligue para a função do `RecognizeEntities()` cliente.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Ligue para a `RecognizeEntitiesExample()` função.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Saída

```console
===== ENTITY RECOGNITION =====
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

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Extração de expressões-chave

No objeto do cliente, crie uma função chamada `ExtractKeyPhrases()` que recorra a uma lista de documentos de entrada que serão criados mais tarde. Ligue para a função do cliente `key_phrases()` e obtenha o resultado. Em seguida, iterar através dos resultados, e imprimir o ID de cada documento, e as frases-chave extraídas.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Fora da função do cliente, crie uma nova função chamada `KeyPhraseExtractionExample()` que leve o objeto criado `TextAnalyticsClient` anteriormente. Crie uma lista de `MultiLanguageInput` objetos, contendo os documentos que pretende analisar. Cada objeto conterá um `id` `language` atributo e um `text` atributo. O `text` atributo armazena o texto a ser analisado, `language` é a linguagem do texto, e pode ser qualquer `id` valor. Então ligue para a função do `ExtractKeyPhrases()` cliente.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Ligue para a `KeyPhraseExtractionExample()` função.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

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
                rock
Document ID: 4
         Key phrases:
                fútbol
```
