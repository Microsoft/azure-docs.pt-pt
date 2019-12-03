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
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: dff96b19f40c2d897b6a018a4c46cec60f8aa201
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689312"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Localizar imagens semelhantes de pesquisas anteriores usando o ImageInsightsToken

O SDK do Pesquisa Visual permite que você encontre imagens online de pesquisas anteriores que retornam um `ImageInsightsToken`. Esse aplicativo obtém um `ImageInsightsToken` e usa o token em uma pesquisa subsequente. Em seguida, ele envia o `ImageInsightsToken` ao Bing e retorna resultados que incluem URLs de Pesquisa do Bing e URLs de imagens semelhantes encontradas online.

O código-fonte completo deste tutorial pode ser encontrado com o tratamento de erros e anotações adicionais no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Se você estiver usando o linux/MacOS, poderá executar esse aplicativo usando o [mono](https://www.mono-project.com/).
* Os pacotes Pesquisa Visual e Pesquisa de Imagem do NuGet.
    - No Gerenciador de Soluções no Visual Studio, clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet** no menu. Instale o pacote de `Microsoft.Azure.CognitiveServices.Search.CustomSearch` e o pacote de `Microsoft.Azure.CognitiveServices.Search.ImageSearch`. A instalação dos pacotes NuGet também instala o seguinte:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Obter o ImageInsightsToken do SDK do Pesquisa de Imagem do Bing

Esse aplicativo usa um `ImageInsightsToken` obtido por meio do [SDK do pesquisa de imagem do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). Em um novo C# aplicativo de console, crie um cliente para chamar a API usando `ImageSearchClient()`. Em seguida, use `SearchAsync()` com sua consulta:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Armazene o primeiro resultado da pesquisa usando `imageResults.Value.First()`e, em seguida, armazene o `ImageInsightsToken`do Image Insight.

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

Esse `ImageInsightsToken` é enviado para Pesquisa Visual do Bing em uma solicitação.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Adicionar o ImageInsightsToken a uma solicitação de Pesquisa Visual

Especifique o `ImageInsightsToken` para uma solicitação de Pesquisa Visual criando um objeto `ImageInfo` da `ImageInsightsToken` contida em respostas de Pesquisa Visual do Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Usar Pesquisa Visual do Bing para localizar imagens de um ImageInsightsToken

O objeto `VisualSearchRequest` contém informações sobre a imagem em `ImageInfo` a ser pesquisada. O método `VisualSearchMethodAsync()` obtém os resultados. Você não precisa fornecer um binário de imagem, pois a imagem é representada pelo token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterar pelos resultados da Pesquisa Visual

Os resultados da Pesquisa Visual são objetos `ImageTag`. Cada etiqueta contém uma lista de objetos `ImageAction`. Cada `ImageAction` contém um campo `Data`, que é uma lista de valores que dependem do tipo de ação. Você pode iterar os objetos `ImageTag` no `visualSearchResults.Tags`, por exemplo, e obter a marca `ImageAction` dentro dele. O exemplo a seguir imprime os detalhes de `PagesIncluding` ações:

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

Obter as URLs da imagem real dos tipos de ação requer uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um elemento `Data` com uma lista de valores. Cada valor é o URL de uma imagem.  O seguinte converte o `PagesIncluding` tipo de ação para `ImageModuleAction` e lê os valores:

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

|actionType  |URL  | |
|---------|---------|---------|
|MoreSizes-> WebSearchUrl     |         |
|VisualSearch-> WebSearchUrl     |         |
|ImageById-> WebSearchUrl    |         |
|RelatedSearches-> WebSearchUrl:    |         |
|DocumentLevelSuggestions-> WebSearchUrl:     |         |
|TopicResults-> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults-> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Como mostrado acima, os tipos `TopicResults` e `ImageResults` contêm consultas para imagens relacionadas. O link de URLs para resultados da pesquisa do Bing.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única Pesquisa Visual](tutorial-bing-visual-search-single-page-app.md)
