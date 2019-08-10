---
title: Redimensionar e cortar miniaturas de imagem-API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como redimensionar e cortar miniaturas fornecidas pelo APIs de Pesquisa do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: ecc6eb86e7115143fa63b44f9191b1fe8d3703b8
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881803"
---
# <a name="resize-and-crop-thumbnail-images"></a>Redimensionar e cortar imagens em miniatura

Algumas respostas do APIs de Pesquisa do Bing incluem URLs para imagens em miniatura servidas pelo Bing, que você pode redimensionar e cortar e pode conter parâmetros de consulta. Por exemplo:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Se você exibir um subconjunto dessas miniaturas, forneça uma opção para exibir as imagens restantes.

> [!NOTE]
> Certifique-se de que as imagens em miniatura de recorte e redimensionamento forneçam um cenário de pesquisa que respeita os direitos de terceiros, conforme exigido pelo Pesquisa do Bing uso da API [e requisitos de exibição](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Redimensionar uma miniatura 

Para redimensionar uma miniatura, o Bing recomenda que você especifique apenas um `w` dos parâmetros de consulta `h` (largura) ou (altura) na URL da miniatura. Especificar apenas a altura ou a largura permite que o Bing Mantenha o aspecto original da imagem. Especifique a largura e a altura em pixels. 

Por exemplo, se a miniatura original for 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

E você quiser diminuir seu tamanho, defina o `w` parâmetro para um novo valor (por exemplo `336`,) e remova o `h` parâmetro:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Se você especificar apenas a altura ou a largura de uma miniatura, a taxa de proporção original da imagem será mantida. Se você especificar ambos os parâmetros e a taxa de proporção não for mantida, o Bing adicionará o preenchimento branco à borda da imagem.

Por exemplo, se você redimensionar uma imagem 480x359 para 200 x 200 sem corte, a largura inteira conterá a imagem, mas a altura conterá 25 pixels de preenchimento branco na parte superior e inferior da imagem. Se a imagem foi 359x480da, as bordas esquerda e direita conterão o preenchimento branco. Se você cortar a imagem, o preenchimento branco não será adicionado.  

A figura a seguir mostra o tamanho original de uma imagem em miniatura (480X300).  
  
![Imagem de paisagem original](./media/resize-crop/bing-resize-crop-landscape.png)  
  
A imagem a seguir mostra a imagem redimensionada para 200 x 200. A taxa de proporção é mantida e as bordas superior e inferior são preenchidas com branco (a borda preta aqui está incluída para mostrar o preenchimento).  
  
![Imagem de paisagem redimensionada](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Se você especificar dimensões que sejam maiores que a largura e a altura originais da imagem, o Bing adicionará o preenchimento branco às bordas esquerda e superior.  

## <a name="request-different-thumbnail-sizes"></a>Solicitar tamanhos de miniatura diferentes

Para solicitar um tamanho de imagem em miniatura diferente, remova todos os parâmetros de consulta da URL da miniatura `id` , `pid` exceto os parâmetros e. Em seguida, adicione `&w` o parâmetro de consulta `&h` (largura) ou (altura) com o tamanho de imagem desejado em pixels, mas não ambos. O Bing manterá a taxa de proporção original da imagem. 

Para aumentar a largura da imagem especificada pela URL acima para 165 pixels, você usaria a seguinte URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Se você solicitar uma imagem maior do que o tamanho original da imagem, o Bing adicionará o preenchimento branco em volta da imagem, conforme necessário. Por exemplo, se o tamanho original da imagem for 474x316 e você definir `&w` como 500, Bing retornará uma imagem 500x333. Essa imagem terá 8,5 pixels de preenchimento branco ao longo das bordas superior e inferior, e 13 pixels de preenchimento nas bordas esquerda e direita.

Para impedir que o Bing adicione um preenchimento branco se o tamanho solicitado for maior que o tamanho original da imagem, `&p` defina o parâmetro de consulta como 0. Por exemplo, se você incluir o `&p=0` parâmetro na URL acima, o Bing retornará uma imagem 474x316 em vez de uma imagem 500x333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Se você especificar ambos `&w` os `&h` parâmetros de consulta e, o Bing manterá a taxa de proporção da imagem e adicionará o preenchimento branco conforme necessário. Por exemplo, se o tamanho original da imagem for 474x316 e você definir os parâmetros Width e Height como 200 x 200 (`&w=200&h=200`), o Bing retornará uma imagem que contenha 33 pixels de preenchimento branco na parte superior e inferior. Se você incluir o `&p` parâmetro de consulta, o Bing retornará uma imagem 200x134.

## <a name="crop-a-thumbnail"></a>Cortar uma miniatura 

Para cortar uma imagem, inclua o `c` parâmetro de consulta (cortar). Você pode usar os seguintes valores:
  
- `4`&mdash; Proporção de cego  
- `7`&mdash; Taxa inteligente  

### <a name="smart-ratio-cropping"></a>Corte de taxa inteligente

Se você solicitar um corte de taxa inteligente (definindo `c` o parâmetro `7`como), o Bing cortará uma imagem do centro de sua região de interesse para fora, mantendo a taxa de proporção da imagem. A região de interesse é a área da imagem que o Bing determina contém a maioria das partes de importação. Veja a seguir uma região de exemplo de interesse.  
  
![Região de interesse](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Se você redimensionar uma imagem e solicitar o corte de taxa inteligente, o Bing reduzirá a imagem para o tamanho solicitado, mantendo a taxa de proporção. Em seguida, o Bing corta a imagem com base nas dimensões redimensionadas. Por exemplo, se a largura redimensionada for menor ou igual à altura, o Bing cortará a imagem à esquerda e à direita do centro da região de interesse. Caso contrário, o Bing irá cortá-lo na parte superior e inferior do centro da região de interesse.  
  
 
O seguinte mostra a imagem reduzida para 200 x 200 usando o corte de taxa inteligente. Como o Bing mede a imagem do canto superior esquerdo, a parte inferior da imagem é cortada. 
  
![Imagem em paisagem cortada para 200 x 200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
O seguinte mostra a imagem reduzida para 200 x 100 usando o corte de taxa inteligente. Como o Bing mede a imagem do canto superior esquerdo, a parte inferior da imagem é cortada. 
   
![Imagem em paisagem cortada para 200 x 100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
O seguinte mostra a imagem reduzida para 100 x 200 usando o corte de taxa inteligente. Como o Bing mede a imagem do centro, as partes esquerda e direita da imagem são cortadas.
  
![Imagem em paisagem cortada para 100 x 200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Se o Bing não puder determinar a região de interesse da imagem, o serviço usará o corte de taxa oculta.  

### <a name="blind-ratio-cropping"></a>Corte de proporção de branco

Se você solicitar um corte de taxa oculta (definindo `c` o parâmetro `4`como), o Bing usará as regras a seguir para cortar a imagem.  
  
- Se `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, a imagem é medida do canto superior esquerdo e cortada na parte inferior.  
- Se `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, a imagem é medida do centro e cortada para a esquerda e para a direita.  

O seguinte mostra uma imagem de retrato 225x300.  
  
![Imagem girassol original](./media/resize-crop/bing-resize-crop-sunflower.png)
  
O a seguir mostra a imagem reduzida para 200 x 200 usando o corte de taxa oculta. A imagem é medida do canto superior esquerdo, resultando na parte inferior da imagem que está sendo cortada.  
  
![Imagem girassol cortada para 200 x 200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
O a seguir mostra a imagem reduzida para 200 x 100 usando o corte de taxa oculta. A imagem é medida do canto superior esquerdo, resultando na parte inferior da imagem que está sendo cortada.  
  
![Imagem girassol cortada para 200 x 100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
O a seguir mostra a imagem reduzida para 100 x 200 usando o corte de taxa oculta. A imagem é medida a partir do centro, resultando nas partes esquerda e direita da imagem que está sendo cortada.  
  
![Imagem girassol cortada para 100 x 200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Passos seguintes

* [Quais são os APIs de Pesquisa do Bing?](bing-api-comparison.md)
* [Requisitos de exibição e uso de API Pesquisa do Bing](use-display-requirements.md)
