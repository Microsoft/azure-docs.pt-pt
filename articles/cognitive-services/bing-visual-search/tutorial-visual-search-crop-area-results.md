---
title: 'Tutorial: Corte uma imagem com o Bing Visual Search SDK'
description: Utilize o Bing Visual Search SDK para obter insights de áreas específicas numa imagem.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.openlocfilehash: 4778a4089c7374c1ac6a9312064dcfb1e0325b63
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478490"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Tutorial: Corte uma imagem com o Bing Visual Search SDK para C #

O Bing Visual Search SDK permite-lhe cortar uma imagem antes de encontrar imagens online semelhantes. Esta aplicação planta uma única pessoa a partir de uma imagem que contém várias pessoas, e depois devolve os resultados de pesquisa contendo imagens semelhantes encontradas online.

O código fonte completo para esta aplicação está disponível com manipulação adicional de erros e anotações no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Este tutorial ilustra como:

> [!div class="checklist"]
> * Envie um pedido utilizando o Bing Visual Search SDK
> * Cultivar uma área de imagem para pesquisar com Bing Visual Search
> * Receber e lidar com a resposta
> * Encontre os URLs de itens de ação na resposta

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019.](https://www.visualstudio.com/downloads/)
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
* O pacote de [Pesquisa Personalizada do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)instalado.
    - Do Solution Explorer no Visual Studio, clique no seu projeto e selecione **Gerir pacotes NuGet** a partir do menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Quando instala o pacote de Pesquisa Personalizada do NuGet também instala as assemblagens seguintes:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Especificar a área de cultura de imagem

Esta aplicação planta uma área desta imagem da equipa de liderança sénior da Microsoft. Esta área de cultura é definida utilizando coordenadas superiores à esquerda e inferior direita, representadas em percentagem de toda a imagem:  

![Equipa de Liderança Sénior da Microsoft](./media/MS_SrLeaders.jpg)

Esta imagem é cortada `ImageInfo` criando um objeto a partir `ImageInfo` da `VisualSearchRequest`área de cultivo, e carregando o objeto em um . O `ImageInfo` objeto também inclui o URL da imagem:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Procurar imagens semelhantes à área de cultivo

A `VisualSearchRequest` variável contém informações sobre a área de cultivo da imagem e o seu URL. O `VisualSearchMethodAsync()` método obtém os resultados:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Obtenha os dados de URL de`ImageModuleAction`

Os resultados `ImageTag` da Pesquisa Visual bing são objetos. Cada etiqueta contém uma lista de objetos `ImageAction`. Cada `ImageAction` um `Data` contém um campo, que é uma lista de valores que dependem do tipo de ação.

Pode imprimir os vários tipos com o seguinte código:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

A aplicação concluída devolve:

|ActionType  |do IdP  | |
|---------|---------|---------|
|Páginas incluindo WebSearchURL     |         |
|Mais Tamanhos WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelacionadoSPesquisas WebSearchURL     |         |
|Entidade -> WebSearchUrl     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TópicoSResultados -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4spkUyCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|Resultados de imagem -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJ szgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Como mostrado acima, `Entity` o ActionType contém uma consulta de pesquisa bing que devolve informações sobre uma pessoa reconhecível, lugar ou coisa. Os tipos `TopicResults` e `ImageResults` contêm consultas de imagens relacionadas. Os URLs na lista ligam aos resultados da pesquisa do Bing.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Obtenha URLs `PagesIncluding` `ActionType` para imagens

Obter os URLs da imagem atual requer uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um elemento `Data` com uma lista de valores. Cada valor é o URL de uma imagem. O seguinte lança `PagesIncluding` o `ImageModuleAction` tipo de ação e lê os valores:

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
