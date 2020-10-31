---
title: Miniaturas de imagem de redimensionamento e cultura - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Algumas respostas das APIs de Pesquisa de Bing incluem URLs para imagens de miniaturas servidas por Bing, que você pode redimensionar e plantar, e pode conter parâmetros de consulta.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: ac1b636f644784092b5f68f59d787c328478df69
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096925"
---
# <a name="resize-and-crop-thumbnail-images"></a>Redimensionar e plantar imagens de miniaturas

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Algumas respostas das APIs de Pesquisa de Bing incluem URLs para imagens de miniaturas servidas por Bing, que você pode redimensionar e plantar, e pode conter parâmetros de consulta. Por exemplo:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Se apresentar um subconjunto destas miniaturas, forneça uma opção para visualizar as imagens restantes.

> [!NOTE]
> Certifique-se de que cortar e redimensionar imagens de miniaturas proporcionará um cenário de pesquisa que respeite os direitos de terceiros, conforme exigido pelos [requisitos de utilização e visualização](use-display-requirements.md)da API de pesquisa Bing Search .

## <a name="resize-a-thumbnail"></a>Redimensione uma miniatura 

Para redimensionar uma miniatura, Bing recomenda que especifique apenas um dos `w` parâmetros de consulta (largura) ou `h` (altura) no URL da miniatura. Especificar apenas a altura ou largura permite que Bing mantenha o aspeto original da imagem. Especifique a largura e a altura nos pixels. 

Por exemplo, se a miniatura original for 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

E pretende-se diminuir o seu tamanho, definir o `w` parâmetro para um novo valor (por `336` exemplo), e remover o `h`  parâmetro:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Se especificar apenas a altura ou largura de uma miniatura, a relação de aspeto original da imagem será mantida. Se especificar ambos os parâmetros, e a razão de aspeto não for mantida, Bing adicionará estofos brancos à borda da imagem.

Por exemplo, se redimensionar uma imagem de 480x359 para 200x200 sem cortar, a largura total conterá a imagem, mas a altura conterá 25 pixels de estofamento branco na parte superior e inferior da imagem. Se a imagem fosse 359x480, as fronteiras esquerda e direita conteriam estofos brancos. Se cortar a imagem, o acolchoado branco não é adicionado.  

A imagem a seguir mostra o tamanho original de uma imagem de miniatura (480x300).  
  
![Imagem de paisagem original](./media/resize-crop/bing-resize-crop-landscape.png)  
  
A imagem que se segue mostra a imagem redimensionada para 200x200. O rácio de aspeto é mantido e as fronteiras superior e inferior são acolchoadas com branco (a borda preta aqui está incluída para mostrar o acolchoamento).  
  
![Imagem de paisagem redimensionada](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Se especificar dimensões maiores do que a largura e altura originais da imagem, Bing adicionará estofos brancos à esquerda e às bordas superiores.  

## <a name="request-different-thumbnail-sizes"></a>Solicite diferentes tamanhos de miniatura

Para solicitar um tamanho diferente de imagem de miniatura, remova todos os parâmetros de consulta do URL da miniatura, exceto os `id` parâmetros e `pid` parâmetros. Em seguida, adicione o `&w` parâmetro de consulta (largura) ou `&h` (altura) com o tamanho de imagem desejado em pixels, mas não ambos. Bing manterá o rácio de aspeto original da imagem. 

Para aumentar a largura da imagem especificada pelo URL acima para 165 pixels, utilizaria o seguinte URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Se você solicitar uma imagem maior do que o tamanho original da imagem, Bing adiciona acolchoado branco em torno da imagem, conforme necessário. Por exemplo, se o tamanho original da imagem for 474x316 e se fixar `&w` em 500, Bing devolverá uma imagem de 500x333. Esta imagem terá 8,5 píxeis de estofamento branco ao longo das bordas superior e inferior, e 13 pixels de enchimento nas bordas esquerda e direita.

Para evitar que bing adicione estofamento branco se o tamanho solicitado for maior do que o tamanho original da imagem, desa ajuste o parâmetro de `&p` consulta para 0. Por exemplo, se incluir o `&p=0` parâmetro no URL acima, Bing devolverá uma imagem 474x316 em vez de uma imagem de 500x333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Se especificar ambos os `&w` parâmetros e `&h` consultar os parâmetros, Bing manterá o rácio de aspeto da imagem e adicionará o acolchoado branco conforme necessário. Por exemplo, se o tamanho original da imagem for de 474x316 e definir os parâmetros de largura e altura para 200x200 `&w=200&h=200` (), Bing devolve uma imagem que contém 33 pixels de estofamento branco na parte superior e inferior. Se incluir o `&p` parâmetro de consulta, Bing devolve uma imagem de 200x134.

## <a name="crop-a-thumbnail"></a>Cortar uma miniatura 

Para plantar uma imagem, inclua o `c` parâmetro de consulta (cultura). Pode utilizar os seguintes valores:
  
- `4`&mdash;Relação Cega  
- `7`&mdash;Relação Inteligente  

### <a name="smart-ratio-cropping"></a>Cultura de relação inteligente

Se solicitar a colheita da Relação Inteligente (definindo o `c` parâmetro `7` para), bing irá cortar uma imagem do centro da sua região de interesse para fora, mantendo ao mesmo tempo o rácio de aspeto da imagem. A região de interesse é a área da imagem que Bing determina que contém mais partes importadas. O que se segue mostra uma região de interesses exemplo.  
  
![Região de interesse](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Se redimensionar uma imagem e solicitar a colheita de Smart Ratio, Bing reduz a imagem para o tamanho solicitado, mantendo a relação de aspeto. Bing então planta a imagem com base nas dimensões redimensionadas. Por exemplo, se a largura redimensionada for inferior ou igual à altura, Bing cortará a imagem para a esquerda e direita do centro da região de interesse. Caso contrário, Bing irá ausenhá-lo para o topo e para o fundo do centro da região de interesse.  
  
 
O seguinte mostra a imagem reduzida para 200x200 usando a colheita smart ratio. Como Bing mede a imagem do canto superior esquerdo, a parte inferior da imagem é cortada. 
  
![Imagem de paisagem cortada a 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
O seguinte mostra a imagem reduzida para 200x100 usando a colheita da Smart Ratio. Como Bing mede a imagem do canto superior esquerdo, a parte inferior da imagem é cortada. 
   
![Imagem de paisagem cortada a 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
O seguinte mostra a imagem reduzida a 100x200 utilizando a cultura Smart Ratio. Como Bing mede a imagem do centro, as partes esquerda e direita da imagem são cortadas.
  
![Imagem de paisagem cortada a 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Se Bing não conseguir determinar a região de interesse da imagem, o serviço utilizará a cultura Blind Ratio.  

### <a name="blind-ratio-cropping"></a>Cultura de rácio cego

Se solicitar a colheita da Relação Cega (definindo o `c` parâmetro `4` para), Bing utiliza as seguintes regras para cortar a imagem.  
  
- Se `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` , a imagem é medida a partir do canto superior esquerdo e cortada na parte inferior.  
- Se, `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)` a imagem for medida a partir do centro e cortada para a esquerda e direita.  

O seguinte mostra uma imagem de retrato que é 225x300.  
  
![Imagem original do girassol](./media/resize-crop/bing-resize-crop-sunflower.png)
  
O seguinte mostra a imagem reduzida para 200x200 usando a colheita da Blind Ratio. A imagem é medida a partir do canto superior esquerdo, resultando na parte inferior da imagem a ser cortada.  
  
![Imagem de girassol cortada a 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
O seguinte mostra a imagem reduzida a 200x100 utilizando a colheita da Blind Ratio. A imagem é medida a partir do canto superior esquerdo, resultando na parte inferior da imagem a ser cortada.  
  
![Imagem de girassol cortada a 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
O seguinte mostra a imagem reduzida a 100x200 utilizando a colheita da Blind Ratio. A imagem é medida a partir do centro, resultando nas partes esquerda e direita da imagem cortadas.  
  
![Imagem de girassol cortada a 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Passos seguintes

* [Quais são as APIs de Pesquisa de Bing?](bing-api-comparison.md)
* [Requisitos de utilização e apresentação da API de Pesquisa do Bing](use-display-requirements.md)
