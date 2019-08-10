---
title: Localizar imagens semelhantes de pesquisas anteriores usando o ImageInsightsToken-Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
description: Use o SDK do Pesquisa Visual do Bing para obter URLs de imagens especificadas por ImageInsightsToken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rosh
ms.openlocfilehash: 5f4faa290fe4ed02ab1ed75d23755af5dc20f215
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880616"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Localizar imagens semelhantes de pesquisas anteriores usando o ImageInsightsToken

O SDK do Pesquisa Visual permite que você localize imagens online de pesquisas anteriores que retornam um `ImageInsightsToken`. Esse aplicativo obtém um `ImageInsightsToken` e usa o token em uma pesquisa subsequente. Em seguida, ele `ImageInsightsToken` envia o para o Bing e retorna resultados que incluem URLs de pesquisa do Bing e URLs de imagens semelhantes encontradas online.

O código-fonte completo deste tutorial pode ser encontrado com o tratamento de erros e anotações adicionais no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Se você estiver usando o linux/MacOS, poderá executar esse aplicativo usando o [mono](https://www.mono-project.com/).
* Os pacotes Pesquisa Visual e Pesquisa de Imagem do NuGet.
    - No Gerenciador de Soluções no Visual Studio, clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet** no menu. Instale o `Microsoft.Azure.CognitiveServices.Search.CustomSearch` pacote e o `Microsoft.Azure.CognitiveServices.Search.ImageSearch` pacote. A instalação dos pacotes NuGet também instala o seguinte:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Obter o ImageInsightsToken do SDK do Pesquisa de Imagem do Bing

Esse aplicativo usa um `ImageInsightsToken` obtido por meio do [SDK do pesquisa de imagem do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). Em um novo C# aplicativo de console, crie um cliente para chamar a API `ImageSearchClient()`usando. Em seguida `SearchAsync()` , use com sua consulta:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Armazene o primeiro resultado da `imageResults.Value.First()`pesquisa usando e, em seguida, armazene os `ImageInsightsToken`insights da imagem.

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

Isso `ImageInsightsToken` é enviado para pesquisa visual do Bing em uma solicitação.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Adicionar o ImageInsightsToken a uma solicitação de Pesquisa Visual

Especifique o `ImageInsightsToken` para uma solicitação de pesquisa Visual criando um `ImageInfo` objeto do `ImageInsightsToken` contido em respostas de pesquisa visual do Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Usar Pesquisa Visual do Bing para localizar imagens de um ImageInsightsToken

O `VisualSearchRequest` objeto contém informações sobre a imagem em `ImageInfo` a ser pesquisada. O método `VisualSearchMethodAsync()` obtém os resultados. Você não precisa fornecer um binário de imagem, pois a imagem é representada pelo token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterar pelos resultados da Pesquisa Visual

Os resultados da Pesquisa Visual são objetos `ImageTag`. Cada etiqueta contém uma lista de objetos `ImageAction`. Cada `ImageAction` contém um `Data` campo, que é uma lista de valores que dependem do tipo de ação. Você pode iterar os `ImageTag` objetos no `visualSearchResults.Tags`, por exemplo, e obter a `ImageAction` marca dentro dele. O exemplo a seguir imprime os `PagesIncluding` detalhes das ações:

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

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Obter as URLs da imagem real dos tipos de ação requer uma conversão que `ActionType` lê `ImageModuleAction`um as, que `Data` contém um elemento com uma lista de valores. Cada valor é o URL de uma imagem.  O seguinte converte o `PagesIncluding` tipo de ação para `ImageModuleAction` e lê os valores:

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

## <a name="returned-urls"></a>URLs retornadas

O aplicativo completo retorna as seguintes URLs:

|ActionType  |URL  | |
|---------|---------|---------|
|MoreSizes-> WebSearchUrl     |         |
|VisualSearch-> WebSearchUrl     |         |
|ImageById-> WebSearchUrl    |         |
|RelatedSearches-> WebSearchUrl:    |         |
|DocumentLevelSuggestions-> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.Bing.com/CR?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&cid=1BA795A21EAF6A63175699B71FC36B7C&RD=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3A%2F%2Fwww.Bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults-> WebSearchUrl    |  https:\//www.Bing.com/CR?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&cid=1BA795A21EAF6A63175699B71FC36B7C&RD=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3A%2F%2Fwww.Bing.com%2fimages%2fsearch%3Fq%3doutdoor&p=DevEx,5831.1       |

Como mostrado acima, os `TopicResults` tipos `ImageResults` e contêm consultas para imagens relacionadas. O link de URLs para resultados da pesquisa do Bing.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única Pesquisa Visual](tutorial-bing-visual-search-single-page-app.md)
