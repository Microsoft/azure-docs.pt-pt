---
title: 'Início rápido: Efetua uma pesquisa de notícias - SDK de pesquisa de notícias do Bing paraC#'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para pesquisar notícias com o SDK de pesquisa de notícias do Bing para Python e processar a resposta.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 90883b136a1c77f12dc4aa81bc18bc43c1e9ca0b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538583"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-c"></a>Início rápido: Efetua uma pesquisa de notícias com o SDK de pesquisa de notícias do Bing paraC#

Utilize este guia de introdução para começar a pesquisa de notícias com o SDK de pesquisa de notícias do Bing para C#. Embora a pesquisa do Bing notícias tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet.
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).

* O [pacote NuGet do SDK de pesquisa do Bing notícias](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0). Também instalar este pacote instala o seguinte:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Para configurar a aplicação de consola através do SDK de Pesquisa de Notícias do Bing, aceda à opção `Manage NuGet Packages` a partir do Explorador de Soluções no Visual Studio.  Adicione o pacote `Microsoft.Azure.CognitiveServices.Search.NewsSearch`.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. criar um novo C# consola de solução no Visual Studio. Em seguida, adicione o seguinte para o ficheiro de código principal.
    
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    ```

2. Criar uma variável para a sua API da chave, um termo de pesquisa e, em seguida, criar uma instância do cliente de pesquisa de notícias com ele.

    ```csharp
    var key = "YOUR-ACCESS-KEY";
    var searchTerm = "Quantum Computing";
    var client = new NewsSearchClient(new ApiKeyServiceClientCredentials(key));
    ```

## <a name="send-a-request-and-parse-the-result"></a>Enviar um pedido e analisar o resultado

1. Utilize o cliente para enviar um pedido de pesquisa para o serviço de pesquisa do Bing notícias:
    ```csharp
    var newsResults = client.News.SearchAsync(query: searchTerm, market: "en-us", count: 10).Result;
    ```

2. Se foram devolvidos resultados, analisá-las:

    ```csharp
    if (newsResults.Value.Count > 0)
    {
        var firstNewsResult = newsResults.Value[0];
    
        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
        Console.WriteLine($"News result count: {newsResults.Value.Count}");
        Console.WriteLine($"First news name: {firstNewsResult.Name}");
        Console.WriteLine($"First news url: {firstNewsResult.Url}");
        Console.WriteLine($"First news description: {firstNewsResult.Description}");
        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
        Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
    }
    
    else
    {
        Console.WriteLine("Couldn't find news results!");
    }
    Console.WriteLine("Enter any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
[Criar uma aplicação Web de página única](tutorial-bing-news-search-single-page-app.md)
