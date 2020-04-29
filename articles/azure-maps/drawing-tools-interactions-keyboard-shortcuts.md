---
title: Desenhar ferramentas de interação tipos e atalhos de teclado no mapa Microsoft Azure Maps
description: Como desenhar e editar formas utilizando um rato, ecrã tátil ou teclado no Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fb8a1e1a8c29086553500bdad2e4604d1e1ef471
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77198297"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Tipos de interação e atalhos de teclado no módulo de ferramentas de desenho

Este artigo descreve todas as diferentes formas de desenhar e editar formas usando um rato, ecrã tátil ou atalhos de teclado.

O gestor de desenho suporta três formas diferentes de interagir com o mapa, para desenhar formas.

* `click`- As coordenadas são adicionadas quando o rato ou o toque são clicados.
* `freehand `- São adicionadas coordenadas quando o rato ou o toque são arrastados no mapa.
* `hybrid`- As coordenadas são adicionadas quando o rato ou o toque são clicados ou arrastados.

## <a name="how-to-draw-shapes"></a>Como desenhar formas

 Antes de qualquer forma poder `drawingMode` ser desenhada, defina a opção do gestor de desenho para uma definição de desenho suportada. Esta definição pode ser programada ou invocada premindo um dos botões de desenho na barra de ferramentas. O modo de desenho mantém-se ativado, mesmo depois de uma forma ter sido desenhada, facilitando a elaboração de formas adicionais do mesmo tipo. Programáticamente, coloque o modo de desenho num estado ocioso. Ou, mude para um estado ocioso clicando no botão de modos de desenho atual na barra de ferramentas.

As secções seguintes delineiam todas as diferentes formas que as formas podem ser desenhadas no mapa.

### <a name="how-to-draw-a-point"></a>Como desenhar um ponto

Quando o gestor `draw-point` de desenho está em modo de desenho, as seguintes ações podem ser feitas para desenhar pontos no mapa. Estes métodos funcionam com todos os modos de interação.

**Começar a desenhar**
 - Clique no botão do rato esquerdo ou toque no mapa para adicionar um ponto ao mapa. 
 - Se o rato estiver sobre `F` o mapa, prima a tecla e um ponto será adicionado na coordenada do ponteiro do rato. Este método proporciona uma maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no rato devido ao movimento de pressão do botão do rato esquerdo.
 - Continue a clicar, tocar `F` ou premir para adicionar mais pontos ao mapa.
 
**Terminar o desenho**
 - Clique em qualquer botão na barra de ferramentas de desenho. 
 - Definir programáticamente o modo de desenho. 
 - Pressione `C` a chave.

**Cancelar o desenho**
 - Pressione `Escape` a chave.

### <a name="how-to-draw-a-line"></a>Como desenhar uma linha

Quando o gestor `draw-line` de desenho está em modo, as seguintes ações podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação.

**Começar a desenhar**
 - Clique no modo
   * Clique no botão do rato esquerdo ou toque no mapa para adicionar cada ponto de linha no mapa. Uma coordenada é adicionada à linha para cada clique ou toque. 
   * Se o rato estiver sobre `F` o mapa, prima a tecla e um ponto será adicionado na coordenada do ponteiro do rato. Este método proporciona uma maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no rato devido ao movimento de pressão do botão do rato esquerdo.
   * Continue a clicar até que todos os pontos desejados tenham sido adicionados à linha.
 - Modo freehand
   * Pressione para baixo o botão do rato esquerdo, ou retoque no mapa e arraste o rato, ou ponto de contacto ao redor. As coordenadas são adicionadas à linha à medida que o rato ou o ponto de contacto se movem em torno do mapa. Assim que o rato ou evento de retoque for desencadeado, o desenho é concluído. A frequência a que são adicionadas coordenadas `freehandInterval` é definida pela opção de gestores de desenho.
 - Modo híbrido
   * Alterne entre os métodos de clique e de mão livre, conforme desejado, enquanto desenha uma única linha. Por exemplo, clique em alguns pontos, em seguida, segure e arraste o rato para adicionar um monte de pontos, em seguida, clique em mais alguns. 

**Terminar o desenho**
 - Modo Híbrido/Clique
   * Clique duas vezes no mapa no último ponto. 
   * Clique em qualquer botão na barra de ferramentas de desenho. 
   * Definir programáticamente o modo de desenho. 
 - Modo freehand
   * Desbloqueie o botão do rato ou o ponto de contacto.
 - Pressione `C` a chave.

**Cancelar o desenho**
 - Pressione `Escape` a chave.

### <a name="how-to-draw-a-polygon"></a>Como desenhar um polígono

Quando o gestor `draw-polygon` de desenho está em modo, as seguintes ações podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação.

**Começar a desenhar**
 - Clique no modo
   * Clique no botão do rato esquerdo ou toque no mapa para adicionar cada ponto de um polígono no mapa. Uma coordenada é adicionada ao polígono para cada clique ou toque. 
   * Se o rato estiver sobre `F` o mapa, prima a tecla e um ponto será adicionado na coordenada do ponteiro do rato. Este método proporciona uma maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no rato devido ao movimento de pressão do botão do rato esquerdo.
   * Continue a clicar até que todos os pontos desejados tenham sido adicionados ao polígono.
 - Modo freehand
   * Pressione para baixo o botão do rato esquerdo, ou retoque no mapa e arraste o rato, ou ponto de contacto ao redor. As coordenadas são adicionadas ao polígono à medida que o rato ou o ponto de contacto se movem em torno do mapa. Assim que o rato ou evento de retoque for desencadeado, o desenho é concluído. A frequência a que são adicionadas coordenadas `freehandInterval` é definida pela opção de gestores de desenho.
 - Modo híbrido
   * Alterne entre os métodos de clique e de mão livre, conforme desejado, enquanto desenha um único polígono. Por exemplo, clique em alguns pontos, em seguida, segure e arraste o rato para adicionar um monte de pontos, em seguida, clique em mais alguns. 

**Terminar o desenho**
 - Modo Híbrido/Clique
   * Clique duas vezes no mapa no último ponto. 
   * Clique no primeiro ponto do polígono.
   * Clique em qualquer botão na barra de ferramentas de desenho. 
   * Definir programáticamente o modo de desenho. 
 - Modo freehand
   * Desbloqueie o botão do rato ou o ponto de contacto.
 - Pressione `C` a chave.

**Cancelar o desenho**
 - Pressione `Escape` a chave.

### <a name="how-to-draw-a-rectangle"></a>Como desenhar um retângulo

Quando o gestor `draw-rectangle` de desenho está em modo, as seguintes ações podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação. A forma gerada seguirá a [especificação GeoJSON estendida para retângulos](extend-geojson.md#rectangle).

**Começar a desenhar**
 - Pressione para baixo o botão do rato esquerdo ou retoque no mapa para adicionar o primeiro canto do retângulo e arraste para criar o retângulo. 

**Terminar o desenho**
 - Desbloqueie o botão do rato ou o ponto de contacto.
 - Definir programáticamente o modo de desenho. 
 - Pressione `C` a chave.

**Cancelar o desenho**
 - Pressione `Escape` a chave.

### <a name="how-to-draw-a-circle"></a>Como desenhar um círculo

Quando o gestor `draw-circle` de desenho está em modo, as seguintes ações podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação. A forma gerada seguirá a [especificação GeoJSON estendida para círculos](extend-geojson.md#circle).

**Começar a desenhar**
 - Pressione para baixo o botão do rato esquerdo ou retoque no mapa para adicionar o centro do círculo e arraste dar aos círculos um raio. 

**Terminar o desenho**
 - Desbloqueie o botão do rato ou o ponto de contacto.
 - Definir programáticamente o modo de desenho. 
 - Pressione `C` a chave.

**Cancelar o desenho**
 - Pressione `Escape` a chave.

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

As ferramentas de desenho suportam atalhos de teclado. Estes atalhos de teclado são funcionais quando o mapa está em foco.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `C` | Completa qualquer desenho que esteja em andamento e define o modo de desenho para o cio. O foco passará para o elemento do mapa de nível superior.  |
| `Escape` | Cancela qualquer desenho que esteja em andamento e define o modo de desenho para o cio. O foco passará para o elemento do mapa de nível superior.  |
| `F` | Adiciona uma coordenada a um ponto, linha ou polígono se o rato estiver sobre o mapa. Ação equivalente de clicar no mapa quando em modo clique ou híbrido. Este atalho permite desenhos mais precisos e rápidos. Pode utilizar uma mão para posicionar o rato e outra para premir o botão sem mover o rato do gesto de pressão. |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas no módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Gestor de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra de ferramentas de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
