---
title: Redimensionar e cortar miniaturas de imagem - API de pesquisa Web Bing
titleSuffix: Azure Cognitive Services
description: Saiba como redimensionar e cortar miniaturas fornecidas pelas APIs de pesquisa do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 6a5b2dada254a0bfc7fa60172f56221ba67ad279
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867915"
---
# <a name="resize-and-crop-thumbnail-images"></a>Redimensionar e cortar imagens em miniatura

Algumas respostas das APIs de pesquisa do Bing incluem URLs para imagens em miniatura servidas pelo Bing, o que pode redimensionar e recorte e podem conter parâmetros de consulta. Por exemplo:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Se exibir um subconjunto dessas miniaturas, fornecem uma opção para ver as imagens restantes.

> [!NOTE]
> Certifique-se de que o corte e redimensionamento de imagens em miniatura fornecerá um cenário de pesquisa que respeita os direitos de terceiros, conforme exigido pela API de pesquisa do Bing [utilizar e apresentam os requisitos](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Redimensionar uma miniatura 

Para redimensionar uma miniatura, Bing recomenda que especifique apenas um a `w` (largura) ou `h` parâmetros de consulta (altura) no URL da miniatura. Especificar apenas o altura ou largura permite manter o aspecto de original da imagem do Bing. Especifique a largura e altura em pixels. 

Por exemplo, se a miniatura original é 480 x 620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

E se pretender reduzir seu tamanho, defina o `w` parâmetro para um novo valor (por exemplo `336`) e remover o `h` parâmetro:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Se especificar apenas o altura ou largura de uma miniatura, taxa de proporção da imagem original será mantida. Se especificar ambos os parâmetros e a proporção de aspecto não é mantida, Bing irá adicionar preenchimento branco para a borda da imagem.

Por exemplo, se o redimensionamento uma imagem de 480 x 359 para 200 x 200 sem corte, a largura total irá conter a imagem, mas a altura irá conter a 25 pixels do white preenchimento na parte superior e inferior da imagem. Se a imagem foi 359 x 480, as bordas esquerdas e direita iria conter preenchimento branco. Se recortar a imagem, não é adicionado preenchimento branco.  

A imagem seguinte mostra o tamanho original de uma imagem em miniatura (480 x 300).  
  
![Imagem de paisagem original](./media/resize-crop/bing-resize-crop-landscape.png)  
  
A imagem seguinte mostra a imagem redimensionada para 200 x 200. A proporção é mantida e os limites superior e inferior são preenchidos com branco (a borda preta aqui é incluída para mostrar o preenchimento).  
  
![Imagem paisagem redimensionada](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Se especificar dimensões maiores que a largura e altura original da imagem, o Bing adicionará branco preenchimento para as bordas esquerdas e superior.  

## <a name="request-different-thumbnail-sizes"></a>Diferentes tamanhos de miniatura do pedido

Para pedir um tamanho de imagem em miniatura diferentes, remover todos os parâmetros de consulta de URL da miniatura, exceto os `id` e `pid` parâmetros. Em seguida, adicionar as `&w` (largura) ou `&h` parâmetro de consulta (altura) com o tamanho da imagem desejada em pixels, mas não ambos. Bing manterá a proporção original da imagem. 

Para aumentar a largura da imagem especificada pela URL acima para 165 pixels, usaria o seguinte URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Se solicitar uma imagem que é maior do que de tamanho original a imagem, o Bing adiciona preenchimento branco em torno da imagem conforme necessário. Por exemplo, se a imagem 's original, o tamanho é 474 x 316 e definir `&w` como 500, Bing irá devolver uma imagem de 500 x 333. Esta imagem tem 8,5 pixéis de preenchimento branco juntamente as bordas superiores e inferiores e 13 pixéis de preenchimento nas margens esquerdas e direita.

Para impedir que o Bing adicionar preenchimento branco se o tamanho pedido é superior ao tamanho original da imagem, defina o `&p` consultar o parâmetro como 0. Por exemplo, se incluir o `&p=0` parâmetro no URL acima, do Bing irá devolver uma imagem de 474 x 316 em vez de uma imagem de 500 x 333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Se especificar ambos `&w` e `&h` parâmetros de consulta do Bing irá manter a proporção de aspecto da imagem e adiciona o preenchimento branco conforme necessário. Por exemplo, se a imagem 's original, o tamanho é 474 x 316 e definir os parâmetros width e height como 200 x 200 (`&w=200&h=200`), o Bing devolve uma imagem que contenha 33 pixéis de preenchimento branco nas partes superior e inferior. Se incluir o `&p` parâmetro de consulta, o Bing devolve uma imagem de 200 x 134.

## <a name="crop-a-thumbnail"></a>Recortar uma miniatura 

Para recortar uma imagem, inclua o `c` parâmetro de consulta (recortar). Pode utilizar os seguintes valores:
  
- `4` &mdash; Rácio cego  
- `7` &mdash; Rácio inteligente  

### <a name="smart-ratio-cropping"></a>Corte o rácio do smart

Se solicitar corte inteligente rácio (definindo a `c` parâmetro `7`), Bing será cortar uma imagem a partir do centro da sua região de interesse exterior, mantendo a proporção de aspecto da imagem. A região de interesse é a área da imagem que Bing determina que contém a maioria das partes de importação. O código a seguir mostra uma região de exemplo de interesse.  
  
![Região de interesse](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Se redimensionar uma imagem e corte inteligente a taxa de pedidos, o Bing reduz a imagem do tamanho solicitado enquanto mantém a proporção de aspecto. Bing, em seguida, recorta a imagem com base nas dimensões redimensionadas. Por exemplo, se a largura redimensionada for menor que ou igual à altura, o Bing será recortar a imagem à esquerda e direita do centro da região de interesse. Caso contrário, Bing será cortá-lo para superior e inferior do centro da região de interesse.  
  
 
O código a seguir mostra a imagem reduzida para 200 x 200 usando o corte inteligente proporção. Uma vez que Bing mede a imagem a partir do canto superior esquerdo, a parte inferior da imagem é recortada. 
  
![Imagem de paisagem recortada para 200 x 200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
O código a seguir mostra a imagem reduzida para 200 x 100 usando o corte inteligente proporção. Uma vez que Bing mede a imagem a partir do canto superior esquerdo, a parte inferior da imagem é recortada. 
   
![Imagem de paisagem recortada para 200 x 100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
O código a seguir mostra a imagem reduzida para 100 x 200 usando o corte inteligente proporção. Porque Bing mede a imagem a partir do centro, são recortadas as partes esquerdas e direita da imagem.
  
![Imagem de paisagem recortada para 100 x 200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Se o Bing não é possível determinar a região da imagem de interesse, o serviço irá utilizar a proporção cego corte.  

### <a name="blind-ratio-cropping"></a>Corte de rácio cego

Se solicitar o corte de rácio cego (definindo a `c` parâmetro para `4`), o Bing utiliza as seguintes regras para recortar a imagem.  
  
- Se `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, a imagem é medida no canto superior esquerdo e recortada na parte inferior.  
- Se `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, a imagem é medida no centro da e recortada para a esquerda e direita.  

O código a seguir mostra uma imagem de retrato é 225 x 300.  
  
![Imagem sunflower original](./media/resize-crop/bing-resize-crop-sunflower.png)
  
O código a seguir mostra a imagem reduzida para 200 x 200 usando o corte de rácio cego. A imagem é medida desde o canto superior esquerdo, resultando na parte inferior da imagem a ser recortada.  
  
![Imagem sunflower recortada para 200 x 200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
O código a seguir mostra a imagem reduzida para 200 x 100 usando o corte de rácio cego. A imagem é medida desde o canto superior esquerdo, resultando na parte inferior da imagem a ser recortada.  
  
![Imagem sunflower recortada para 200 x 100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
O código a seguir mostra a imagem reduzida para 100 x 200 usando o corte de rácio cego. A imagem é medida desde o Centro de resulta nas partes esquerdas e direita da imagem a ser recortada.  
  
![Imagem sunflower recortada para 100 x 200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Passos Seguintes

* [Quais são as APIs de pesquisa do Bing?](bing-api-comparison.md)
* [API de pesquisa Bing utilizar e apresentam os requisitos](use-display-requirements.md)
