---
title: 'Quickstart: Text Analytics biblioteca de clientes para C# Microsoft Docs'
description: 'Começa com a biblioteca de clientes Text Analytics para C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: e27719f7a639a97a0cde04848a51ba6135c3199e
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84346286"
---
<a name="HOLTop"></a>

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[v3 Documentação](https://aka.ms/azsdk-net-textanalytics-ref-docs)  |  de referência [v3 Código](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics)  |  fonte da biblioteca [pacote v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [v3 Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[v2 Documentação](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview)  |  de referência [v2 Código](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics)  |  fonte da biblioteca [v2 Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/)  |  [v2 Amostras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/)
* O [Estúdio Visual IDE](https://visualstudio.microsoft.com/vs/)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" Crie um recurso Text Analytics crie um recurso Text Analytics no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final.  Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API de Análise de Texto. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-net-core-application"></a>Criar uma nova aplicação .NET Core

Utilizando o Visual Studio IDE, crie uma nova aplicação de consola .NET Core. Isto criará um projeto "Hello World" com um único ficheiro de origem C#: *program.cs*.

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Instale a biblioteca do cliente clicando à direita na solução no Explorador de **Soluções** e selecione **Gerir pacotes NuGet**. No gestor de pacotes que abre **selecione Navegar,** verificar **Incluir pré-relançar,** e procurar `Azure.AI.TextAnalytics` . Selecione a versão `1.0.0-preview.5` e, em seguida, **instale**. Também pode utilizar a [Consola Gestor de Pacotes.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs)que contém os exemplos de código neste arranque rápido. 

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Instale a biblioteca do cliente clicando à direita na solução no Explorador de **Soluções** e selecione **Gerir pacotes NuGet**. No gestor de pacotes que abre, **selecione Procurar** e procurar `Microsoft.Azure.CognitiveServices.Language.TextAnalytics` . Clique nele e, em seguida, **instale**. Também pode utilizar a [Consola Gestor de Pacotes.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs)que contém os exemplos de código neste arranque rápido. 

---

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

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

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Abra o ficheiro *program.cs* e adicione as `using` seguintes diretivas:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Na classe da `Program` aplicação, crie variáveis para a chave e ponto final do seu recurso. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Substitua o método da `Main` aplicação. Definirá os métodos aqui chamados mais tarde.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Modelo de objeto

O cliente Text Analytics é um `TextAnalyticsClient` objeto que autentica a Azure usando a sua chave, e fornece funções para aceitar texto como cordas simples ou como um lote. Pode enviar sms para a API de forma sincronizada ou assíncronea. O objeto de resposta conterá as informações de análise de cada documento que enviar. 

Se estiver a utilizar a versão `3.0` do serviço, pode utilizar uma instância opcional `TextAnalyticsClientOptions` para inicializar o cliente com várias definições predefinidos (por exemplo, linguagem predefinição ou sugestão de país/região). Também pode autenticar usando um token Azure Ative Directory. 

## <a name="code-examples"></a>Exemplos de código

* [Análise de sentimento](#sentiment-analysis)
* [Deteção linguística](#language-detection)
* [Reconhecimento de Entidades Nomeadas](#named-entity-recognition-ner)
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Certifique-se de que o seu método principal de anteriormente cria um novo objeto cliente com o seu ponto final e credenciais.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma nova `ApiKeyServiceClientCredentials` classe para armazenar as credenciais e adicioná-las aos pedidos do cliente. Dentro dela, crie uma sobreposição para `ProcessHttpRequestAsync()` que adicione a sua chave ao `Ocp-Apim-Subscription-Key` cabeçalho.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Crie um método para instantaneaizar o objeto [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) com o seu ponto final e um `ApiKeyServiceClientCredentials` objeto que contenha a sua chave.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Análise de sentimentos

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

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

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma nova função chamada `SentimentAnalysisExample()` que leva o cliente que criou anteriormente, e chame a sua função De [Sentimento().](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) O objeto [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) devolvido conterá o sentimento `Score` se for bem sucedido, e um se `errorMessage` não. 

Uma pontuação que está perto de 0 indica um sentimento negativo, enquanto uma pontuação que está mais perto de 1 indica um sentimento positivo.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Deteção de idioma

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)


Crie uma nova função chamada `LanguageDetectionExample()` que leva o cliente que criou anteriormente, e chama a sua `DetectLanguage()` função. O objeto devolvido `Response<DetectedLanguage>` conterá a linguagem detetada juntamente com o seu nome e código ISO-6391. Se houve um erro, vai lançar um `RequestFailedException` .

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

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma nova função chamada `languageDetectionExample()` que leva o cliente que criou anteriormente, e chame a sua função [DetectLanguage().](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) O objeto [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) devolvido conterá a lista de idiomas detetados `DetectedLanguages` se for bem sucedido e um se `errorMessage` não. Imprima a primeira língua devolvida.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar o `countryHint` parâmetro para especificar um código país/região de 2 letras. Por predefinição, a API está a usar os "EUA" como país padrãoHint, para remover este comportamento pode redefinir este parâmetro definindo este valor para cadeia vazia `countryHint = ""` .

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Saída

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)


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

## <a name="entity-linking"></a>Ligação de entidades

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

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a ligação da entidade está incluída na resposta NER.

Criar uma nova função chamada `RecognizeEntitiesExample()` que leva o cliente que criou anteriormente, e ligue para a função [entidades.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) Iterar através dos resultados. O objeto [Deresertivo de Entidades devolvidas](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) conterá a lista de entidades detetadas `Entities` se for bem sucedida, e se não `errorMessage` for. Para cada entidade detetada, imprima o seu tipo, subtipo, nome da Wikipédia (se existirem) bem como as localizações no texto original.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extração de expressões-chave

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

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

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma nova função chamada `KeyPhraseExtractionExample()` que leva o cliente que criou anteriormente e chama a sua função [KeyPhrases().](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) O resultado conterá a lista de frases-chave detetadas `KeyPhrases` se for bem sucedida, e se não `errorMessage` for. Imprima quaisquer frases-chave detetadas.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

---
