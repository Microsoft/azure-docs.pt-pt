---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868986"
---
Algumas respostas do Bing incluem URLs para imagens em miniatura servidas pelo Bing. Você pode redimensionar e cortar as imagens em miniatura. 

> [!NOTE]
> Certifique-se de que o tamanho e o corte da miniatura forneçam um cenário de pesquisa e respeite os direitos de terceiros, conforme exigido pelo Pesquisa do Bing uso da API e requisitos de exibição.


Para redimensionar uma imagem, inclua o parâmetro de consulta w (largura), o parâmetro de consulta h (Height) ou ambos na URL da miniatura. Especifique a largura e a altura em pixels. Por exemplo:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Se você especificar apenas a largura ou apenas o parâmetro de consulta de altura, o Bing manterá a taxa de proporção da imagem. Se você especificar largura e altura e não mantiver a taxa de proporção original da imagem, o Bing adicionará o preenchimento branco à borda da imagem. Por exemplo, se você redimensionar uma imagem 480x359 para 200 x 200 sem corte, a largura inteira conterá a imagem, mas a altura conterá 25 pixels de preenchimento branco na parte superior e inferior da imagem. O mesmo seria verdadeiro se a imagem fosse 359x480, exceto que as bordas esquerda e direita conteriam preenchimento branco. Se você cortar a imagem, o preenchimento branco não será adicionado.  

 
A figura a seguir mostra o tamanho original de uma imagem em miniatura (480X300).  
  
![Imagem de paisagem original](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
A imagem a seguir mostra a imagem redimensionada para 200 x 200. A taxa de proporção é mantida e as bordas superior e inferior são preenchidas com branco (a borda preta é incluída para mostrar o preenchimento).  
  
![Imagem de paisagem redimensionada](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Se você especificar dimensões que sejam maiores que a largura e a altura originais da imagem, a imagem será preenchida com branco nas bordas esquerda e superior.  
  
Para cortar uma imagem, inclua o parâmetro de consulta c (cortar). A seguir estão os possíveis valores que você pode especificar.  
  
- 4&mdash;proporção de cego  
- 7&mdash;proporção inteligente  
  
Se você solicitar o corte de taxa inteligente (c = 7), a imagem será cortada do centro da região de interesse da imagem para fora, mantendo a taxa de proporção da imagem. A região de interesse é a área da imagem que o Bing determina contém a maioria das partes de importação. Veja a seguir uma região de exemplo de interesse.  
  
![Região de interesse](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Se você redimensionar uma imagem e solicitar um corte de taxa inteligente, a imagem será reduzida para o tamanho solicitado, mantendo a taxa de proporção. A imagem é cortada com base nas dimensões redimensionadas. Por exemplo, se a largura redimensionada for menor ou igual à altura, a imagem será cortada à esquerda e à direita do centro da região de interesse. Caso contrário, a imagem será cortada na parte superior e inferior do centro da região de interesse.  
  
 
O seguinte mostra a imagem reduzida para 200 x 200 usando o corte de taxa inteligente.  
  
![Imagem em paisagem cortada para 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
O seguinte mostra a imagem reduzida para 200 x 100 usando o corte de taxa inteligente.  
   
![Imagem em paisagem cortada para 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
O seguinte mostra a imagem reduzida para 100 x 200 usando o corte de taxa inteligente.  
  
![Imagem em paisagem cortada para 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Se o Bing não puder determinar a região de interesse da imagem, o Bing usará o corte da taxa de branco.  
  
Se você solicitar o corte de taxa oculta (c = 4), o Bing usará as regras a seguir para cortar a imagem.  
  
- Se (largura da imagem original/altura da imagem original) < (largura da imagem solicitada/altura da imagem solicitada), a imagem será medida do canto superior esquerdo e cortada na parte inferior.  
- Se (largura da imagem original/altura da imagem original) > (largura da imagem solicitada/altura da imagem solicitada), a imagem será medida do centro e cortada para a esquerda e para a direita.  



O seguinte mostra uma imagem de retrato 225x300.  
  
![Imagem girassol original](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
O a seguir mostra a imagem reduzida para 200 x 200 usando o corte de taxa oculta. A imagem é medida a partir do canto superior esquerdo, resultando na parte inferior da imagem que está sendo cortada.  
  
![Imagem girassol cortada para 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
O a seguir mostra a imagem reduzida para 200 x 100 usando o corte de taxa oculta. A imagem é medida a partir do canto superior esquerdo, resultando na parte inferior da imagem que está sendo cortada.  
  
![Imagem girassol cortada para 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
O a seguir mostra a imagem reduzida para 100 x 200 usando o corte de taxa oculta. A imagem é medida a partir do centro, resultando nas partes esquerda e direita da imagem que está sendo cortada.  
  
![Imagem girassol cortada para 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

