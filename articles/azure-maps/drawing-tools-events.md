---
title: Adicione uma barra de ferramentas de desenho a um mapa Microsoft Azure Maps
description: Neste artigo você vai aprender, como adicionar uma barra de ferramentas de desenho a um mapa usando o Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d8509af7829910bdda8bba3d63553e83626fe784
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804678"
---
# <a name="drawing-tool-events"></a>Eventos de ferramentas de desenho

Ao usar ferramentas de desenho num mapa, é útil reagir a certos eventos à medida que o utilizador se baseia no mapa. Esta tabela lista todos os eventos apoiados pela `DrawingManager` classe.

| Evento | Descrição |
|-------|-------------|
| `drawingchanged` | Disparado quando qualquer coordenada em forma foi adicionada ou alterada. | 
| `drawingchanging` | Disparado quando está a ser apresentada qualquer coordenada de pré-visualização para uma forma. Por exemplo, este evento irá disparar várias vezes à medida que uma coordenada é arrastada. | 
| `drawingcomplete` | Disparado quando uma forma termina de ser desenhada ou retirada do modo de edição. |
| `drawingmodechanged` | Disparado quando o modo de desenho mudou. O novo modo de desenho é passado para o manipulador de eventos. |
| `drawingstarted` | Disparado quando o utilizador começa a desenhar uma forma ou coloca uma forma no modo de edição.  |

O código que se segue mostra como funcionam os eventos no módulo Desagregação de Ferramentas. Desenhe formas no mapa e veja como os eventos disparam.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenhar ferramentas eventos" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja os eventos de ferramentas<a href='https://codepen.io/azuremaps'>@azuremaps</a>de desenho de <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>canetas</a> do Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Exemplos

Vamos ver alguns cenários comuns que usam os eventos de ferramentas de desenho.

### <a name="select-points-in-polygon-area"></a>Selecione pontos na área do polígono

Este código demonstra como monitorizar um evento de formas de desenho do utilizador. Para este exemplo, o código monitoriza formas de polígonos, retângulos e círculos. Em seguida, determina quais os pontos de dados no mapa estão dentro da área desenhada. O `drawingcomplete` evento é usado para desencadear a lógica selecionada. Na lógica selecionada, o código passa por todos os pontos de dados no mapa. Verifica se há uma intersecção do ponto e a área da forma desenhada. Este exemplo faz uso da biblioteca [Turf.js](https://turfjs.org/) de código aberto para realizar um cálculo de intersecção espacial.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Selecione dados na área do polígono desenhado" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte os dados pen <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Select na área de polígono desenhada</a> por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Desenhe e procure na área do polígono

Este código procura pontos de interesse dentro da área de uma forma após o utilizador terminar de desenhar a forma. Pode modificar e executar o código clicando em 'Editar em Código' no canto superior direito da moldura. O `drawingcomplete` evento é usado para desencadear a lógica de pesquisa. Se o utilizador desenhar um retângulo ou polígono, é realizada uma pesquisa no interior da geometria. Se um círculo for desenhado, o raio e a posição central são utilizados para realizar um ponto de pesquisa de interesse. O `drawingmodechanged` evento é utilizado para determinar quando o utilizador muda para o modo de desenho, e este evento limpa a tela de desenho.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenhe e procure na área do polígono" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o Pen Draw e procure na área<a href='https://codepen.io/azuremaps'>@azuremaps</a>do <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>polígono</a> por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Criar uma ferramenta de medição

O código abaixo mostra como os eventos de desenho podem ser usados para criar uma ferramenta de medição. O `drawingchanging` é usado para monitorizar a forma, como está sendo desenhado. À medida que o utilizador move o rato, as dimensões da forma são calculadas. O `drawingcomplete` evento é usado para fazer um cálculo final sobre a forma depois de desenhado. O `drawingmodechanged` evento é utilizado para determinar quando o utilizador está a mudar para um modo de desenho. Além `drawingmodechanged` disso, o evento limpa a tela de desenho e limpa informações antigas de medição.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ferramenta de medição" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a ferramenta de <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>medição</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>da caneta por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Passos seguintes

Aprenda a utilizar funcionalidades adicionais do módulo de ferramentas de desenho:

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
