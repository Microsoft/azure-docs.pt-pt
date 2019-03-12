---
title: Encontrar imagens semelhantes de pesquisas anteriores com ImageInsightsToken - pesquisa Visual do Bing
titlesuffix: Azure Cognitive Services
description: Utilize o SDK de pesquisa Visual do Bing para obter os URLs de imagens especificadas pelo ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: c3ceb8f130118ee7abdbe801cd3931e9f870ec4c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538889"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Encontrar imagens semelhantes de pesquisas anteriores com ImageInsightsToken

O SDK de pesquisa Visual permite-lhe encontrar imagens online a partir de pesquisas anteriores que retornam um `ImageInsightsToken`.  Esta aplicação obtém um `ImageInsightsToken` e utiliza o token numa pesquisa subsequente. Em seguida, envia o `ImageInsightsToken` do Bing e devolve resultados que incluem URLs de pesquisa do Bing e URLs de imagens semelhantes encontradas online.

O código-fonte completo para este exemplo pode ser encontrado com o tratamento de erros adicionais e anotações no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInisghtsTokens.cs).

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
* Os pacotes de pesquisa Visual do NuGet e pesquisa de imagens. 
    - No Explorador de Soluções do Visual Studio, clique com o botão direito do rato no seu projeto e selecione `Manage NuGet Packages` no menu. Instalar o `Microsoft.Azure.CognitiveServices.Search.CustomSearch` pacote e o `Microsoft.Azure.CognitiveServices.Search.ImageSearch` pacote. Instalar os pacotes NuGet também instala o seguinte:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Obter o ImageInsightsToken do SDK de pesquisa de imagens do Bing

Esta aplicação utiliza uma `ImageInsightsToken` obtidas através do [SDK de pesquisa de imagens do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). Numa nova C# aplicação de consola, criar um cliente para chamar a API utilizando `ImageSearchAPI()`. Em seguida, utilizar `SearchAsync()` com a sua consulta.

```csharp
var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Store, a primeira pesquisa resultar usando `imageResults.Value.First()`e, em seguida, armazenar a informação de imagem `ImageInsightsToken`. 

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

Isso `ImageInsightsToken` será enviado para a pesquisa Visual do Bing num pedido.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Adicionar o ImageInsightsToken a um pedido de pesquisa Visual

Especifique a `ImageInsightsToken` de um pedido de pesquisa Visual através da criação de um `ImageInfo` de objeto do `ImageInsightsToken` contidas nas respostas da pesquisa Visual do Bing. 

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Utilize a pesquisa Visual do Bing para localizar imagens a partir de um ImageInsightsToken

O `VisualSearchRequest` objeto contém informações sobre a imagem no `ImageInfo` deve ser pesquisada. O método `VisualSearchMethodAsync()` obtém os resultados. Não precisa fornecer um binário de imagem, conforme a imagem é representada pelo token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Iterar pelos resultados da pesquisa Visual

Os resultados da Pesquisa Visual são objetos `ImageTag`.  Cada etiqueta contém uma lista de objetos `ImageAction`.  Cada `ImageAction` contém uma `Data` campo que é uma lista de valores que dependem do tipo de ação. Pode iterar o `ImageTag` objetos na `visualSearchResults.Tags`, para a instância e get a `ImageAction` etiqueta dentro do mesmo. O exemplo abaixo imprime os detalhes de `PagesIncluding` ações.

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

Obter os URLs de imagem real de tipos de ação requer uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um `Data` elemento com uma lista de valores.  Cada valor é o URL de uma imagem.  O seguinte converte o tipo de ação `PagesIncluding` em `ImageModuleAction` e lê os valores.

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


## <a name="returned-urls"></a>URLs retornados

A aplicação completa devolve os seguintes URLs:

|ActionType  |do IdP  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |         
|VisualSearch -> WebSearchUrl     |         |         
|ImageById -> WebSearchUrl    |         |         
|RelatedSearches -> WebSearchUrl:    |         |         
|DocumentLevelSuggestions -> WebSearchUrl:     |         |         
|TopicResults -> WebSearchUrl    | https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |         
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |         

Conforme mostrado acima, o `TopicResults` e `ImageResults` tipos conter consultas para imagens relacionadas. A ligação de URLs para os resultados da pesquisa do Bing.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-visual-search-single-page-app.md)
