---
title: 'Início rápido: Chamar seu ponto de extremidade de C# pesquisa personalizada do Bing usando o SDK | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para começar a solicitar resultados de pesquisa de C# sua instância de pesquisa personalizada do Bing usando o SDK.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: scottwhi
ms.openlocfilehash: c7ac6d051c8333a6329a3c2ed238d78fb9da4a30
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565720"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Início rápido: Chamar seu ponto de extremidade de C# pesquisa personalizada do Bing usando o SDK 

Use este guia de início rápido para começar a solicitar resultados de pesquisa de sua C# instância do pesquisa personalizada do Bing, usando o SDK. Embora Pesquisa Personalizada do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK do Pesquisa Personalizada do Bing fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de Pesquisa Personalizada do Bing. Consulte [início rápido: Crie sua primeira instância](quick-start.md) de pesquisa personalizada do Bing para obter mais informações.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Qualquer edição do [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/)
- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
- O pacote NuGet [pesquisa personalizada do Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) . 
    - Em **Gerenciador de soluções** no Visual Studio, clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet** no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Quando instala o pacote de Pesquisa Personalizada do NuGet também instala as assemblagens seguintes:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo C# aplicativo de console no Visual Studio. Em seguida, adicione os seguintes pacotes ao seu projeto.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. No método principal do seu aplicativo, crie uma instância do cliente de pesquisa com sua chave de API.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Enviar a solicitação de pesquisa e receber uma resposta
    
1. Envie uma consulta de pesquisa usando o método `SearchAsync()` do cliente e salve a resposta. Certifique-se de substituir `YOUR-CUSTOM-CONFIG-ID` seu pela ID de configuração da instância (você pode encontrar a ID no [portal de pesquisa personalizada do Bing](https://www.customsearch.ai/)). Este exemplo pesquisa "Xbox".

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. O método `SearchAsync()`devolve um objeto `WebData`. Use o objeto para iterar por `WebPages` meio de quaisquer que foram encontrados. Este código localiza o primeiro resultado de página Web, imprime-a `Name` e `URL`.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
