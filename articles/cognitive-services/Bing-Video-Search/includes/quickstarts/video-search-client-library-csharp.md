---
title: Bing Video Search C# biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d50a8e2c9d0263616b25e25958be6a6f0fb7fe1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88929279"
---
Use este quickstart para começar a procurar notícias com a biblioteca de clientes Bing Video Search para C#. Enquanto a Bing Video Search tem uma API REST compatível com a maioria das linguagens de programação, a biblioteca do cliente fornece uma maneira fácil de integrar o serviço nas suas aplicações. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) com anotações adicionais e características.

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/).
* O quadro Json.NET, disponível [como um pacote NuGet.](https://www.nuget.org/packages/Newtonsoft.Json/)

Para adicionar a biblioteca de clientes Bing Video Search ao seu projeto, **selecione Gerir pacotes NuGet** do **Solution Explorer** em Visual Studio. Adicione o pacote `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

A instalação do [pacote [NuGet Video Search SDK]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) também instala as seguintes dependências:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie uma nova solução de consola C# no Visual Studio. Em seguida, adicione o seguinte no ficheiro de código principal.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Instantânear o cliente criando um novo `ApiKeyServiceClientCredentials` objeto com a sua chave de subscrição, e chamando o construtor.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Envie um pedido de pesquisa e processe os resultados

1. Use o cliente para enviar um pedido de pesquisa. Utilize "SwiftKey" para a consulta de pesquisa.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Se os resultados forem devolvidos, obtenha o primeiro com `videoResults.Value[0]` . Em seguida, imprima o ID, título e url do vídeo.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de uma página única](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Veja também 

* [O que é a API de Pesquisa de Vídeos do Bing?](../../overview.md)
* [Serviços cognitivos .NET SDK amostras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
