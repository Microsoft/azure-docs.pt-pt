---
title: Tipos de interação das ferramentas de desenho e atalhos de teclado no Azure Maps | Microsoft Docs
description: Como desenhar e editar formas usando um mouse, tela sensível ao toque ou teclado no SDK da Web do Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8385f294426f6d793f7b539006eafd0b2cb940ea
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481948"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Tipos de interação e atalhos de teclado no módulo ferramentas de desenho

Este artigo descreve todas as diferentes maneiras de desenhar e editar formas em um mapa usando um mouse, uma tela sensível ao toque ou atalhos de teclado.

O Gerenciador de desenho dá suporte a três maneiras diferentes de interagir com o mapa para desenhar formas.

* as coordenadas de `click` são adicionadas quando o mouse ou toque é clicado.
* as coordenadas de `freehand ` são adicionadas quando o mouse ou toque é arrastado no mapa. 
* as coordenadas de `hybrid` são adicionadas quando o mouse ou toque é clicado ou arrastado.

## <a name="how-to-draw-shapes"></a>Como desenhar formas

O seguinte descreve todas as diferentes maneiras pelas quais as formas podem ser desenhadas no mapa. Antes que qualquer forma possa ser desenhada, a opção `drawingMode` do Gerenciador de desenho precisa ser definida como uma configuração de desenho com suporte. Isso pode ser feito programaticamente ou pressionando um dos botões de desenho na barra de ferramentas. O modo de desenho permanece habilitado, mesmo depois que uma forma é desenhada, facilitando o desenho de formas adicionais do mesmo tipo. O modo de desenho pode ser colocado em um estado ocioso programaticamente ou clicando no botão modos de desenho atuais na barra de ferramentas. 

### <a name="how-to-draw-a-point"></a>Como desenhar um ponto

Quando o Gerenciador de desenho está no modo de desenho `draw-point`, as ações a seguir podem ser feitas para desenhar pontos no mapa. Esses métodos funcionam com todos os modos de interação.

**Iniciar desenho**
 - Clique no botão esquerdo do mouse ou toque no mapa para adicionar um ponto ao mapa. 
 - Se o mouse estiver sobre o mapa, pressione a tecla `F` e um ponto será adicionado usando a coordenada de onde está o ponteiro do mouse. Isso fornecerá um método de maior precisão para adicionar um ponto ao mapa, pois haverá menos movimento no mouse devido ao pressionamento do botão esquerdo do mouse.
 - Continue clicando, tocando ou pressionando `F` para adicionar mais pontos ao mapa.
 
**Concluir desenho**
 - Clique em qualquer botão na barra de ferramentas de desenho. 
 - Defina programaticamente o modo de desenho. 
 - Pressione a tecla `C`.

**Cancelar desenho**
 - Pressione a tecla `Escape`.

### <a name="how-to-draw-a-line"></a>Como desenhar uma linha

Quando o Gerenciador de desenho está no modo de `draw-line`, as ações a seguir podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação definido como.

**Iniciar desenho**
 - Clique no modo
   * Clique no botão esquerdo do mouse ou toque no mapa para adicionar cada ponto de uma linha no mapa. Uma coordenada é adicionada à linha para cada clique/toque. 
   * Se o mouse estiver sobre o mapa, pressione a tecla `F` e um ponto será adicionado usando a coordenada de onde está o ponteiro do mouse. Isso fornecerá um método de maior precisão para adicionar um ponto ao mapa, pois haverá menos movimento no mouse devido ao pressionamento do botão esquerdo do mouse.
   * Continue clicando até que todos os pontos desejados tenham sido adicionados à linha.
 - Modo de FreeHand
   * Pressione o botão esquerdo do mouse ou toque no mapa e arraste o mouse ou o ponto de toque ao lado. As coordenadas são adicionadas à linha à medida que o mouse ou o ponto de toque se move em torno do mapa. Assim que o evento de mouse ou de toque é disparado, o desenho é concluído. A frequência na qual as coordenadas são adicionadas é definida pela opção gerenciadores de desenho `freehandInterval`.
 - Modo híbrido
   * Alterne entre os métodos Click e FreeHand conforme desejado ao desenhar uma única linha. Por exemplo, clique em alguns pontos, mantenha pressionado e arraste o mouse para adicionar vários pontos e clique em mais alguns. 

**Concluir desenho**
 - Modo híbrido/clique
   * Clique duas vezes no mapa no último ponto. 
   * Clique em qualquer botão na barra de ferramentas de desenho. 
   * Defina programaticamente o modo de desenho. 
 - Modo de FreeHand
   * Solte o botão do mouse ou o ponto de toque.
 - Pressione a tecla `C`.

**Cancelar desenho**
 - Pressione a tecla `Escape`.

### <a name="how-to-draw-a-polygon"></a>Como desenhar um polígono

Quando o Gerenciador de desenho está no modo de `draw-polygon`, as ações a seguir podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação definido como.

**Iniciar desenho**
 - Clique no modo
   * Clique no botão esquerdo do mouse ou toque no mapa para adicionar cada ponto de um polígono no mapa. Uma coordenada é adicionada ao polígono para cada clique/toque. 
   * Se o mouse estiver sobre o mapa, pressione a tecla `F` e um ponto será adicionado usando a coordenada de onde está o ponteiro do mouse. Isso fornecerá um método de maior precisão para adicionar um ponto ao mapa, pois haverá menos movimento no mouse devido ao pressionamento do botão esquerdo do mouse.
   * Continue clicando até que todos os pontos desejados tenham sido adicionados ao polígono.
 - Modo de FreeHand
   * Pressione o botão esquerdo do mouse ou toque no mapa e arraste o mouse ou o ponto de toque ao lado. As coordenadas são adicionadas ao polígono à medida que o mouse ou o ponto de toque se move em volta do mapa. Assim que o evento de mouse ou de toque é disparado, o desenho é concluído. Observe que a frequência na qual as coordenadas são adicionadas é definida pela opção gerenciadores de desenho `freehandInterval`.
 - Modo híbrido
   * Alterne entre os métodos Click e FreeHand conforme desejado ao desenhar um único polígono. Por exemplo, clique em alguns pontos, mantenha pressionado e arraste o mouse para adicionar vários pontos e clique em mais alguns. 

**Concluir desenho**
 - Modo híbrido/clique
   * Clique duas vezes no mapa no último ponto. 
   * Clique no primeiro ponto no polígono.
   * Clique em qualquer botão na barra de ferramentas de desenho. 
   * Defina programaticamente o modo de desenho. 
 - Modo de FreeHand
   * Solte o botão do mouse ou o ponto de toque.
 - Pressione a tecla `C`.

**Cancelar desenho**
 - Pressione a tecla `Escape`.

### <a name="how-to-draw-a-rectangle"></a>Como desenhar um retângulo

Quando o Gerenciador de desenho está no modo de `draw-rectangle`, as ações a seguir podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação definido como. A forma gerada seguirá a [especificação geojson estendida para retângulos](extend-geojson.md#rectangle).

**Iniciar desenho**
 - Pressione o botão esquerdo do mouse ou toque no mapa para adicionar o primeiro canto do retângulo e arraste para criar o retângulo. 

**Concluir desenho**
 - Solte o botão do mouse ou o ponto de toque.
 - Defina programaticamente o modo de desenho. 
 - Pressione a tecla `C`.

**Cancelar desenho**
 - Pressione a tecla `Escape`.

### <a name="how-to-draw-a-circle"></a>Como desenhar um círculo

Quando o Gerenciador de desenho está no modo de `draw-circle`, as ações a seguir podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação definido como. A forma gerada seguirá a [especificação geojson estendida para círculos](extend-geojson.md#circle).

**Iniciar desenho**
 - Pressione o botão esquerdo do mouse ou toque no mapa para adicionar o centro do círculo e arraste dar aos círculos um raio. 

**Concluir desenho**
 - Solte o botão do mouse ou o ponto de toque.
 - Defina programaticamente o modo de desenho. 
 - Pressione a tecla `C`.

**Cancelar desenho**
 - Pressione a tecla `Escape`.

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

As ferramentas de desenho dão suporte a atalhos de teclado que facilitam desenhar e editar formas no mapa. Esses atalhos de teclado são funcionais quando o mapa tem foco.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `C` | Conclui qualquer desenho em andamento e define o modo de desenho como ocioso. O foco será movido para o elemento de mapa de nível superior.  |
| `Escape` | Cancela qualquer desenho em andamento e define o modo de desenho como ocioso. O foco será movido para o elemento de mapa de nível superior.  |
| `F` | Adiciona uma coordenada a um ponto, linha ou polígono se o mouse estiver sobre o mapa. Ação equivalente de clicar no mapa quando estiver no modo de clique ou híbrido. Esse atalho permite desenhos mais precisos e mais rápidos, pois você pode usar uma mão para posicionar o mouse e outro para pressionar o botão sem que o mouse se mova do gesto de imprensa. |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes no módulo ferramentas de desenho:

> [!div class="nextstepaction"]
> [Gerenciador de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra de ferramentas desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)