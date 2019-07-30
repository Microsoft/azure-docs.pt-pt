---
title: 'Início rápido: Chamar o serviço de Análise de Texto usando o SDK do Azure para .NET eC#'
titleSuffix: Azure Cognitive Services
description: Informações e exemplos de código para ajudá-lo a começar a usar C#o serviço de análise de texto e o.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: assafi
ms.openlocfilehash: 5e5246ab46d00e0b62b3dee321c6cea893e6a5d3
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619626"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Início rápido: Usar o SDK do .NET C# e chamar o serviço de análise de texto
<a name="HOLTop"></a>

Este guia de início rápido ajuda você a começar a usar o C# SDK do Azure para .net e a analisar a linguagem. Embora a API REST do [análise de texto](//go.microsoft.com/fwlink/?LinkID=759711) seja compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos.

> [!NOTE]
> O código de demonstração neste artigo usa os métodos síncronos do SDK do .NET Análise de Texto para simplificar. No entanto, para cenários de produção, é recomendável usar os métodos assíncronos em lote em seus próprios aplicativos para mantê-los escalonáveis e responsivos. Por exemplo, você pode usar `SentimentBatchAsync` em vez `Sentiment`de.
>
> Uma versão assíncrona em lote deste exemplo pode ser encontrada no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Para obter detalhes técnicos, consulte a referência do SDK para .NET [análise de texto](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do Visual Studio 2017 ou posterior
* O SDK do Análise de Texto [para .net](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Você também precisa do [ponto de extremidade e da chave de acesso](../How-tos/text-analytics-how-to-access-key.md) que foi gerada para você durante a inscrição.

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Criar a solução do Visual Studio e instalar o SDK

1. Crie um novo projeto de aplicativo de console (.NET Core). [Acesse o Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Clique com o botão direito do mouse na solução e selecione **gerenciar pacotes NuGet para solução**.
1. Selecione o separador **Procurar**. Pesquise por **Microsoft. Azure. cognitivaservices. Language. textanalytics**.

## <a name="authenticate-your-credentials"></a>Autenticar suas credenciais

1. Adicione as instruções `using` a seguir ao arquivo de classe principal (que é Program.cs por padrão).

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. Crie uma nova `ApiKeyServiceClientCredentials` classe para armazenar as credenciais e adicioná-las para cada solicitação.

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Atualize a `Program` classe. Adicione um membro constante para sua chave de API de Análise de Texto e outro para o ponto de extremidade de serviço. Lembre-se de usar o local correto do Azure para seu recurso de Análise de Texto.

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```

> [!Tip]
> Para aumentar a segurança dos segredos em sistemas de produção, recomendamos que você use [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Criar um cliente Análise de Texto

`Main` Na função do seu projeto, chame o método de exemplo que você deseja invocar. Passe os `Endpoint` parâmetros `ApiKey` e que você definiu.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

As seções a seguir descrevem como chamar cada recurso de serviço.

## <a name="perform-sentiment-analysis"></a>Executar análise de sentimentos

1. Crie uma nova função `SentimentAnalysisExample()` que usa o cliente que você criou anteriormente.
2. Na mesma função, chame `client.Sentiment()` e obtenha o resultado. O resultado conterá o sentimentos `Score` se for bem-sucedido e um `errorMessage` se não. Uma pontuação que está perto de 0 indica uma observação negativa, enquanto uma pontuação mais próxima de 1 indica um sentimentos positivo.

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Executar detecção de idioma

1. Crie uma nova função `DetectLanguageExample()` que usa o cliente que você criou anteriormente.
2. Na mesma função, chame `client.DetectLanguage()` e obtenha o resultado. O resultado conterá a lista de idiomas detectados `DetectedLanguages` em se bem-sucedido e um `errorMessage` se não. Em seguida, imprima o primeiro idioma retornado.

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o `countryHint` parâmetro para especificar um código de país de duas letras. Por padrão, a API está usando o "US" como o countryHint padrão, para remover esse comportamento, você pode redefinir esse parâmetro definindo esse valor como cadeia `countryHint = ""` de caracteres vazia.

### <a name="output"></a>Output

```console
Language: English
```

## <a name="perform-entity-recognition"></a>Executar reconhecimento de entidade

1. Crie uma nova função `RecognizeEntitiesExample()` que usa o cliente que você criou anteriormente.
2. Na mesma função, chame `client.Entities()` e obtenha o resultado. Em seguida, itere pelos resultados. O resultado conterá a lista de entidades detectadas `Entities` em se bem-sucedido e um `errorMessage` se não. Para cada entidade detectada, imprima seu tipo, subtipo, nome da Wikipédia (se existir), bem como os locais no texto original.

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

### <a name="output"></a>Output

```console
Entities:
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0,      Length: 9,      Score: 1.000
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25,     Length: 10,     Score: 1.000
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40,     Length: 10,     Score: 0.999
    Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54,     Length: 7,      Score: 0.800
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54,     Length: 13,     Score: 0.800
    Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89,     Length: 5,      Score: 0.800
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="perform-key-phrase-extraction"></a>Executar extração de frase-chave

1. Crie uma nova função `KeyPhraseExtractionExample()` que usa o cliente que você criou anteriormente.
2. Na mesma função, chame `client.KeyPhrases()` e obtenha o resultado. O resultado conterá a lista de frases-chave detectadas em `KeyPhrases` se for bem-sucedido e um `errorMessage` se não. Em seguida, imprima as frases-chave detectadas.

    ```csharp
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
    ```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Descrição Geral da Análise de Texto](../overview.md)
* [Perguntas Mais Frequentes (FAQ)](../text-analytics-resource-faq.md)
