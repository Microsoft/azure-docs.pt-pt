---
title: Módulo de ferramentas de desenho [ Microsoft Azure Maps
description: Neste artigo, você vai aprender a definir dados de opções de desenho usando o Microsoft Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334301"
---
# <a name="use-the-drawing-tools-module"></a>Utilizar o módulo de ferramentas de desenho

O Azure Maps Web SDK fornece um módulo de ferramentas de *desenho.* Este módulo facilita a elaboração e edição de formas no mapa utilizando um dispositivo de entrada, como um rato ou ecrã tátil. A classe central deste módulo é o gestor de [desenho.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) O gestor de desenho fornece todas as capacidades necessárias para desenhar e editar formas no mapa. Pode ser usado diretamente, e é integrado com uma barra de ferramentas personalizada UI. Também pode utilizar a aula de barra de [ferramentas](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) de desenho incorporada. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Carregar o módulo de ferramentas de desenho numa página web

1. Crie um novo ficheiro HTML e [implemente o mapa como de costume.](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)
2. Carregue o módulo de ferramentas de desenho Do Azure Maps. Pode carregá-lo de duas maneiras:
    - Utilize a versão da Rede de Entrega de Conteúdos Azure, a nível global, do módulo de serviços Azure Maps. Adicione referência à folha de estilo JavaScript `<head>` e CSS no elemento do ficheiro:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Ou, pode carregar o módulo de ferramentas de desenho para o código fonte Azure Maps Web SDK localmente, utilizando o pacote npm de ferramentas de desenho de [mapas azuis](https://www.npmjs.com/package/azure-maps-drawing-tools) e, em seguida, acolhê-lo com a sua app. Este pacote também inclui definições de TypeScript. Utilize este comando:
    
        > **npm instalar azure-maps-drawing-tools**
    
        Em seguida, adicione uma referência à folha de `<head>` estilo JavaScript e CSS no elemento do ficheiro:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Use o gestor de desenho diretamente

Uma vez que o módulo de ferramentas de desenho é carregado na sua aplicação, pode ativar as capacidades de desenho e edição utilizando o gestor de [desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Pode especificar opções para o gestor de desenho enquanto `drawingManager.setOptions()` o instantaneamente ou utilizar a função em alternativa.

### <a name="set-the-drawing-mode"></a>Definir o modo de desenho

O código seguinte cria uma instância do gestor de desenho e define a **opção** modo de desenho. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

O código abaixo é um exemplo completo de como definir um modo de desenho do gestor de desenho. Clique no mapa para começar a desenhar um polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenhe um polígono" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a Caneta <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Desenhar um polígono</a> por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Definir o tipo de interação

O gestor de desenho suporta três formas diferentes de interagir com o mapa para desenhar formas.

* `click`- As coordenadas são adicionadas quando o rato ou o toque são clicados.
* `freehand `- São adicionadas coordenadas quando o rato ou o toque são arrastados no mapa. 
* `hybrid`- As coordenadas são adicionadas quando o rato ou o toque são clicados ou arrastados.

O seguinte código permite o modo de desenho do polígono e define `freehand`o tipo de interação de desenho a que o gestor de desenho deve aderir . 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Esta amostra de código implementa a funcionalidade de desenhar um polígono no mapa. Basta segurar o botão do rato esquerdo e arrastá-lo, livremente.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenho de mão livre" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>desenho de mãos</a> livres<a href='https://codepen.io/azuremaps'>@azuremaps</a>da Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Personalização de opções de desenho

Os exemplos anteriores demonstraram como personalizar as opções de desenho enquanto instantaneamente o Gestor de Desenho. Também pode definir as opções `drawingManager.setOptions()` do Gestor de Desenho utilizando a função. Abaixo está uma ferramenta para testar a personalização de todas as opções para o gestor de desenho usando a função setOptions.

<br/>

<iframe height="685" title="Personalizar gestor de desenho" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consulte os dados de <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>forma</a> da<a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen Get por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Aprenda a utilizar funcionalidades adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Adicionar uma barra de ferramentas de desenho](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Obter dados da forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagir a eventos de desenho](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gestor de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra de ferramentas de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
