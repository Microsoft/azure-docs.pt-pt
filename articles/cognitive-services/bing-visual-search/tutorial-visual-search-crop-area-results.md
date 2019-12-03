---
title: 'Tutorial: cortar uma imagem com o SDK do Pesquisa Visual do Bing'
description: Use o SDK do Pesquisa Visual do Bing para obter informações de ares específicas em uma imagem.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 7adca44f1710431ad1095cbd0da897d4c7c7f325
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689340"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Tutorial: cortar uma imagem com o SDK do Pesquisa Visual do Bing paraC#

O SDK do Pesquisa Visual do Bing permite cortar uma imagem antes de localizar imagens online semelhantes. Esse aplicativo corta uma única pessoa de uma imagem que contém várias pessoas e, em seguida, retorna os resultados da pesquisa que contêm imagens semelhantes encontradas online.

O código-fonte completo para esse aplicativo está disponível com o tratamento de erros e anotações adicionais no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Este tutorial ilustra como:

> [!div class="checklist"]
> * Enviar uma solicitação usando o SDK do Pesquisa Visual do Bing
> * Cortar uma área de imagem para pesquisar com Pesquisa Visual do Bing
> * Receber e tratar a resposta
> * Localizar as URLs dos itens de ação na resposta

## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
* O pacote de [Pesquisa Personalizada do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)instalado.
    - No Gerenciador de Soluções no Visual Studio, clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet** no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Quando instala o pacote de Pesquisa Personalizada do NuGet também instala as assemblagens seguintes:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Especificar a área de corte da imagem

Este aplicativo corta uma área desta imagem da equipe de liderança sênior da Microsoft. Essa área de corte é definida usando coordenadas superior esquerda e inferior direita, representadas como uma porcentagem da imagem inteira:  

![Equipa de Liderança Sénior da Microsoft](./media/MS_SrLeaders.jpg)

Essa imagem é cortada com a criação de um objeto de `ImageInfo` da área de corte e o carregamento do objeto de `ImageInfo` em um `VisualSearchRequest`. O objeto `ImageInfo` também inclui a URL da imagem:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Pesquisar imagens semelhantes à área de corte

A variável `VisualSearchRequest` contém informações sobre a área de corte da imagem e sua URL. O método `VisualSearchMethodAsync()` Obtém os resultados:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Obter os dados da URL do `ImageModuleAction`

Pesquisa Visual do Bing resultados são `ImageTag` objetos. Cada etiqueta contém uma lista de objetos `ImageAction`. Cada `ImageAction` contém um campo `Data`, que é uma lista de valores que dependem do tipo de ação.

Você pode imprimir os vários tipos com o seguinte código:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

A aplicação concluída devolve:

|actionType  |URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entidade-> WebSearchUrl     | \:https//www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults-> WebSearchUrl    |  \:https//www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults-> WebSearchUrl    |  \:https//www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Como mostrado acima, o ActionType `Entity` contém uma consulta de pesquisa do Bing que retorna informações sobre uma pessoa, lugar ou coisa reconhecível. Os tipos `TopicResults` e `ImageResults` contêm consultas de imagens relacionadas. Os URLs na lista ligam aos resultados da pesquisa do Bing.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Obter URLs para `PagesIncluding` `ActionType` imagens

Obter os URLs da imagem atual requer uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um elemento `Data` com uma lista de valores. Cada valor é o URL de uma imagem. O seguinte converte o `PagesIncluding` tipo de ação para `ImageModuleAction` e lê os valores:

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
> [Criar um aplicativo Web de página única Pesquisa Visual](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Ver também
> [O que é o API da Pesquisa Visual do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
