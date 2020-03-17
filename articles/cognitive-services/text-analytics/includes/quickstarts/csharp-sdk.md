---
title: 'Início Rápido: Biblioteca de cliente de Análise de Texto para C# | Microsoft Docs'
description: Comece a utilizar a biblioteca de cliente de Análise de Texto para C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 6adce0ed6b5b5768bd9a489fced25ce439a33e0a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203430"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

[Documentação de referência v3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Código fonte da Biblioteca v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Pacote v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Exemplos v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[Documentação de referência v2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Código fonte da Biblioteca v2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Pacote v2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Exemplos v2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/)
* Assim que tiver a sua subscrição do Azure, pode <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Criar um recurso de Análise de Texto"  target="_blank">criar um recurso de Análise de Texto <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto final. 
    * Necessitará da chave e do ponto final do recurso criado para ligar a sua aplicação à API de Análise de Texto. Fará isto mais adiante no início rápido.
    * Pode utilizar o escalão de preço gratuito para experimentar o serviço e atualizar mais tarde para um escalão pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-net-core-application"></a>Criar uma nova aplicação .NET Core

Com o IDE do Visual Studio, crie uma nova aplicação de consola .NET Core. Isto irá criar um projeto "Hello World" com um único ficheiro de origem C#: *program.cs*.

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Instale a biblioteca de cliente, clicando com o botão direito do rato na solução no **Explorador de Soluções** e selecionando **Gerir Pacotes NuGet**. No gestor de pacotes que abre, selecione **Procurar**, marque a caixa **Incluir pré-lançamento** e procure `Azure.AI.TextAnalytics`. Selecione a versão `1.0.0-preview.2` e, em seguida, **Instalar**. Também pode utilizar a [Consola do Gestor de Pacotes](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Quer ver o todo o ficheiro de código do início rápido de uma vez? Pode encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), que contém os exemplos de código deste início rápido. 

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Instale a biblioteca de cliente, clicando com o botão direito do rato na solução no **Explorador de Soluções** e selecionando **Gerir Pacotes NuGet**. No gestor de pacotes que abre, selecione **Procurar** e procure `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Clique nele e, em seguida, em **Instalar**. Também pode utilizar a [Consola do Gestor de Pacotes](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Quer ver o todo o ficheiro de código do início rápido de uma vez? Pode encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), que contém os exemplos de código deste início rápido. 

---

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Abra o ficheiro *program.cs* e adicione as seguintes diretivas `using`:

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Na classe `Program` da aplicação, crie variáveis para a chave e o ponto final do seu recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Substitua o método `Main` da aplicação. Irá definir os métodos aqui chamados mais tarde.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Abra o ficheiro *program.cs* e adicione as seguintes diretivas `using`:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Na classe `Program` da aplicação, crie variáveis para a chave e o ponto final do seu recurso. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Substitua o método `Main` da aplicação. Irá definir os métodos aqui chamados mais tarde.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Modelo de objeto

O cliente Análise de Texto é um objeto `TextAnalyticsClient` que autentica o Azure com a sua chave e fornece funções para aceitar texto como cadeias individuais ou como um lote. Pode enviar texto para a API sincronamente ou assincronamente. O objeto de resposta irá conter as informações de análise de cada documento que envia. 

Se estiver a utilizar a versão `3.0-preview`, pode utilizar uma instância de `TextAnalyticsClientOptions` opcional para inicializar o cliente com várias predefinições (por exemplo, idioma predefinido ou sugestão de país). Também pode fazer a autenticação com um token do Azure Active Directory. 

## <a name="code-examples"></a>Exemplos de código

* [Análise de sentimentos](#sentiment-analysis)
* [Deteção de idioma](#language-detection)
* [Reconhecimento de Entidades Nomeadas](#named-entity-recognition-ner)
* [Detetar informações pessoais](#detect-personal-information)
* [Associação de entidades](#entity-linking)
* [Extração de expressões-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Certifique-se de que o método principal do passo anterior cria um novo objeto de cliente com o seu ponto final e credenciais.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma nova classe `ApiKeyServiceClientCredentials` para armazenar as credenciais e adicioná-las aos pedidos do cliente. Dentro da mesma, crie uma substituição para `ProcessHttpRequestAsync()` que adicione a sua chave ao cabeçalho `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Crie um método para instanciar o objeto [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) com o seu ponto final e um objeto `ApiKeyServiceClientCredentials` que contenha a sua chave.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Análise de sentimentos

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma nova função chamada `SentimentAnalysisExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função `AnalyzeSentiment()`. O objeto `Response<DocumentSentiment>` devolvido incluirá a classificação e a etiqueta de sentimento de todo o documento de entrada, bem como uma análise de sentimentos para cada frase, se for bem-sucedido. Caso tenha ocorrido um erro, lançará uma `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [length {sentence.GraphemeLength}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.GraphemeOffset, sentence.GraphemeLength)}\"");
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

        Sentence [length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma nova função chamada `SentimentAnalysisExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet). O objeto [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) devolvido incluirá o sentimento `Score` se for bem-sucedido e uma `errorMessage` se não for. 

Uma classificação próxima de 0 indica um sentimento negativo, enquanto que uma classificação próxima de 1 indica um sentimento positivo.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Deteção de idioma

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)


Crie uma nova função chamada `LanguageDetectionExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função `DetectLanguage()`. O objeto `Response<DetectedLanguage>` devolvido incluirá o idioma detetado, juntamente com o respetivo nome e código ISO-6391. Caso tenha ocorrido um erro, lançará uma `RequestFailedException`.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar os idiomas com base na entrada. Pode utilizar o parâmetro `countryHint` para especificar um código de país de 2 letras. Por predefinição, a API utiliza "US" como countryHint predefinida. Para remover este comportamento, pode repor este parâmetro definindo este valor como uma cadeia vazia `countryHint = ""`. Para configurar uma predefinição diferente, defina a propriedade `TextAnalyticsClientOptions.DefaultCountryHint` e transmita-a durante a inicialização do cliente.

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

Crie uma nova função chamada `languageDetectionExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). O objeto [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) devolvido incluirá a lista de idiomas detetados em `DetectedLanguages` se for bem-sucedido e uma `errorMessage` se não for. Imprima o primeiro idioma devolvido.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar os idiomas com base na entrada. Pode utilizar o parâmetro `countryHint` para especificar um código de país de 2 letras. Por predefinição, a API utiliza "US" como countryHint predefinida. Para remover este comportamento, pode repor este parâmetro definindo este valor como uma cadeia vazia `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Saída

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)


> [!NOTE]
> Novidade na versão `3.0-preview`:
> * Agora, o reconhecimento de entidades inclui a capacidade de detetar informações pessoais em texto.
> * A associação de entidades é agora separada do reconhecimento de entidades.


Crie uma nova função chamada `EntityRecognitionExample()`, que assuma o cliente que criou anteriormente, chame a respetiva função `RecognizeEntities()` e itere os resultados. O objeto `Response<IReadOnlyCollection<CategorizedEntity>>` devolvido incluirá a lista de entidades detetadas. Caso tenha ocorrido um erro, lançará uma `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Length: 7,      Score: 0.92

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Length: 9,      Score: 0.80
```

## <a name="detect-personal-information"></a>Detetar informações pessoais

Crie uma nova função chamada `EntityPIIExample()`, que assuma o cliente que criou anteriormente, chame a respetiva função `RecognizePiiEntities()` e itere os resultados. Tal como acontece na função anterior, o objeto `Response<IReadOnlyCollection<CategorizedEntity>>` devolvido incluirá a lista de entidades detetadas. Caso tenha ocorrido um erro, lançará uma `RequestFailedException`.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category:
                Length: 11,     Score: 0.85
```


## <a name="entity-linking"></a>Associação de entidades

Crie uma nova função chamada `EntityLinkingExample()`, que assuma o cliente que criou anteriormente, chame a respetiva função `RecognizeLinkedEntities()` e itere os resultados. O objeto `Response<IReadOnlyCollection<LinkedEntity>>` devolvido representa a lista de entidades detetadas. Caso tenha ocorrido um erro, lançará uma `RequestFailedException`. Uma vez que as entidades associadas são identificadas exclusivamente, as ocorrências da mesma entidade são agrupadas num objeto `LinkedEntity` como uma lista de objetos `LinkedEntityMatch`.

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
            Console.WriteLine($"\t\tLength: {match.GraphemeLength},\tScore: {match.ConfidenceScore:F2}\n");
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
                Length: 11,     Score: 0.78

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Length: 10,     Score: 0.55

                Text: Gates
                Length: 5,      Score: 0.55

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Length: 10,     Score: 0.53

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Length: 9,      Score: 0.47

                Text: Microsoft
                Length: 9,      Score: 0.47

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Length: 7,      Score: 0.25

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Length: 5,      Score: 0.28
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a associação de entidades está incluída na resposta do NER.

Crie uma nova função chamada `RecognizeEntitiesExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Itere os resultados. O objeto [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) devolvido incluirá a lista de entidades detetadas em `Entities` se for bem-sucedido e uma `errorMessage` se não for. Para cada entidade detetada, imprima o respetivo Tipo, Subtipo e nome na Wikipédia (se existirem), bem como as localizações no texto original.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extração de expressões-chave

#### <a name="version-30-preview"></a>[Versão 3.0-pré-visualização](#tab/version-3)

Crie uma nova função chamada `KeyPhraseExtractionExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função `ExtractKeyPhrases()`. O objeto `<Response<IReadOnlyCollection<string>>` devolvido incluirá a lista de expressões-chave detetadas. Caso tenha ocorrido um erro, lançará uma `RequestFailedException`.

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

Crie uma nova função chamada `KeyPhraseExtractionExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). O resultado incluirá a lista de expressões-chave detetadas em `KeyPhrases` se for bem-sucedido e um `errorMessage` se não for. Imprima as expressões-chave detetadas.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

---
