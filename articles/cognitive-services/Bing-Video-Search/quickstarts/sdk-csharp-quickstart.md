---
title: 'Início rápido: pesquisar vídeos usando o SDK para C# -pesquisa de vídeo do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para enviar solicitações de pesquisa de vídeo C#usando o SDK do pesquisa de vídeo do Bing para o.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: d29aef614b8308dfeba8da7925bd2880c25fe72d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383749"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Início rápido: executar uma pesquisa de vídeo com o SDK do Pesquisa de Vídeo do Bing paraC#

Use este guia de início rápido para começar a procurar notícias com o C#SDK do pesquisa de vídeo do Bing para o. Embora Pesquisa de Vídeo do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) com anotações adicionais e recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/).
* A estrutura Json.NET, disponível [como um pacote NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Para adicionar o SDK do Pesquisa de Vídeo do Bing ao seu projeto, selecione **gerenciar pacotes NuGet** do **Gerenciador de soluções** no Visual Studio. Adicione o pacote `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Instalar o [[pacote SDK do NuGet pesquisa de vídeo]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) também instala as seguintes dependências:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie uma nova C# solução de console no Visual Studio. Em seguida, adicione o seguinte ao arquivo de código principal.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Crie uma instância do cliente criando um novo objeto `ApiKeyServiceClientCredentials` com sua chave de assinatura e chamando o construtor.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Enviar uma solicitação de pesquisa e processar os resultados

1. Use o cliente para enviar uma solicitação de pesquisa. Use "SwiftKey" para a consulta de pesquisa.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Se algum resultado for retornado, obtenha o primeiro com `videoResults.Value[0]`. Em seguida, imprima a ID, o título e a URL do vídeo.

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
> [Criar um aplicativo Web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

* [O que é o API de Pesquisa de Vídeo do Bing?](../overview.md)
* [Exemplos de SDK .NET nos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
