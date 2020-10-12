---
title: Encontre imagens semelhantes de pesquisas anteriores usando tokens de insights de imagem e a API de Pesquisa Visual Bing
titleSuffix: Azure Cognitive Services
description: Utilize a biblioteca de clientes Bing Visual Search para obter URLs de imagens de pesquisas anteriores.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: c03800696897dc6cbb4cd793879e734366829b7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88925118"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Tutorial: Encontre imagens semelhantes de pesquisas anteriores usando um token de insights de imagem

A biblioteca de clientes Visual Search permite-lhe encontrar imagens online de pesquisas anteriores que retornem um `ImageInsightsToken` . Esta aplicação recebe um `ImageInsightsToken` e usa o símbolo numa pesquisa subsequente. Em seguida, envia o `ImageInsightsToken` bing e devolve resultados que incluem URLs de pesquisa Bing e URLs de imagens semelhantes encontradas online.

O código fonte completo para este tutorial pode ser encontrado com manipulação adicional de erros e anotações no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Se estiver a utilizar o Linux/MacOS, pode executar esta aplicação utilizando [o Mono.](https://www.mono-project.com/)
* Os pacotes de pesquisa visual e de procura de imagem NuGet.
    - A partir do Solution Explorer no Estúdio Visual, clique à direita no seu projeto e selecione **Gerir pacotes NuGet** no menu. Instale a `Microsoft.Azure.CognitiveServices.Search.CustomSearch` embalagem e a `Microsoft.Azure.CognitiveServices.Search.ImageSearch` embalagem. A instalação dos pacotes NuGet também instala o seguinte:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>Obtenha o ImageInsightsToken da biblioteca de clientes Bing Image Search

Esta aplicação utiliza uma `ImageInsightsToken` ferramenta obtida através da biblioteca do cliente [Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). Numa nova aplicação de consola C#, crie um cliente para ligar para a API utilizando `ImageSearchClient()` . Em `SearchAsync()` seguida, use com a sua consulta:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Guarde o primeiro resultado de pesquisa utilizando `imageResults.Value.First()` , e, em seguida, guarde o insight de imagem `ImageInsightsToken` .

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

Isto `ImageInsightsToken` é enviado para bing Visual Search num pedido.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Adicione o ImageInsightsToken a um pedido de Pesquisa Visual

Especifique o `ImageInsightsToken` pedido de procura visual criando um objeto a partir do contido em `ImageInfo` `ImageInsightsToken` respostas da Pesquisa Visual de Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Use bing Visual Search para encontrar imagens de um ImageInsightsToken

O `VisualSearchRequest` objeto contém informações sobre a imagem a `ImageInfo` ser revistada. O método `VisualSearchMethodAsync()` obtém os resultados. Não é preciso fornecer uma imagem binária, pois a imagem é representada pelo símbolo.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterar através dos resultados da Pesquisa Visual

Os resultados da Pesquisa Visual são objetos `ImageTag`. Cada etiqueta contém uma lista de objetos `ImageAction`. Cada `ImageAction` um contém um `Data` campo, que é uma lista de valores que dependem do tipo de ação. Pode iterar através dos `ImageTag` objetos `visualSearchResults.Tags` em , por exemplo, e colocar a `ImageAction` etiqueta dentro dela. A amostra abaixo imprime os detalhes das `PagesIncluding` ações:

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

### <a name="pagesincluding-actiontypes"></a>Páginas Incluindo AçãoTypes

Obter os URLs de imagem reais dos tipos de ação requer um elenco que leia um `ActionType` como , que contém um elemento com uma lista de `ImageModuleAction` `Data` valores. Cada valor é o URL de uma imagem.  O seguinte lança o `PagesIncluding` tipo de ação `ImageModuleAction` e lê os valores:

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

|ActionType  |URL  |
|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelacionadosSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TópicoResults -> WebSearchUrl    | https: \/ /www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https: \/ /www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DCVESDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Como mostrado acima, os `TopicResults` e os tipos contêm consultas para `ImageResults` imagens relacionadas. Os URLs ligam-se aos resultados da pesquisa de Bing.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de pesquisa visual de uma página](tutorial-bing-visual-search-single-page-app.md)
