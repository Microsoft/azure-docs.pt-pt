---
title: 'Início rápido: Obtenha informações de imagem com o SDK de pesquisa Visual do Bing paraC#'
titleSuffix: Azure Cognitive Services
description: Aprenda a carregar uma imagem usando o SDK de pesquisa Visual do Bing e obter conhecimentos sobre ele.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/16/2018
ms.author: v-gedod
ms.openlocfilehash: a7fc4686ec31b4c3a23d3e0ff72e5e2a270015b0
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532104"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Início rápido: Obtenha informações de imagem com o SDK de pesquisa Visual do Bing paraC#

Utilize este guia de introdução para iniciar a obtenção de informações de imagem do serviço de pesquisa Visual do Bing, utilizando o C# SDK. Embora a pesquisa Visual do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
* O pacote de pesquisa Visual do NuGet. 
    - No Explorador de Soluções do Visual Studio, clique com o botão direito do rato no seu projeto e selecione `Manage NuGet Packages` no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.VisualSearch`. Instalar os pacotes NuGet também instala o seguinte:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. No Visual Studio, crie um novo projeto. Em seguida, adicione as seguintes diretivas.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Criar uma instância do cliente com a sua chave de subscrição.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Enviar um pedido de pesquisa 

1. Criar uma `FileStream` para as imagens (neste caso `TestImages/image.jpg`). Em seguida, utilizar o cliente para enviar um pedido de pesquisa com `client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Analise os resultados da consulta anterior:

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-visual-search-single-page-app.md)
