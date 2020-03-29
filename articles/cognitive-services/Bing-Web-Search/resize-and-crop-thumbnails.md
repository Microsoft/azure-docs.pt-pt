---
title: Redimensionar e cultivar miniaturas de imagem - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Algumas respostas das APIs de Pesquisa de Bing incluem URLs a imagens de miniaturas servidas por Bing, que você pode redimensionar e cultivar, e pode conter parâmetros de consulta.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110634"
---
# <a name="resize-and-crop-thumbnail-images"></a>Imagens de redimensionamento e colheita de miniaturas

Algumas respostas das APIs de Pesquisa de Bing incluem URLs a imagens de miniaturas servidas por Bing, que você pode redimensionar e cultivar, e pode conter parâmetros de consulta. Por exemplo:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Se apresentar um subconjunto destas miniaturas, forneça uma opção para visualizar as imagens restantes.

> [!NOTE]
> Certifique-se de que a colheita e redimensionamento de imagens de miniaturas fornecerá um cenário de pesquisa que respeite os direitos de terceiros, conforme exigido pelos requisitos de [utilização e exibição](use-display-requirements.md)da API de Pesquisa bing.

## <a name="resize-a-thumbnail"></a>Redimensionar uma miniatura 

Para redimensionar uma miniatura, bing recomenda `w` que especifique apenas um dos parâmetros de consulta (largura) ou `h` (altura) no URL da miniatura. Especificar apenas a altura ou largura permite que Bing mantenha o aspeto original da imagem. Especifique a largura e a altura em pixels. 

Por exemplo, se a miniatura original for 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

E pretende diminuir o seu `w` tamanho, definir o parâmetro `336`para um `h` novo valor (por exemplo), e remover o parâmetro:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Se especificar apenas a altura ou largura de uma miniatura, a relação de aspeto original da imagem será mantida. Se especificar ambos os parâmetros, e a relação de aspeto não for mantida, Bing adicionará estofos brancos à borda da imagem.

Por exemplo, se redimensionar uma imagem de 480x359 para 200x200 sem cortar, a largura total conterá a imagem, mas a altura conterá 25 píxeis de estofamento branco na parte superior e inferior da imagem. Se a imagem fosse 359x480, as fronteiras esquerda e direita conteriam estofamento branco. Se cortar a imagem, não é adicionado estofo branco.  

A imagem seguinte mostra o tamanho original de uma imagem de miniatura (480x300).  
  
![Imagem de paisagem original](./media/resize-crop/bing-resize-crop-landscape.png)  
  
A imagem seguinte mostra a imagem redimensionada para 200x200. O rácio de aspeto é mantido e as fronteiras superior e inferior são acolchoadas com branco (a borda preta aqui está incluída para mostrar o estofo).  
  
![Imagem de paisagem redimensionada](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Se especificar dimensões superiores à largura e altura originais da imagem, bing adicionará estofos brancos às bordas esquerda e superior.  

## <a name="request-different-thumbnail-sizes"></a>Solicitar diferentes tamanhos de miniatura

Para solicitar um tamanho de imagem diferente da miniatura, remova todos `id` os `pid` parâmetros de consulta do URL da miniatura, exceto os parâmetros e parâmetros. Em seguida, `&w` adicione o `&h` parâmetro de consulta (largura) ou (altura) com o tamanho de imagem desejado em píxeis, mas não ambos. Bing manterá a relação de aspeto original da imagem. 

Para aumentar a largura da imagem especificada pelo URL acima para 165 pixels, utilizaria o seguinte URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Se você pedir uma imagem maior do que o tamanho original da imagem, Bing adiciona estofamento branco em torno da imagem conforme necessário. Por exemplo, se o tamanho original da imagem for 474x316 e se tiver definido `&w` para 500, bing devolverá uma imagem de 500x333. Esta imagem terá 8,5 pixels de estofamento branco ao longo das bordas superior e inferior, e 13 pixels de estofamento nas bordas esquerda e direita.

Para evitar que bing adicione estofamento branco se o tamanho solicitado for `&p` maior do que o tamanho original da imagem, defina o parâmetro de consulta para 0. Por exemplo, se `&p=0` incluir o parâmetro no URL acima, bing devolverá uma imagem 474x316 em vez de uma imagem 500x333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Se especificar `&w` os `&h` parâmetros de consulta e ambos, Bing manterá a relação de aspeto da imagem e adiciona o acolchoamento branco conforme necessário. Por exemplo, se o tamanho original da imagem for de 474x316 e definir os parâmetros de largura e altura para 200x200 (`&w=200&h=200`), Bing devolve uma imagem que contém 33 pixels de estofamento branco na parte superior e inferior. Se incluir `&p` o parâmetro de consulta, Bing devolve uma imagem de 200x134.

## <a name="crop-a-thumbnail"></a>Colheita de uma miniatura 

Para plantar uma imagem, inclua o parâmetro de `c` consulta (crop). Pode utilizar os seguintes valores:
  
- `4`&mdash; Relação cega  
- `7`&mdash; Relação Inteligente  

### <a name="smart-ratio-cropping"></a>Colheita de rácio inteligente

Se solicitar a colheita de Smart `c` Ratio `7`(definindo o parâmetro para), bing irá cortar uma imagem do centro da sua região de interesse para fora, mantendo ao mesmo tempo a relação de aspeto da imagem. A região de interesse é a área da imagem que Bing determina contém as partes mais importadas. O seguinte mostra um exemplo de interesse.  
  
![Região de interesse](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Se redimensionar uma imagem e solicitar a colheita de Relações Inteligentes, bing reduz a imagem para o tamanho solicitado, mantendo a relação de aspeto. Bing então planta a imagem com base nas dimensões redimensionadas. Por exemplo, se a largura redimensionada for inferior ou igual à altura, Bing cortará a imagem à esquerda e à direita do centro da região de interesse. Caso contrário, Bing irá abaná-lo para o topo e o fundo do centro da região de interesse.  
  
 
O seguinte mostra que a imagem reduziu para 200x200 usando a colheita de Smart Ratio. Como Bing mede a imagem do canto superior esquerdo, a parte inferior da imagem é cortada. 
  
![Imagem de paisagem cortada para 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
O seguinte mostra que a imagem reduziu para 200x100 usando a colheita de Smart Ratio. Como Bing mede a imagem do canto superior esquerdo, a parte inferior da imagem é cortada. 
   
![Imagem de paisagem cortada para 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
O seguinte mostra que a imagem foi reduzida para 100x200 utilizando a colheita de Smart Ratio. Porque Bing mede a imagem do centro, as partes esquerda e direita da imagem são cortadas.
  
![Imagem de paisagem cortada para 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Se bing não conseguir determinar a região de interesse da imagem, o serviço usará a colheita de Razão Cega.  

### <a name="blind-ratio-cropping"></a>Colheita de rácio cego

Se solicitar a colheita de relação `c` cega `4`(definindo o parâmetro para), bing utiliza as seguintes regras para cortar a imagem.  
  
- Se `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, a imagem for medida do canto superior esquerdo e cortada na parte inferior.  
- Se `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`a imagem for medida a partir do centro e cortada para a esquerda e para a direita.  

O seguinte mostra uma imagem de retrato que é 225x300.  
  
![Imagem original do girassol](./media/resize-crop/bing-resize-crop-sunflower.png)
  
O seguinte mostra que a imagem reduziu para 200x200 usando a colheita de Razão Cega. A imagem é medida a partir do canto superior esquerdo, resultando na parte inferior da imagem sendo cortada.  
  
![Imagem de girassol cortada para 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
O seguinte mostra que a imagem reduziu para 200x100 usando a colheita de Razão Cega. A imagem é medida a partir do canto superior esquerdo, resultando na parte inferior da imagem sendo cortada.  
  
![Imagem de girassol cortada a 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
O seguinte mostra a imagem reduzida a 100x200 usando a colheita de Razão Cega. A imagem é medida a partir do centro, resultando nas partes esquerda e direita da imagem sendo cortada.  
  
![Imagem de girassol cortada para 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Passos seguintes

* [O que são as APIs de Pesquisa bing?](bing-api-comparison.md)
* [Requisitos de utilização e apresentação da API de Pesquisa do Bing](use-display-requirements.md)
