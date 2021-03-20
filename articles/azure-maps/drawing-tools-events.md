---
title: Eventos de ferramentas de desenho | Microsoft Azure Maps
description: Neste artigo você vai aprender, como adicionar uma barra de ferramentas de desenho a um mapa usando Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6abe0ed88adbdf8263aa27d340fb2fff156d98e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90089348"
---
# <a name="drawing-tool-events"></a>Eventos de ferramentas de desenho

Ao usar ferramentas de desenho num mapa, é útil reagir a certos eventos à medida que o utilizador se desenha no mapa. Esta tabela lista todos os eventos apoiados pela `DrawingManager` classe.

| Evento | Description |
|-------|-------------|
| `drawingchanged` | Disparado quando qualquer coordenada de forma foi adicionada ou alterada. | 
| `drawingchanging` | Disparado quando estiver a ser apresentada qualquer coordenada de pré-visualização para uma forma. Por exemplo, este evento disparará várias vezes à medida que uma coordenada é arrastada. | 
| `drawingcomplete` | Disparado quando uma forma terminar de ser desenhada ou retirada do modo de edição. |
| `drawingmodechanged` | Disparado quando o modo de desenho tiver mudado. O novo modo de desenho é passado para o manipulador de eventos. |
| `drawingstarted` | Disparado quando o utilizador começa a desenhar uma forma ou coloca uma forma no modo de edição.  |

O código que se segue mostra como funcionam os eventos no módulo Ferramentas de Desenho. Desenhe formas no mapa e assista como os eventos disparam.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventos de ferramentas de desenho" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte os eventos de <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>ferramentas de desenho</a> de canetas por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Exemplos

Vamos ver alguns cenários comuns que usam os eventos de ferramentas de desenho.

### <a name="select-points-in-polygon-area"></a>Selecione pontos na área do polígono

Este código demonstra como monitorizar um evento de formas de desenho do utilizador. Para este exemplo, o código monitoriza formas de polígonos, retângulos e círculos. Em seguida, determina quais os pontos de dados no mapa dentro da área desenhada. O `drawingcomplete` evento é usado para desencadear a lógica selecionada. Na lógica selecionada, o código passa por todos os pontos de dados do mapa. Verifica se há uma intersecção entre o ponto e a área da forma desenhada. Este exemplo faz uso da biblioteca [ deTurf.js](https://turfjs.org/) de código aberto para realizar um cálculo de intersecção espacial.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Selecione dados na área do polígono desenhado" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte os dados pen <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Select na área de polígono desenhado</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Desenhar e pesquisar na área do polígono

Este código procura pontos de interesse dentro da área de uma forma após o utilizador terminar de desenhar a forma. Pode modificar e executar o código clicando em 'Editar na caneta Código' no canto superior direito da moldura. O `drawingcomplete` evento é usado para desencadear a lógica de pesquisa. Se o utilizador desenhar um retângulo ou polígono, é realizada uma busca dentro da geometria. Se um círculo for desenhado, o raio e a posição central são utilizados para executar um ponto de pesquisa de interesse. O `drawingmodechanged` evento é utilizado para determinar quando o utilizador muda para o modo de desenho, e este evento limpa a tela de desenho.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenhar e pesquisar na área do polígono" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o Pen <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Draw e procure na área do polígono</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Criar uma ferramenta de medição

O código abaixo mostra como os eventos de desenho podem ser usados para criar uma ferramenta de medição. É `drawingchanging` usado para monitorizar a forma, enquanto está a ser desenhado. À medida que o utilizador move o rato, as dimensões da forma são calculadas. O `drawingcomplete` evento é usado para fazer um cálculo final sobre a forma depois de ter sido desenhado. O `drawingmodechanged` evento é utilizado para determinar quando o utilizador está a mudar para um modo de desenho. Além disso, o  `drawingmodechanged` evento limpa a tela de desenho e limpa informações antigas de medição.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ferramenta de medição" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>ferramenta de medição de canetas</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar características adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Obter dados da forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre o módulo Serviços:

> [!div class="nextstepaction"]
> [Módulo de serviços](how-to-use-services-module.md)

Confira mais amostras de código:

> [!div class="nextstepaction"]
> [Página de amostra de código](https://aka.ms/AzureMapsSamples)
