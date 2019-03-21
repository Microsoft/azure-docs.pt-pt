---
title: 'Início rápido: Utilizar SDK para de pesquisa Bing WebC#'
titleSuffix: Azure Cognitive Services
description: O SDK de Pesquisa na Web do Bing facilita a integração da Pesquisa na Web do Bing na sua aplicação C#. Neste início rápido, irá aprender a instanciar um cliente, a enviar um pedido e a imprimir a resposta.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 3424137b36e4e277a8914ab04cdf7097660930e3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860759"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>Início rápido: Utilizar SDK para de pesquisa Bing WebC#

O SDK de Pesquisa na Web do Bing facilita a integração da Pesquisa na Web do Bing na sua aplicação C#. Neste início rápido, irá aprender a instanciar um cliente, a enviar um pedido e a imprimir a resposta.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão algumas coisas de que irá precisar antes de executar este início rápido:

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) ou
* [Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [C# para o Visual Studio Code](https://visualstudio.microsoft.com/downloads/)
  * [Gestor de Pacotes NuGet](https://github.com/jmrog/vscode-nuget-package-manager)
* [SDK .NET Core](https://www.microsoft.com/net/download)

## <a name="create-a-project-and-install-dependencies"></a>Criar um projeto e instalar dependências

> [!TIP]
> Obter o código mais recente como uma solução do Visual Studio a partir do [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/).

O primeiro passo é criar um novo projeto de consola. Se precisar de ajuda com a configuração de um projeto de consola, veja [Olá, mundo – seu primeiro programa (C# guia de programação)](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program). Para utilizar o SDK de Pesquisa na Web do Bing na sua aplicação, terá de instalar `Microsoft.Azure.CognitiveServices.Search.WebSearch` através do Gestor de Pacotes NuGet.

O [pacote do SDK de Pesquisa na Web](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) também instala:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>Declarar dependências

Abra o projeto no Visual Studio ou Visual Studio Code e importe estas dependências:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
```

## <a name="create-project-scaffolding"></a>Criar a estrutura do projeto

Quando criou o seu novo projeto de consola, deverá ter sido criado um espaço de nomes e uma classe para a sua aplicação. Seu programa deve ter um aspeto semelhante a este exemplo:

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

Nas secções seguintes, vamos criar o nosso exemplo de aplicação nesta classe.

## <a name="construct-a-request"></a>Construir um pedido

Este código constrói a consulta de pesquisa.

```csharp
public static void WebResults(WebSearchAPI client)
{
    try
    {
        var webData = client.Web.Search(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>Processar a resposta

Em seguida, vamos adicionar algum código para analisar a resposta e imprimir os resultados. O `Name` e `Url` da primeira página Web, imagem, artigo de notícias e vídeo são impressos se estiverem presentes no objeto de resposta.

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Declarar o método principal

Nesta aplicação, o método principal inclui código que instancia o cliente, valida `subscriptionKey` e chama `WebResults`. Certifique-se de que introduz uma chave de subscrição válida para a sua conta do Azure antes de continuar.

```csharp
static void Main(string[] args)
{
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>Executar a aplicação

Vamos executar a aplicação!

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>Definir funções e filtrar resultados

Agora que fez a sua primeira chamada à API de Pesquisa na Web do Bing, vamos ver algumas funções que destacam a funcionalidade do SDK para refinar consultas e filtrar resultados. Pode adicionar cada função à aplicação C# criada na secção anterior.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Limitar o número de resultados devolvidos pelo Bing

Este exemplo utiliza os parâmetros `count` e `offset` para limitar o número de resultados devolvidos para "Melhor restaurantes em Seattle". O `Name` e `Url` para o primeiro resultado são impressos.

1. Adicione este código ao seu projeto de consola:

    ```csharp
    public static void WebResultsWithCountAndOffset(WebSearchAPI client)
    {
        try
        {
            var webData = client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any Web data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Adicione `WebResultsWithCountAndOffset` a `main`:

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Execute a aplicação.

### <a name="filter-for-news"></a>Filtrar notícias

Este exemplo utiliza o parâmetro `response_filter` para filtrar os resultados da pesquisa. Os resultados da pesquisa devolvidos estão limitados a artigos de notícias para "Microsoft". O `Name` e `Url` para o primeiro resultado são impressos.

1. Adicione este código ao seu projeto de consola:

    ```csharp
    public static void WebSearchWithResponseFilter(WebSearchAPI client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Adicione `WebResultsWithCountAndOffset` a `main`:

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Execute a aplicação.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Utilizar a pesquisa segura, a contagem de respostas e o filtro de promover

Este exemplo utiliza os parâmetros `answer_count`, `promote` e `safe_search` para filtrar os resultados da pesquisa para "Vídeos de Música". O `Name` e `ContentUrl` para o primeiro resultado são apresentados.

1. Adicione este código ao seu projeto de consola:

    ```csharp
    public static void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchAPI client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Adicione `WebResultsWithCountAndOffset` a `main`:

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Execute a aplicação.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado de fazer o que quer neste projeto, não se esqueça de remover a sua chave de subscrição do código da aplicação.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Exemplos do SDK Node.js dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
