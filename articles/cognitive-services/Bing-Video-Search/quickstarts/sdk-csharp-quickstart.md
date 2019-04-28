---
title: 'Início rápido: Procurar vídeos com o SDK de pesquisa de vídeo Bing paraC#'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar pedidos de pesquisa de vídeos com o SDK de pesquisa de vídeos do Bing para C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: f94db7a29827a80307f4a081d93744c5edc59edb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473285"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Início rápido: Efetua uma pesquisa de vídeo com o SDK de pesquisa de vídeos do Bing paraC#

Utilize este guia de introdução para começar a pesquisa de notícias com o SDK de pesquisa de vídeo Bing para C#. Embora a pesquisa de vídeos do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch). Ela contém mais anotações e funcionalidades.

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
* A estrutura de Json.NET [como um pacote NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Para adicionar o SDK de pesquisa de vídeos do Bing ao seu projeto, navegue para o `Manage NuGet Packages` opção a partir do Explorador de soluções no Visual Studio.  Adicione o pacote `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Instalar o [[pacote do SDK de pesquisa de vídeo do NuGet]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) também instala as seguintes dependências:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. criar um novo C# consola de solução no Visual Studio. Em seguida, adicione o seguinte para o ficheiro de código principal.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Criar uma instância do cliente ao criar um novo `ApiKeyServiceClientCredentials` de objeto com a sua chave de subscrição e chamar o construtor.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Enviar um pedido de pesquisa e processar os resultados

1. Utilize o cliente para enviar uma solicitação de pesquisa. Utilize "SwiftKey" para a consulta de pesquisa.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Se foram devolvidos resultados, obtenha primeiro aquele com `videoResults.Value[0]`. Em seguida, imprima ID, title e url do vídeo.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

* [O que é a API de pesquisa de vídeos do Bing?](../overview.md)
* [Exemplos de SDK .NET nos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
