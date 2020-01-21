---
title: 'Início rápido: biblioteca de cliente do C# análise de texto V3 para | Microsoft Docs'
description: Introdução à biblioteca de cliente do Análise de Texto V3 paraC#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2cf9a006e1f6f1edb996aa9beaf8934a14af29df
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281189"
---
<a name="HOLTop"></a>

[Documentação de referência](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [do NuGet ( do pacote)de](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [amostras biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

> [!NOTE]
> * Este guia de início rápido usa a versão `3.0-preview` da biblioteca de cliente do Análise de Texto, que inclui uma visualização pública para [análise de sentimento](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) aprimorado e [reconhecimento de entidade nomeada (Ner)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * O código neste artigo usa métodos síncronos e o armazenamento de credenciais não protegidas por motivos de simplicidade. Para cenários de produção, é recomendável usar os métodos assíncronos em lote para desempenho e escalabilidade. Por exemplo, chamar `AnalyzeSentimentAsync()` em vez de `AnalyzeSentiment()`.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-text-analytics-azure-resource"></a>Criar um Análise de Texto recurso do Azure

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Criar um novo aplicativo .NET Core

Usando o IDE do Visual Studio, crie um novo aplicativo de console .NET Core. Isso criará um projeto "Olá, Mundo" com um único C# arquivo de origem: *Program.cs*.

Instale a biblioteca de cliente clicando com o botão direito do mouse na solução na **Gerenciador de soluções** e selecionando **gerenciar pacotes NuGet**. No Gerenciador de pacotes que é aberto, selecione **procurar**, marque **incluir pré-lançamento**e pesquise por `Azure.AI.TextAnalytics`. Clique nele e, em seguida, em **instalar**. Você também pode usar o [console do Gerenciador de pacotes](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Abra o arquivo *Program.cs* e adicione as seguintes diretivas de `using`:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

Na classe de `Program` do aplicativo, crie variáveis para a chave e o ponto de extremidade do recurso.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Substitua o método de `Main` do aplicativo. Você irá definir os métodos chamados aqui mais tarde.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);

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

## <a name="object-model"></a>Modelo de objeto

O cliente Análise de Texto é um objeto `TextAnalyticsClient` que se autentica no Azure usando sua chave e fornece funções para aceitar texto como cadeias de caracteres únicas ou como um lote. Você pode enviar texto para a API de forma síncrona ou assíncrona. O objeto de resposta conterá as informações de análise para cada documento que você enviar. Uma instância opcional de `TextAnalyticsClientOptions` pode ser usada para inicializar o cliente com várias configurações padrão (por exemplo, linguagem padrão ou dica de país).

Exemplos adicionais, incluindo a autenticação do AAD e o uso de configurações padrão do cliente, podem ser encontrados [aqui](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples).

## <a name="code-examples"></a>Exemplos de código

* [Análise de sentimentos](#sentiment-analysis) (visualização pública)
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-public-preview) (visualização pública)
* [Reconhecimento de entidade nomeada – informações pessoais](#named-entity-recognition---personal-information-public-preview) (visualização pública)
* [Vinculação de entidade](#entity-linking)
* [Extração de frases-chave](#key-phrase-extraction)

No método de `main()` do seu programa, chame o método de autenticação para instanciar o cliente.

## <a name="sentiment-analysis-public-preview"></a>Análise de sentimentos (visualização pública)

> [!NOTE]
> O código abaixo é para Análise de Sentimento v3, que está em visualização pública.

Crie uma nova função chamada `SentimentAnalysisExample()` que usa o cliente que você criou anteriormente e chame sua função `AnalyzeSentiment()`. O objeto `Response<AnalyzeSentimentResult>` retornado conterá o rótulo de sentimentos e a pontuação de todo o documento de entrada, bem como uma análise de sentimentos para cada sentença, se for bem-sucedido, e uma `Value.ErrorMessage` se não for.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

## <a name="language-detection"></a>Deteção de idioma

Crie uma nova função chamada `LanguageDetectionExample()` que usa o cliente que você criou anteriormente e chame sua função `DetectLanguage()`. O objeto `Response<DetectLanguageResult>` retornado conterá o idioma detectado em `Value.PrimaryLanguage` se for bem-sucedido e um `Value.ErrorMessage` se não for.

> [!Tip]
> Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o parâmetro `countryHint` para especificar um código de país de duas letras. Por padrão, a API está usando o "US" como o countryHint padrão, para remover esse comportamento, você pode redefinir esse parâmetro definindo esse valor como cadeia de caracteres vazia `countryHint = ""`. Para definir um padrão diferente, defina a propriedade `TextAnalyticsClientOptions.DefaultCountryHint` e passe-a durante a inicialização do cliente.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Saída

```console
Language:
        French, ISO-6391: fr
```

## <a name="named-entity-recognition-public-preview"></a>Reconhecimento de entidade nomeada (visualização pública)

> [!NOTE]
> O código abaixo é para o reconhecimento de entidade nomeada v3, que está em visualização pública.

Crie uma nova função chamada `EntityRecognitionExample()` que usa o cliente que você criou anteriormente, chame sua função `RecognizeEntities()` e itere pelos resultados. O objeto `Response<RecognizeEntitiesResult>` retornado conterá a lista de entidades detectadas em `Value.NamedEntities` se for bem-sucedido e um `Value.ErrorMessage` se não for. Para cada entidade detectada, imprima seu tipo e subtipo, se existir.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Reconhecimento de entidade nomeada – informações pessoais (visualização pública)

> [!NOTE]
> O código abaixo é para detectar informações pessoais usando o reconhecimento de entidade nomeada v3, que está em visualização pública.

Crie uma nova função chamada `EntityPIIExample()` que usa o cliente que você criou anteriormente, chame sua função `RecognizePiiEntities()` e itere pelos resultados. Semelhante à função anterior, o objeto de `Response<RecognizeEntitiesResult>` retornado conterá a lista de entidades detectadas em `Value.NamedEntities` se for bem-sucedido e um `Value.ErrorMessage` se não for.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```

## <a name="entity-linking"></a>Ligação de Entidades

Crie uma nova função chamada `EntityLinkingExample()` que usa o cliente que você criou anteriormente, chame sua função `RecognizeLinkedEntities()` e itere pelos resultados. O objeto `Response<RecognizeLinkedEntitiesResult>` retornado conterá a lista de entidades detectadas em `Value.LinkedEntities` se for bem-sucedido e um `Value.ErrorMessage` se não for. Como as entidades vinculadas são identificadas exclusivamente, as ocorrências da mesma entidade são agrupadas em um objeto `LinkedEntity` como uma lista de objetos `LinkedEntityMatch`.

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
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
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
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

## <a name="key-phrase-extraction"></a>Extração de expressões chave

Crie uma nova função chamada `KeyPhraseExtractionExample()` que usa o cliente que você criou anteriormente e chame sua função `ExtractKeyPhrases()`. O resultado conterá a lista de frases-chave detectadas em `Value.KeyPhrases` se for bem-sucedido e uma `Value.ErrorMessage` se não for. Imprima as frases-chave detectadas.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
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
