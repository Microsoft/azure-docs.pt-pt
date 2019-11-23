---
title: 'Quickstart: Get image insights using the SDK for C# - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Learn how to upload an image using the Bing Visual Search SDK and get insights about it.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 3db40f999770823052f07a328d3d8dc8c01ce345
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383583"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Quickstart: Get image insights using the Bing Visual Search SDK for C#

Use this quickstart to begin getting image insights from the Bing Visual Search service, using the C# SDK. While Bing Visual Search has a REST API compatible with most programming languages, the SDK provides an easy way to integrate the service into your applications. The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
* The NuGet Visual Search package. 
    - No Explorador de Soluções do Visual Studio, clique com o botão direito do rato no seu projeto e selecione `Manage NuGet Packages` no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.VisualSearch`. Installing the NuGet packages also installs the following:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. In Visual Studio, create a new project. Then add the following directives.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Instantiate the client with your subscription key.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Send a search request 

1. Create a `FileStream` to your images (in this case `TestImages/image.jpg`). Then use the client to send a search request using `client.Images.VisualSearchMethodAsync()`. 
    
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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Build a single-page web app](tutorial-bing-visual-search-single-page-app.md)
