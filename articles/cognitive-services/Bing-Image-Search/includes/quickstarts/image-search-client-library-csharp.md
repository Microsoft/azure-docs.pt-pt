---
title: Bing Image Search C# biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: 122e44da7bbf4229f932eefdae4c70dc49f43bfe
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371290"
---
Utilize este quickstart para fazer a sua primeira pesquisa de imagem utilizando a biblioteca do cliente Bing Image Search. 

A biblioteca de pesquisa de clientes é um invólucro para a API REST e contém as mesmas funcionalidades. 

Você vai criar uma aplicação C# envia uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe o URL da primeira imagem devolvida.

O código fonte deste exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) com processamento de erros e anotações de código adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver a usar o Windows, qualquer edição do [Visual Studio 2017 ou mais tarde](https://visualstudio.microsoft.com/vs/whatsnew/)
* Se estiver a utilizar o macOS ou o Linux, [VS Code](https://code.visualstudio.com) com [.NET Core instalado](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
* [Uma subscrição gratuita do Azure](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

Consulte também [preços dos serviços cognitivos - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-a-console-project"></a>Criar um projeto de consola

Primeiro, crie uma nova aplicação de consola C#.

## <a name="create-a-console-project"></a>Criar um projeto de consola

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Crie uma nova solução de consola chamada *BingImageSearch* no Visual Studio.
    
1. Adicione o [pacote De Pesquisa de Imagem Cognitiva NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch)
    1. Clique com o botão direito no seu projeto no **Solution Explorer.**
    1. **Selecione Gerir pacotes nuget**.
    1. Procure e selecione *Microsoft.Azure.CognitiveServices.Search.ImageSearch*e, em seguida, instale o pacote.
    
# <a name="vs-code"></a>[Código VS](#tab/vscode)

1. Abra a janela do terminal no Código VS.
1. Crie um novo projeto de consola chamado *BingImageSearch* introduzindo o seguinte código na janela do terminal:
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. Abra a pasta *BingImageSearch* no Código VS.
1. Adicione o [pacote NuGet de pesquisa de imagem cognitiva,](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) introduzindo o seguinte código na janela do terminal:

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>Inicializar a aplicação


1. Substitua todas as `using` declarações em *Program.cs* pelo seguinte código:

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. No `Main` método do seu projeto, crie variáveis para a sua chave de subscrição válida, os resultados de imagem a serem devolvidos por Bing, e um termo de pesquisa. Em seguida, inicie o cliente de pesquisa de imagens com a chave.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>Enviar uma consulta de pesquisa com o cliente
    
Ainda no `Main` método, utilize o cliente para pesquisar com um texto de consulta:
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analisar e ver o primeiro resultado da imagem

Analise os resultados da imagem devolvidos na resposta. 

Se a resposta contiver resultados de pesquisa, guarde o primeiro resultado e imprima alguns dos seus detalhes.

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Bing Image Search single-page app tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) (Tutorial de aplicação de página única da Pesquisa de Imagens do Bing)

## <a name="see-also"></a>Ver também

* [O que é a Pesquisa de Imagens do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimentar uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Amostras de .NET para o SDK dos Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Documentação dos Serviços Cognitivos da Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) (Referência da API de Pesquisa de Imagens do Bing)
