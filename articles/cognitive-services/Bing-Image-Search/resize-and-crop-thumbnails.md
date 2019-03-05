---
title: Redimensionar e cortar imagens em miniatura - API de pesquisa de imagens do Bing
titleSuffix: Azure Cognitive Services
description: Redimensionar e cortar as imagens em miniatura incluídas nas respostas de API de pesquisa de imagens do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 78391a9892ff568bfa41b16beb4a5b599ceee6c6
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337384"
---
# <a name="resize-and-crop-thumbnail-images"></a>Redimensionar e cortar imagens em miniatura

Após o processamento de uma consulta de pesquisa, o Bing irá gerar informações em miniatura para todas as imagens no seu [resposta](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Estas informações podem ser utilizadas para apresentar todos os ou um subconjunto das miniaturas retornados. Se exibir um subconjunto, fornecem uma opção para ver as imagens restantes.


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Se o utilizador clicar na miniatura, pode utilizar [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) para apresentar a imagem em tamanho completo ao utilizador. Lembre-se de atribuir a imagem.

Se `shoppingSourcesCount` ou `recipeSourcesCount` for maior que zero, adicione a criação de distintivos, tal como um carrinho de compras, à miniatura para indicar que existem compras ou receitas para o item na imagem.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Para obter informações sobre a imagem, tais como as páginas Web que incluem a imagem ou as pessoas que foram reconhecidas na imagem, utilize [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Para obter mais informações, veja [Informações sobre uma imagem](image-insights.md).

## <a name="resizing-and-cropping-thumbnails"></a>Redimensionamento e corte miniaturas

Também pode redimensionar e expanda miniaturas, por exemplo, quando o cursor de um utilizador passa o rato por acima dela.
> [!NOTE]
> Lembre-se de atribuir a imagem se a expandir. Por exemplo, extraindo o anfitrião de [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) e apresentando-o abaixo da imagem.

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]
