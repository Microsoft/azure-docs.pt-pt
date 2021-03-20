---
title: '| do módulo de ferramentas de desenho Microsoft Azure Maps'
description: Neste artigo, você vai aprender a definir dados de opções de desenho usando o Microsoft Azure Maps Web SDK
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 95a04d763fa5982181cc1c797bce969d9857ae4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890637"
---
# <a name="use-the-drawing-tools-module"></a>Utilizar o módulo de ferramentas de desenho

O Azure Maps Web SDK fornece um módulo de *ferramentas de desenho.* Este módulo facilita a desenhei e edita formas no mapa utilizando um dispositivo de entrada, como um rato ou um ecrã tátil. A classe central deste módulo é o [gestor de desenho.](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-) O gestor de desenho fornece todas as capacidades necessárias para desenhar e editar formas no mapa. Pode ser usado diretamente, e é integrado com uma barra de ferramentas personalizada UI. Também pode utilizar a aula de barra de [ferramentas](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) incorporada. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Carregar o módulo de ferramentas de desenho numa página web

1. Crie um novo ficheiro HTML e [implemente o mapa como de costume.](./how-to-use-map-control.md)
2. Carregue o módulo de ferramentas de desenho Azure Maps. Pode carregá-lo de duas maneiras:
    - Utilize a versão globalmente hospedada da Rede de Entrega de Conteúdos Azure do módulo de serviços Azure Maps. Adicione referência à folha de estilo JavaScript e CSS no `<head>` elemento do ficheiro:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Ou, você pode carregar o módulo de ferramentas de desenho para o código fonte Azure Maps Web SDK localmente, usando o pacote npm de [ferramentas de desenho de mapas azul-maps](https://www.npmjs.com/package/azure-maps-drawing-tools) e, em seguida, acolhê-lo com a sua app. Este pacote também inclui definições typeScript. Utilize este comando:
    
        > **npm instalar azure-maps-drawing-tools**
    
        Em seguida, adicione uma referência à folha de estilo JavaScript e CSS no `<head>` elemento do ficheiro:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Use o gestor de desenho diretamente

Uma vez que o módulo de ferramentas de desenho é carregado na sua aplicação, pode ativar as capacidades de desenho e edição utilizando o [gestor de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-). Pode especificar opções para o gestor de desenho durante a instantânea ou utilizar a `drawingManager.setOptions()` função em alternativa.

### <a name="set-the-drawing-mode"></a>Definir o modo de desenho

O código a seguir cria uma instância do gestor de desenho e define a opção **de modo** de desenho. 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

O código abaixo é um exemplo completo de como definir um modo de desenho do gestor de desenho. Clique no mapa para começar a desenhar um polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenhe um polígono" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a Pen <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Draw a polygon</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Definir o tipo de interação

O gestor de desenho suporta três maneiras diferentes de interagir com o mapa para desenhar formas.

* `click` - As coordenadas são adicionadas quando o rato ou o toque são clicados.
* `freehand ` - As coordenadas são adicionadas quando o rato ou o toque são arrastados no mapa. 
* `hybrid` - As coordenadas são adicionadas quando o rato ou o toque são clicados ou arrastados.

O código a seguir permite o modo de desenho do polígono e define o tipo de interação de desenho a que o gestor de desenho deve aderir `freehand` . 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Esta amostra de código implementa a funcionalidade de desenhar um polígono no mapa. Basta segurar o botão do rato esquerdo e arrastá-lo, livremente.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenho de mão livre" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>desenho pen free-hand</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Personalizar opções de desenho

Os exemplos anteriores demonstraram como personalizar opções de desenho enquanto instantaneamente o Gestor de Desenho. Também pode definir as opções de Gestor de Desenho utilizando a `drawingManager.setOptions()` função. Abaixo está uma ferramenta para testar a personalização de todas as opções para o gestor de desenho usando a função setOptions.

<br/>

<iframe height="685" title="Personalizar gestor de desenho" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>dados</a> de forma pen obter por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar características adicionais do módulo de ferramentas de desenho:

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
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Gestor de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Barra de ferramentas de desenho](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)