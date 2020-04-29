---
title: Encontre imagens semelhantes de pesquisas anteriores usando fichas de imagem e a API de Pesquisa Visual Bing
titleSuffix: Azure Cognitive Services
description: Utilize a biblioteca de clientes bing Visual Search para obter URLs de imagens de pesquisas anteriores.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.openlocfilehash: ad24a8a194a11c3fd5f7f77ea8c52197d5438edc
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80477916"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Tutorial: Encontre imagens semelhantes de pesquisas anteriores usando um símbolo de imagem

A biblioteca de clientes da Procura Visual permite-lhe encontrar `ImageInsightsToken`imagens online de pesquisas anteriores que devolvem um . Esta aplicação `ImageInsightsToken` recebe um e utiliza o símbolo numa pesquisa subsequente. Em seguida, `ImageInsightsToken` envia o bing e devolve resultados que incluem URLs de Pesquisa bing e URLs de imagens semelhantes encontradas online.

O código fonte completo para este tutorial pode ser encontrado com manipulação adicional de erros e anotações no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019.](https://www.visualstudio.com/downloads/)
* Se estiver a utilizar o Linux/MacOS, pode executar esta aplicação utilizando [o Mono](https://www.mono-project.com/).
* Os pacotes NuGet Visual Search and Image Search.
    - Do Solution Explorer no Visual Studio, clique no seu projeto e selecione **Gerir pacotes NuGet** a partir do menu. Instale `Microsoft.Azure.CognitiveServices.Search.CustomSearch` a embalagem `Microsoft.Azure.CognitiveServices.Search.ImageSearch` e a embalagem. A instalação dos pacotes NuGet também instala o seguinte:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>Obtenha o ImageInsightsToken da biblioteca de clientes bing Image Search

Esta aplicação `ImageInsightsToken` utiliza um obtido através da biblioteca de [clientes Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). Numa nova aplicação de consola C#, crie `ImageSearchClient()`um cliente para ligar para a API usando . Em `SearchAsync()` seguida, use com a sua consulta:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Guarde o primeiro `imageResults.Value.First()`resultado de pesquisa utilizando `ImageInsightsToken`, e depois guarde os insights da imagem .

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Isto `ImageInsightsToken` é enviado para a Bing Visual Search num pedido.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Adicione o ImageInsightsToken a um pedido de Pesquisa Visual

Especifique o `ImageInsightsToken` pedido de `ImageInfo` procura `ImageInsightsToken` visual criando um objeto a partir do contido nas respostas da Bing Visual Search.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Use bing visual search para encontrar imagens de um ImageInsightsToken

O `VisualSearchRequest` objeto contém informações `ImageInfo` sobre a imagem para ser revistada. O método `VisualSearchMethodAsync()` obtém os resultados. Não é preciso fornecer uma imagem binária, já que a imagem é representada pelo símbolo.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterado através dos resultados da Pesquisa Visual

Os resultados da Pesquisa Visual são objetos `ImageTag`. Cada etiqueta contém uma lista de objetos `ImageAction`. Cada `ImageAction` um `Data` contém um campo, que é uma lista de valores que dependem do tipo de ação. Pode iterar através `ImageTag` dos `visualSearchResults.Tags`objetos, por `ImageAction` exemplo, e obter a etiqueta dentro dela. A amostra abaixo imprime `PagesIncluding` os detalhes das ações:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>Páginas incluindo tipos de ação

Obter os URLs de imagem reais de tipos `ActionType` `ImageModuleAction`de ação `Data` requer um elenco que lê um as , que contém um elemento com uma lista de valores. Cada valor é o URL de uma imagem.  O seguinte lança `PagesIncluding` o `ImageModuleAction` tipo de ação e lê os valores:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Para obter mais informações sobre estes tipos de dados, veja [Imagens - Pesquisa Visual](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>URLs devolvidos

A aplicação completa devolve os seguintes URLs:

|ActionType  |do IdP  | |
|---------|---------|---------|
|MaisTamanhos -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|Pesquisas Relacionadas -> WebSearchUrl:    |         |
|Sugestões de Documentlevel -> WebSearchUrl:     |         |
|TópicoSResultados -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kz q1Geh7RucVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fdiscover%2fcanadian%2brocky&p=DevEx.5823.1       |
|Resultados de imagem -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSD RE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx.5831.1       |

Como mostrado acima, `TopicResults` `ImageResults` os tipos e tipos contêm consultas para imagens relacionadas. Os URLs ligam-se aos resultados da pesquisa do Bing.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de pesquisa visual de uma página](tutorial-bing-visual-search-single-page-app.md)
