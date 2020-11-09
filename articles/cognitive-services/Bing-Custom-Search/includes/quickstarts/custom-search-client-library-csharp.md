---
title: Bing Custom Search C# biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 4698649eace23e8428dffb9801294beb22ac126a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371913"
---
Começa com a biblioteca de clientes Bing Custom Search para C#. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. A API de Pesquisa Personalizada Bing permite-lhe criar experiências de pesquisa personalizadas e sem anúncios para tópicos que lhe interessam. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

Utilize a biblioteca de clientes Bing Custom Search para C# para:
* Encontre os resultados da pesquisa na web, a partir da sua instância de Pesquisa Personalizada Bing.

[Documentação de referência](/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Pré-requisitos

- Um caso de pesquisa personalizada Bing. Consulte [Quickstart: Crie a sua primeira instância de pesquisa personalizada Bing](../../quick-start.md) para obter mais informações.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Qualquer edição do [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/)
- Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
- O pacote [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)  NuGet. 
    - A partir do **Solution Explorer** no Visual Studio, clique com o botão direito do seu projeto e selecione **Gerir pacotes NuGet** a partir do menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Quando instala o pacote de Pesquisa Personalizada do NuGet também instala as assemblagens seguintes:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie uma nova aplicação de consola C# no Visual Studio. Em seguida, adicione os seguintes pacotes ao seu projeto.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. No método principal da sua aplicação, instantânea o cliente de pesquisa com a sua chave API.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Envie o pedido de pesquisa e receba uma resposta
    
1. Envie uma consulta de pesquisa usando o método do seu cliente `SearchAsync()` e guarde a resposta. Certifique-se de que substitui `YOUR-CUSTOM-CONFIG-ID` o seu pelo ID de configuração do seu caso (pode encontrar o ID no [portal Bing Custom Search).](https://www.customsearch.ai/) Este exemplo procura por "Xbox".

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. O método `SearchAsync()`devolve um objeto `WebData`. Use o objeto para iterar através de qualquer `WebPages` que tenha sido encontrado. Este código localiza o primeiro resultado de página Web, imprime-a `Name` e `URL`.

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
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa um aplicativo web de pesquisa personalizada](../../tutorials/custom-search-web-page.md)