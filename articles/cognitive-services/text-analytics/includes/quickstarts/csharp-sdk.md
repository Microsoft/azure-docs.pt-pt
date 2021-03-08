---
title: 'Quickstart: Text Analytics client library for C# | Microsoft Docs'
description: 'Começa com a biblioteca de clientes Text Analytics para C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 6fc7b347dfc12f1dd7758ccc2a2d1c58f11debad
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444422"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

[v3.1 Documentação](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview)  |  de referência [v3.1 Código](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)  |  fonte da biblioteca [v3.1 Pacote (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.3)  |  [v3.1 Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[v3 Documentação](/dotnet/api/azure.ai.textanalytics)  |  de referência [v3 Código](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics)  |  fonte da biblioteca [pacote v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [v3 Amostras](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

---

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* O [Estúdio Visual IDE](https://visualstudio.microsoft.com/vs/)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" Crie um recurso Text Analytics crie um recurso Text Analytics no portal "  target="_blank"> </a> Azure para obter a sua chave e ponto final.  Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API de Análise de Texto. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Para utilizar a função Analisar, necessitará de um recurso Text Analytics com o nível de preços padrão (S).

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-net-core-application"></a>Criar uma nova aplicação .NET Core

Utilizando o Visual Studio IDE, crie uma nova aplicação de consola .NET Core. Isto criará um projeto "Hello World" com um único ficheiro de origem C#: *program.cs*.

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Instale a biblioteca do cliente clicando à direita na solução no Explorador de **Soluções** e selecione **Gerir pacotes NuGet**. No gestor de pacotes que abre **selecione Navegar** e procurar `Azure.AI.TextAnalytics` . Verifique a **caixa de pré-estalaelase,** selecione versão `5.1.0-beta.3` e, em seguida, **instale**. Também pode utilizar a [Consola Gestor de Pacotes.](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Instale a biblioteca do cliente clicando à direita na solução no Explorador de **Soluções** e selecione **Gerir pacotes NuGet**. No gestor de pacotes que abre **selecione Navegar** e procurar `Azure.AI.TextAnalytics` . Selecione a versão `5.0.0` e, em seguida, **instale**. Também pode utilizar a [Consola Gestor de Pacotes.](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)


> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs)que contém os exemplos de código neste arranque rápido. 

---

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Abra o ficheiro *program.cs* e adicione as `using` seguintes diretivas:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Na classe da `Program` aplicação, crie variáveis para a chave e ponto final do seu recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Substitua o método da `Main` aplicação. Definirá os métodos aqui chamados mais tarde.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Abra o ficheiro *program.cs* e adicione as `using` seguintes diretivas:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Na classe da `Program` aplicação, crie variáveis para a chave e ponto final do seu recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Substitua o método da `Main` aplicação. Definirá os métodos aqui chamados mais tarde.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

---

## <a name="object-model"></a>Modelo de objeto

O cliente Text Analytics é um `TextAnalyticsClient` objeto que autentica a Azure usando a sua chave, e fornece funções para aceitar texto como cordas simples ou como um lote. Pode enviar sms para a API de forma sincronizada ou assíncronea. O objeto de resposta conterá as informações de análise de cada documento que enviar. 

Se estiver a utilizar a versão `3.x` do serviço, pode utilizar uma instância opcional `TextAnalyticsClientOptions` para inicializar o cliente com várias definições predefinidos (por exemplo, linguagem predefinição ou sugestão de país/região). Também pode autenticar usando um token Azure Ative Directory. 

## <a name="code-examples"></a>Exemplos de código

* [Análise de sentimentos](#sentiment-analysis)
* [Mineração de opinião](#opinion-mining)
* [Deteção linguística](#language-detection)
* [Reconhecimento de Entidades Nomeadas](#named-entity-recognition-ner)
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Certifique-se de que o seu método principal de anteriormente cria um novo objeto cliente com o seu ponto final e credenciais.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Certifique-se de que o seu método principal de anteriormente cria um novo objeto cliente com o seu ponto final e credenciais.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

---

## <a name="sentiment-analysis"></a>Análise de sentimentos

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Crie uma nova função chamada `SentimentAnalysisExample()` que leva o cliente que criou anteriormente, e chama a sua `AnalyzeSentiment()` função. O objeto devolvido `Response<DocumentSentiment>` conterá o rótulo de sentimento e a pontuação de todo o documento de entrada, bem como uma análise de sentimento para cada frase se for bem sucedida. Se houve um erro, vai lançar um `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>Mineração de opinião

Crie uma nova função chamada `SentimentAnalysisWithOpinionMiningExample()` que leva o cliente que criou anteriormente, e ligue para a sua `AnalyzeSentimentBatch()` função com `AdditionalSentimentAnalyses.OpinionMining` opção. O objeto devolvido `AnalyzeSentimentResultCollection` conterá a coleção `AnalyzeSentimentResult` em que representa `Response<DocumentSentiment>` . A diferença entre `SentimentAnalysis()` e é que esta última irá conter em cada `SentimentAnalysisWithOpinionMiningExample()` `MinedOpinion` frase, o que mostra um aspeto analisado e o(s) de opinião conexo. Se houve um erro, vai lançar um `RequestFailedException` .

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (MinedOpinion minedOpinion in sentence.MinedOpinions)
            {
                Console.WriteLine($"\tAspect: {minedOpinion.Aspect.Text}, Value: {minedOpinion.Aspect.Sentiment}");
                Console.WriteLine($"\tAspect positive score: {minedOpinion.Aspect.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tAspect negative score: {minedOpinion.Aspect.ConfidenceScores.Negative:0.00}");
                foreach (OpinionSentiment opinion in minedOpinion.Opinions)
                {
                    Console.WriteLine($"\t\tRelated Opinion: {opinion.Text}, Value: {opinion.Sentiment}");
                    Console.WriteLine($"\t\tRelated Opinion positive score: {opinion.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Opinion negative score: {opinion.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Saída

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Aspect: food, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: service, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: concierge, Value: Positive
        Aspect positive score: 1.00
        Aspect negative score: 0.00
                Related Opinion: nice, Value: Positive
                Related Opinion positive score: 1.00
                Related Opinion negative score: 0.00


Press any key to exit.
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie uma nova função chamada `SentimentAnalysisExample()` que leva o cliente que criou anteriormente, e chama a sua `AnalyzeSentiment()` função. O objeto devolvido `Response<DocumentSentiment>` conterá o rótulo de sentimento e a pontuação de todo o documento de entrada, bem como uma análise de sentimento para cada frase se for bem sucedida. Se houve um erro, vai lançar um `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

---

## <a name="language-detection"></a>Deteção de idioma

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)


Crie uma nova função chamada `LanguageDetectionExample()` que leva o cliente que criou anteriormente, e chama a sua  `DetectLanguage()` função. O objeto devolvido `Response<DetectedLanguage>` conterá a linguagem detetada juntamente com o seu nome e código ISO-6391. Se houve um erro, vai lançar um `RequestFailedException` .

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar o `countryHint` parâmetro para especificar um código país/região de 2 letras. Por predefinição, a API está a usar os "EUA" como país padrãoHint, para remover este comportamento pode redefinir este parâmetro definindo este valor para cadeia vazia `countryHint = ""` . Para definir um padrão diferente, desaperte a `TextAnalyticsClientOptions.DefaultCountryHint` propriedade e passe-a durante a inicialização do cliente.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Saída

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)


Crie uma nova função chamada `LanguageDetectionExample()` que leva o cliente que criou anteriormente, e chama a sua  `DetectLanguage()` função. O objeto devolvido `Response<DetectedLanguage>` conterá a linguagem detetada juntamente com o seu nome e código ISO-6391. Se houve um erro, vai lançar um `RequestFailedException` .

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar o `countryHint` parâmetro para especificar um código país/região de 2 letras. Por predefinição, a API está a usar os "EUA" como país padrãoHint, para remover este comportamento pode redefinir este parâmetro definindo este valor para cadeia vazia `countryHint = ""` . Para definir um padrão diferente, desaperte a `TextAnalyticsClientOptions.DefaultCountryHint` propriedade e passe-a durante a inicialização do cliente.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Saída

```console
Language:
        French, ISO-6391: fr
```


---

## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)


Criar uma nova função chamada `EntityRecognitionExample()` que leva o cliente que criou anteriormente, ligue para a sua `RecognizeEntities()` função e itere através dos resultados. O objeto devolvido `Response<CategorizedEntityCollection>` conterá a recolha de entidades detetadas. `CategorizedEntity` Se houve um erro, vai lançar um `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>Ligação de entidades

Criar uma nova função chamada `EntityLinkingExample()` que leva o cliente que criou anteriormente, ligue para a sua `RecognizeLinkedEntities()` função e itere através dos resultados. O objeto devolvido `Response<LinkedEntityCollection>` conterá a recolha de entidades detetadas. `LinkedEntity` Se houve um erro, vai lançar um `RequestFailedException` . Uma vez que as entidades ligadas são identificadas de forma única, as ocorrências da mesma entidade são agrupadas sob um `LinkedEntity` objeto como uma lista de `LinkedEntityMatch` objetos.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>Reconhecimento de informação pessoalmente identificável

Criar uma nova função chamada `RecognizePIIExample()` que leva o cliente que criou anteriormente, ligue para a sua `RecognizePiiEntities()` função e itere através dos resultados. O devolvido `PiiEntityCollection` representa a lista de entidades PII detetadas. Se houve um erro, vai lançar um `RequestFailedException` .

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Saída

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)


> [!NOTE]
> Novo na `3.0` versão:
> * A ligação da entidade é agora separada do reconhecimento de entidades.


Criar uma nova função chamada `EntityRecognitionExample()` que leva o cliente que criou anteriormente, ligue para a sua `RecognizeEntities()` função e itere através dos resultados. O objeto devolvido `Response<IReadOnlyCollection<CategorizedEntity>>` conterá a lista de entidades detetadas. Se houve um erro, vai lançar um `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>Ligação de entidades

Criar uma nova função chamada `EntityLinkingExample()` que leva o cliente que criou anteriormente, ligue para a sua `RecognizeLinkedEntities()` função e itere através dos resultados. O devolvido `Response<IReadOnlyCollection<LinkedEntity>>` representa a lista de entidades detetadas. Se houve um erro, vai lançar um `RequestFailedException` . Uma vez que as entidades ligadas são identificadas de forma única, as ocorrências da mesma entidade são agrupadas sob um `LinkedEntity` objeto como uma lista de `LinkedEntityMatch` objetos.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

--- 


### <a name="key-phrase-extraction"></a>Extração de expressões-chave

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Crie uma nova função chamada `KeyPhraseExtractionExample()` que leva o cliente que criou anteriormente, e chama a sua `ExtractKeyPhrases()` função. O objeto devolvido `<Response<KeyPhraseCollection>` conterá a lista de frases-chave detetadas. Se houve um erro, vai lançar um `RequestFailedException` .

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie uma nova função chamada `KeyPhraseExtractionExample()` que leva o cliente que criou anteriormente, e chama a sua `ExtractKeyPhrases()` função. O objeto devolvido `<Response<IReadOnlyCollection<string>>` conterá a lista de frases-chave detetadas. Se houve um erro, vai lançar um `RequestFailedException` .

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Utilize a API assíncronea com a operação Análise

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Crie uma nova função chamada `AnalyzeOperationExample()` que leva o cliente que criou anteriormente, e chama a sua `StartAnalyzeOperationBatch()` função. O objeto devolvido `AnalyzeOperation` conterá o objeto de `Operation` interface para `AnalyzeOperationResult` . Como se trata de uma Operação de Longa Duração, `await` para que o valor seja `operation.WaitForCompletionAsync()` atualizado. Uma vez terminado o `WaitForCompletionAsync()` resultado, a coleção deve ser atualizada no `operation.Value` . Se houve um erro, vai lançar um `RequestFailedException` .


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };

    AnalyzeOperationOptions operationOptions = new AnalyzeOperationOptions()
    {
        EntitiesTaskParameters = new EntitiesTaskParameters(),
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeOperation operation = client.StartAnalyzeOperationBatch(batchDocuments, operationOptions, "en");

    await operation.WaitForCompletionAsync();

    AnalyzeOperationResult resultCollection = operation.Value;

    RecognizeEntitiesResultCollection entitiesResult = resultCollection.Tasks.EntityRecognitionTasks[0].Results;

    Console.WriteLine("Analyze Operation Request Details");
    Console.WriteLine($"    Status: {resultCollection.Status}");
    Console.WriteLine($"    DisplayName: {resultCollection.DisplayName}");
    Console.WriteLine("");

    Console.WriteLine("Recognized Entities");

    foreach (RecognizeEntitiesResult result in entitiesResult)
    {
        Console.WriteLine($"    Recognized the following {result.Entities.Count} entities:");

        foreach (CategorizedEntity entity in result.Entities)
        {
            Console.WriteLine($"    Entity: {entity.Text}");
            Console.WriteLine($"    Category: {entity.Category}");
            Console.WriteLine($"    Offset: {entity.Offset}");
            Console.WriteLine($"    ConfidenceScore: {entity.ConfidenceScore}");
            Console.WriteLine($"    SubCategory: {entity.SubCategory}");
        }
        Console.WriteLine("");
    }
}
```

Depois de adicionar este exemplo à sua aplicação, ligue para o seu `main()` método utilizando `await` .

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>Saída

```console
Analyze Operation Request Details
    Status: succeeded
    DisplayName: Analyze Operation Quick Start Example

Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

Também pode utilizar a operação De analisar para detetar pii e extração de frases-chave. Veja a [amostra de Análise](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeOperation.md) no GitHub.

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Esta funcionalidade não está disponível na versão 3.0.

---
