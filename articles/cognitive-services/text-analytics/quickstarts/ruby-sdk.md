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
ms.date: 01/29/2020
ms.author: aahi
ms.openlocfilehash: f34d4e50042f0fd05a224ff096c2b472224248f8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992402"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Quickstart: Use a biblioteca de clientes text Analytics para ruby

Introdução à biblioteca de cliente do Análise de Texto. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de cliente Análise de Texto para executar:

* Análise de sentimentos
* Deteção de idioma
* Reconhecimento de entidades
* Extração de expressões chave

> [!NOTE]
> Este quickstart aplica-se apenas à versão 2.1 do Text Analytics. Atualmente, uma biblioteca de clientes V3 para ruby não está disponível.

[Documentação de referência](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) |  | [(RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code) do [código-fonte de biblioteca](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Ruby](https://www.ruby-lang.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-text-analytics-azure-resource"></a>Criar um Análise de Texto recurso do Azure 

[!INCLUDE [text-analytics-resource-creation](../includes/quickstarts/resource-creation.md)]

### <a name="create-a-new-ruby-application"></a>Criar um novo aplicativo Ruby

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele. Em seguida, crie um arquivo chamado `GemFile`e um arquivo Ruby para seu código.

```console
mkdir myapp && cd myapp
```

No `GemFile`, adicione as linhas a seguir para adicionar a biblioteca de cliente como uma dependência.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Em seu arquivo Ruby, importe os pacotes a seguir.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Crie variáveis para o ponto de extremidade e a chave do Azure do recurso. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Modelo de objeto 

O Análise de Texto cliente é autenticado no Azure usando sua chave. O cliente fornece vários métodos para analisar o texto, como uma única cadeia de caracteres ou um lote. 

O texto é enviado para a API como uma lista de `documents`, que são `dictionary` objetos que contêm uma combinação de atributos `id`, `text`e `language` dependendo do método usado. O atributo `text` armazena o texto a ser analisado no `language`de origem e o `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente Análise de Texto para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de sentimentos](#sentiment-analysis)
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade](#entity-recognition)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie uma classe chamada `TextAnalyticsClient`. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

Nessa classe, crie uma função chamada `initialize` para autenticar o cliente usando sua chave e ponto de extremidade. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Fora da classe, use a função `new()` do cliente para instanciá-la.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Análise de sentimentos

No objeto cliente, crie uma função chamada `AnalyzeSentiment()` que usa uma lista de documentos de entrada que serão criados posteriormente. Chame a função de `sentiment()` do cliente e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e a pontuação de sentimentos. Uma pontuação mais próxima de 0 indica uma observação negativa, enquanto uma pontuação mais próxima de 1 indica uma observação positiva.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Fora da função de cliente, crie uma nova função chamada `SentimentAnalysisExample()` que usa o objeto `TextAnalyticsClient` criado anteriormente. Crie uma lista de objetos `MultiLanguageInput`, contendo os documentos que você deseja analisar. Cada objeto conterá um `id`, `Language` e um atributo `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do documento e o `id` pode ser qualquer valor. Em seguida, chame a função de `AnalyzeSentiment()` do cliente.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Chame a função `SentimentAnalysisExample()`.

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

No objeto cliente, crie uma função chamada `DetectLanguage()` que usa uma lista de documentos de entrada que serão criados posteriormente. Ligue para a função `detect_language()` do cliente e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e a linguagem detectada.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Fora da função de cliente, crie uma nova função chamada `DetectLanguageExample()` que usa o objeto `TextAnalyticsClient` criado anteriormente. Crie uma lista de objetos `LanguageInput`, contendo os documentos que você deseja analisar. Cada objeto conterá um `id`e um atributo `text`. O atributo `text` armazena o texto a ser analisado e o `id` pode ser qualquer valor. Em seguida, chame a função de `DetectLanguage()` do cliente.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Chame a função `DetectLanguageExample()`.

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

No objeto cliente, crie uma função chamada `RecognizeEntities()` que usa uma lista de documentos de entrada que serão criados posteriormente. Chame a função de `entities()` do cliente e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e as entidades reconhecidas.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Fora da função de cliente, crie uma nova função chamada `RecognizeEntitiesExample()` que usa o objeto `TextAnalyticsClient` criado anteriormente. Crie uma lista de objetos `MultiLanguageInput`, contendo os documentos que você deseja analisar. Cada objeto conterá um `id`, um `language`e um atributo `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do texto e o `id` pode ser qualquer valor. Em seguida, chame a função de `RecognizeEntities()` do cliente.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Chame a função `RecognizeEntitiesExample()`.

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

## <a name="key-phrase-extraction"></a>Extração de expressões chave

No objeto cliente, crie uma função chamada `ExtractKeyPhrases()` que usa uma lista de documentos de entrada que serão criados posteriormente. Ligue para a função `key_phrases()` do cliente e obtenha o resultado. Em seguida, itere pelos resultados e imprima a ID de cada documento e as frases-chave extraídas.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Fora da função de cliente, crie uma nova função chamada `KeyPhraseExtractionExample()` que usa o objeto `TextAnalyticsClient` criado anteriormente. Crie uma lista de objetos `MultiLanguageInput`, contendo os documentos que você deseja analisar. Cada objeto conterá um `id`, um `language`e um atributo `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do texto e o `id` pode ser qualquer valor. Em seguida, chame a função de `ExtractKeyPhrases()` do cliente.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Chame a função `KeyPhraseExtractionExample()`.

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
