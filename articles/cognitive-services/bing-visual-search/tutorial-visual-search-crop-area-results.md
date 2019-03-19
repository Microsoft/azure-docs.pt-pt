---
title: 'Tutorial: Recortar uma imagem com o SDK de pesquisa Visual do Bing'
description: Utilize o SDK de pesquisa Visual do Bing para obter informações a partir ares específicos numa imagem.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 46bd170966d391f49d3c816c15e4bdf2e7449c90
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58102488"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Tutorial: Recortar uma imagem com o SDK de pesquisa Visual do Bing paraC#

O SDK de pesquisa Visual do Bing permite-lhe recortar uma imagem antes de encontrar imagens online que são semelhantes. Esta aplicação recorta a partir de uma imagem que contém várias pessoas, uma única pessoa e, em seguida, devolve os resultados da pesquisa que contém imagens semelhantes encontradas online.

O código-fonte completo para esta aplicação está disponível com o tratamento de erros adicionais e anotações no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Este tutorial ilustra como:

> [!div class="checklist"]
> * Enviar um pedido com o SDK de pesquisa Visual do Bing
> * Cortar uma área da imagem para pesquisar com pesquisa Visual do Bing
> * Receber e tratar da resposta
> * Encontre os URLs de itens de ação na resposta

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
* O pacote de [Pesquisa Personalizada do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)instalado. 
    - No Explorador de Soluções do Visual Studio, clique com o botão direito do rato no seu projeto e selecione `Manage NuGet Packages` no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Quando instala o pacote de Pesquisa Personalizada do NuGet também instala as assemblagens seguintes:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Especifique a área de recorte da imagem

Esta aplicação recorta uma área da imagem da equipa de liderança sênior da Microsoft. Esta área de recorte é definida usando coordenadas do canto superior esquerdo e direito inferior, representadas como uma percentagem da imagem inteira.  

![Equipa de Liderança Sénior da Microsoft](./media/MS_SrLeaders.jpg)

Esta imagem é recortada através da criação de um `ImageInfo` objeto a partir da área de recorte e carregar o `ImageInfo` objeto para um `VisualSearchRequest`. O `ImageInfo` objeto também inclui o URL da imagem.

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Procure imagens semelhantes para a área de recorte

a variável `VisualSearchRequest` contém informações sobre a área de recorte da imagem e o url. O método `VisualSearchMethodAsync()` obtém os resultados.

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Obter os dados de URL de ImageModuleAction

Resultados de pesquisa Visual do Bing são `ImageTag` objetos.  Cada etiqueta contém uma lista de objetos `ImageAction`.  Cada `ImageAction` contém uma `Data` campo que é uma lista de valores que dependem do tipo de ação.

Pode imprimir os vários tipos com o código a seguir:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

A aplicação concluída devolve:


|ActionType  |do IdP  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |         
|MoreSizes WebSearchURL     |         |         
|VisualSearch WebSearchURL    |         |         
|ImageById WebSearchURL     |         |         
|RelatedSearches WebSearchURL     |         |         
|Entity -> WebSearchUrl     | https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |         
|TopicResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |         
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |         

Conforme mostrado acima, o `Entity` ActionType contém uma consulta de pesquisa do Bing que retorna informações sobre uma pessoa reconhecível, local ou coisa.  Os tipos `TopicResults` e `ImageResults` contêm consultas de imagens relacionadas. Os URLs na lista ligam aos resultados da pesquisa do Bing.


## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Obter os URLs para PagesIncluding ActionType imagens

Obter os URLs da imagem atual requer uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um elemento `Data` com uma lista de valores.  Cada valor é o URL de uma imagem.  O seguinte converte o tipo de ação `PagesIncluding` em `ImageModuleAction` e lê os valores.

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-visual-search-single-page-app.md)

[Resposta da Pesquisa Visual](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
