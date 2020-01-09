---
title: Adicionar uma barra de ferramentas de desenho ao Azure Maps | Microsoft Docs
description: Como adicionar uma barra de ferramentas de desenho a um mapa usando o SDK da Web do Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 63848ecf92daecf28f6e0046e87da15355aa35c1
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561835"
---
# <a name="drawing-tool-events"></a>Eventos da ferramenta de desenho

Ao usar ferramentas de desenho em um mapa, geralmente é útil reagir a determinados eventos à medida que o usuário desenha no mapa. A tabela a seguir lista todos os eventos com suporte pela classe `DrawingManager`.

| Evento | Descrição |
|-------|-------------|
| `drawingchanged` | Acionado quando qualquer coordenada em uma forma tiver sido adicionada ou alterada. | 
| `drawingchanging` | Acionado quando qualquer coordenada de visualização para uma forma está sendo exibida. Por exemplo, serão disparados várias vezes à medida que uma coordenada for arrastada. | 
| `drawingcomplete` | Acionado quando uma forma termina de ser desenhada ou retirada do modo de edição. |
| `drawingmodechanged` | Acionado quando o modo de desenho é alterado. O novo modo de desenho é passado para o manipulador de eventos. |
| `drawingstarted` | Acionado quando o usuário começa a desenhar uma forma ou coloca uma forma no modo de edição.  |

O código a seguir mostra como os eventos no módulo ferramentas de desenho funcionam. Desenhe formas no mapa e assista à medida que os eventos são disparados.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventos de ferramentas de desenho" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte os <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>eventos das ferramentas de desenho</a> de caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Exemplos

Veja a seguir exemplos de alguns cenários comuns que usam os eventos de ferramentas de desenho.

### <a name="select-points-in-polygon-area"></a>Selecionar pontos na área de polígono

O código a seguir mostra como monitorar o desenho de formas que representam áreas de polígono (polígonos, retângulos e círculos) e determinar quais pontos de dados no mapa estão dentro da área de desenho. O evento `drawingcomplete` é usado para disparar a lógica SELECT. Na lógica Select, todos os pontos de dados no mapa são iterados e testados para interseção com a área de polígono da forma desenhada. Este exemplo usa a biblioteca [Turf. js](https://turfjs.org/) de software livre para executar um cálculo de interseção espacial.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Selecionar dados na área de polígono desenhada" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/XWJdeja'>selecionar dados em área de polígono desenhada</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Desenhar e pesquisar na área do polígono

O código a seguir mostra como executar uma pesquisa por pontos de interesse dentro de uma área de forma depois que o usuário termina de desenhar a forma. O evento `drawingcomplete` é usado para disparar a lógica de pesquisa. Se o usuário desenha um retângulo ou polígono, uma pesquisa dentro de geometry é executada. Se um círculo for desenhado, a posição RADIUS e Center será usada para executar uma pesquisa de ponto de interesse. O evento `drawingmodechanged` é usado para determinar quando o usuário está alternando para um modo de desenho e limpa a tela de desenho.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenhar e pesquisar na área do polígono" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a caneta <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>desenhar e pesquisar na área de polígono</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Criar uma ferramenta de medição

O código a seguir mostra como os eventos de desenho podem ser usados para criar uma ferramenta de medição. O `drawingchanging` é usado para monitorar a forma à medida que ela está sendo desenhada. À medida que o usuário move o mouse, as dimensões da forma são calculadas. O evento `drawingcomplete` é usado para fazer um cálculo final na forma depois que ele é desenhado. O evento `drawingmodechanged` é usado para determinar quando o usuário está alternando para um modo de desenho e limpa a tela de desenho e as informações de medição antigas.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ferramenta de medição" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>ferramenta de medição</a> de caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Passos seguintes

Saiba como usar recursos adicionais do módulo ferramentas de desenho:

> [!div class="nextstepaction"]
> [Obter dados da forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre o módulo de serviços:

> [!div class="nextstepaction"]
> [Módulo de serviços](how-to-use-services-module.md)

Confira mais exemplos de código:

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
