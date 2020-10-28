---
title: Tipos de interação de ferramentas de desenho e atalhos de teclado no mapa Microsoft Azure Maps
description: Como desenhar e editar formas usando um rato, ecrã táctil ou teclado no Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b609a21348222150faf5fb828f2cc38ca6658f5d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895822"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Tipos de interação e atalhos de teclado no módulo de ferramentas de desenho

Este artigo descreve todas as diferentes formas de desenhar e editar formas usando um rato, ecrã tátil ou atalhos de teclado.

O gestor de desenho suporta três maneiras diferentes de interagir com o mapa, para desenhar formas.

* `click` - As coordenadas são adicionadas quando o rato ou o toque são clicados.
* `freehand ` - As coordenadas são adicionadas quando o rato ou o toque são arrastados no mapa.
* `hybrid` - As coordenadas são adicionadas quando o rato ou o toque são clicados ou arrastados.

## <a name="how-to-draw-shapes"></a>Como desenhar formas

 Antes de qualquer forma ser desenhada, defina a `drawingMode` opção do gestor de desenho para uma definição de desenho suportada. Esta definição pode ser programada ou invocada premindo um dos botões de desenho na barra de ferramentas. O modo de desenho permanece ativado, mesmo depois de ter sido desenhada uma forma, facilitando a escolha de formas adicionais do mesmo tipo. Programáticamente definir o modo de desenho para um estado ocioso. Ou, mude para um estado ocioso clicando no botão de modos de desenho atual na barra de ferramentas.

As próximas secções delineiam todas as diferentes formas que as formas podem ser desenhadas no mapa.

### <a name="how-to-draw-a-point"></a>Como desenhar um ponto

Quando o gestor de desenho está em `draw-point` modo de desenho, podem ser feitas as seguintes ações para desenhar pontos no mapa. Estes métodos funcionam com todos os modos de interação.

**Comece a desenhar**
 - Clique no botão do rato esquerdo ou toque no mapa para adicionar um ponto ao mapa. 
 - Se o rato estiver sobre o mapa, prima a `F` tecla e um ponto será adicionado na coordenada do ponteiro do rato. Este método proporciona uma maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no rato devido ao movimento de pressão do botão do rato esquerdo.
 - Continue a clicar, a tocar ou a pressionar `F` para adicionar mais pontos ao mapa.
 
**Acabamento desenho**
 - Clique em qualquer botão na barra de ferramentas de desenho. 
 - Programáticamente definir o modo de desenho. 
 - Pressione a `C` chave.

**Cancelar desenho**
 - Pressione a `Escape` chave.

### <a name="how-to-draw-a-line"></a>Como desenhar uma linha

Quando o gestor de desenho está em `draw-line` modo, podem ser feitas as seguintes ações para desenhar pontos no mapa, dependendo do modo de interação.

**Comece a desenhar**
 - Clique no modo
   * Clique no botão do rato esquerdo ou toque no mapa para adicionar cada ponto de uma linha no mapa. Uma coordenada é adicionada à linha para cada clique ou toque. 
   * Se o rato estiver sobre o mapa, prima a `F` tecla e um ponto será adicionado na coordenada do ponteiro do rato. Este método proporciona uma maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no rato devido ao movimento de pressão do botão do rato esquerdo.
   * Continue a clicar até que todos os pontos desejados tenham sido adicionados à linha.
 - Modo freehand
   * Pressione o botão do rato esquerdo, ou toque no mapa e arraste o rato, ou toque o ponto de toque ao redor. As coordenadas são adicionadas à linha à medida que o rato ou o ponto de toque se movem em torno do mapa. Assim que o rato ou o evento de retoque são acionados, o desenho é concluído. A frequência em que as coordenadas são adicionadas é definida pela opção de gestores de `freehandInterval` desenho.
 - Modo híbrido
   * Alterne entre os métodos de clique e freehand, conforme desejado, enquanto desenha uma única linha. Por exemplo, clique em alguns pontos, em seguida, segure e arraste o rato para adicionar um monte de pontos, em seguida, clique em mais alguns. 

**Acabamento desenho**
 - Modo híbrido/clique
   * Clique duas vezes no mapa no último ponto. 
   * Clique em qualquer botão na barra de ferramentas de desenho. 
   * Programáticamente definir o modo de desenho. 
 - Modo freehand
   * Liberte o botão do rato ou o ponto de toque.
 - Pressione a `C` chave.

**Cancelar desenho**
 - Pressione a `Escape` chave.

### <a name="how-to-draw-a-polygon"></a>Como desenhar um polígono

Quando o gestor de desenho está em `draw-polygon` modo, podem ser feitas as seguintes ações para desenhar pontos no mapa, dependendo do modo de interação.

**Comece a desenhar**
 - Clique no modo
   * Clique no botão do rato esquerdo ou toque no mapa para adicionar cada ponto de um polígono no mapa. Uma coordenada é adicionada ao polígono para cada clique ou toque. 
   * Se o rato estiver sobre o mapa, prima a `F` tecla e um ponto será adicionado na coordenada do ponteiro do rato. Este método proporciona uma maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no rato devido ao movimento de pressão do botão do rato esquerdo.
   * Continue a clicar até que todos os pontos desejados tenham sido adicionados ao polígono.
 - Modo freehand
   * Pressione o botão do rato esquerdo, ou toque no mapa e arraste o rato, ou toque o ponto de toque ao redor. As coordenadas são adicionadas ao polígono à medida que o rato ou o ponto de toque se movem em torno do mapa. Assim que o rato ou o evento de retoque são acionados, o desenho é concluído. A frequência em que as coordenadas são adicionadas é definida pela opção de gestores de `freehandInterval` desenho.
 - Modo híbrido
   * Alterne entre os métodos de clique e freehand, conforme desejado, enquanto desenha um único polígono. Por exemplo, clique em alguns pontos, em seguida, segure e arraste o rato para adicionar um monte de pontos, em seguida, clique em mais alguns. 

**Acabamento desenho**
 - Modo híbrido/clique
   * Clique duas vezes no mapa no último ponto. 
   * Clique no primeiro ponto do polígono.
   * Clique em qualquer botão na barra de ferramentas de desenho. 
   * Programáticamente definir o modo de desenho. 
 - Modo freehand
   * Liberte o botão do rato ou o ponto de toque.
 - Pressione a `C` chave.

**Cancelar desenho**
 - Pressione a `Escape` chave.

### <a name="how-to-draw-a-rectangle"></a>Como desenhar um retângulo

Quando o gestor de desenho está em `draw-rectangle` modo, podem ser feitas as seguintes ações para desenhar pontos no mapa, dependendo do modo de interação. A forma gerada seguirá a [especificação de GeoJSON estendida para retângulos](extend-geojson.md#rectangle).

**Comece a desenhar**
 - Pressione o botão do rato esquerdo, ou toque no mapa para adicionar o primeiro canto do retângulo e arraste para criar o retângulo. 

**Acabamento desenho**
 - Liberte o botão do rato ou o ponto de toque.
 - Programáticamente definir o modo de desenho. 
 - Pressione a `C` chave.

**Cancelar desenho**
 - Pressione a `Escape` chave.

### <a name="how-to-draw-a-circle"></a>Como desenhar um círculo

Quando o gestor de desenho está em `draw-circle` modo, podem ser feitas as seguintes ações para desenhar pontos no mapa, dependendo do modo de interação. A forma gerada seguirá a [especificação de GeoJSON estendida para círculos](extend-geojson.md#circle).

**Comece a desenhar**
 - Pressione o botão do rato esquerdo, ou toque no mapa para adicionar o centro do círculo e arraste dar aos círculos um raio. 

**Acabamento desenho**
 - Liberte o botão do rato ou o ponto de toque.
 - Programáticamente definir o modo de desenho. 
 - Pressione a `C` chave.

**Cancelar desenho**
 - Pressione a `Escape` chave.

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

As ferramentas de desenho suportam atalhos de teclado. Estes atalhos de teclado são funcionais quando o mapa está em foco.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `C` | Completa qualquer desenho que esteja em andamento e define o modo de desenho para ocioso. O foco passará para o elemento de mapa de nível superior.  |
| `Escape` | Cancela qualquer desenho que esteja em andamento e define o modo de desenho para ocioso. O foco passará para o elemento de mapa de nível superior.  |
| `F` | Adiciona uma coordenada a um ponto, linha ou polígono se o rato estiver sobre o mapa. Ação equivalente de clicar no mapa quando em modo clique ou híbrido. Este atalho permite desenhos mais precisos e mais rápidos. Pode utilizar uma mão para posicionar o rato e outra para premir o botão sem mover o rato do gesto de pressão. |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas no módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Gestor de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Barra de ferramentas de desenho](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)