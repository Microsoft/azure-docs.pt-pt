---
title: 'Tutorial: Corte uma imagem com o Bing Visual Search SDK'
description: Utilize o Bing Visual Search SDK para obter informações de ares específicos numa imagem.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 84cbdfbf4d34e1779fc119e4b6a92a3bbec88bdc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88925135"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Tutorial: Corte uma imagem com o Bing Visual Search SDK para C #

O Bing Visual Search SDK permite-lhe cultivar uma imagem antes de encontrar imagens online semelhantes. Esta aplicação cria uma única pessoa a partir de uma imagem que contém várias pessoas, e depois devolve resultados de pesquisa contendo imagens semelhantes encontradas online.

O código fonte completo desta aplicação está disponível com tratamento adicional de erros e anotações no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Este tutorial ilustra como:

> [!div class="checklist"]
> * Enviar um pedido usando o Bing Visual Search SDK
> * Corte uma área de imagem para pesquisar com Bing Visual Search
> * Receber e lidar com a resposta
> * Encontre os URLs de itens de ação na resposta

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
* O pacote de [Pesquisa Personalizada do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)instalado.
    - A partir do Solution Explorer no Estúdio Visual, clique à direita no seu projeto e selecione **Gerir pacotes NuGet** no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Quando instala o pacote de Pesquisa Personalizada do NuGet também instala as assemblagens seguintes:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Especificar a área de cultura de imagem

Esta aplicação cria uma área desta imagem da equipa de liderança sénior da Microsoft. Esta área de cultura é definida com coordenadas superiores esquerda e inferior-direita, representadas em percentagem de toda a imagem:  

![Equipa de Liderança Sénior da Microsoft](./media/MS_SrLeaders.jpg)

Esta imagem é cortada criando um `ImageInfo` objeto a partir da área de cultivo, e carregando o objeto em um `ImageInfo` `VisualSearchRequest` . O `ImageInfo` objeto também inclui o URL da imagem:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Pesquisa de imagens semelhantes à área de cultivo

A variável `VisualSearchRequest` contém informações sobre a área de cultura da imagem e o seu URL. O `VisualSearchMethodAsync()` método obtém os resultados:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Obtenha os dados de URL a partir de `ImageModuleAction`

Bing Os resultados da pesquisa visual são `ImageTag` objetos. Cada etiqueta contém uma lista de objetos `ImageAction`. Cada `ImageAction` um contém um `Data` campo, que é uma lista de valores que dependem do tipo de ação.

Pode imprimir os vários tipos com o seguinte código:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

A aplicação concluída devolve:

|ActionType  |URL  |
|---------|---------|
|PáginasIncluindo WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelacionadosSearches WebSearchURL     |         |
|Entidade -> WebSearchUrl     | https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TópicoResults -> WebSearchUrl    |  https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A42137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7X4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF82D7386A42137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Como mostrado acima, o `Entity` ActionType contém uma consulta de pesquisa Bing que devolve informações sobre uma pessoa, lugar ou coisa reconhecíveis. Os tipos `TopicResults` e `ImageResults` contêm consultas de imagens relacionadas. Os URLs na lista ligam aos resultados da pesquisa do Bing.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Obtenha URLs para `PagesIncluding` `ActionType` imagens

Obter os URLs da imagem atual requer uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um elemento `Data` com uma lista de valores. Cada valor é o URL de uma imagem. O seguinte lança o `PagesIncluding` tipo de ação `ImageModuleAction` e lê os valores:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Criar uma aplicação web de pesquisa visual de uma página](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Consulte também
> [O que é a API de Pesquisa Visual do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
