---
title: Adicione controlos a um mapa Android | Microsoft Azure Maps
description: Como adicionar controlo de zoom, controlo de tom, controlo de rotação e um picker de estilo para um mapa no Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8224192ed0d13af2ff6ac60aac5aa928589ff01a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055104"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Adicionar controlos a um mapa (Android SDK)

Este artigo mostra-lhe como adicionar controlos de UI ao mapa.

## <a name="add-zoom-control"></a>Adicionar controlo de zoom

Um controlo de zoom adiciona botões para fazer zoom no mapa para dentro e para fora. A amostra de código a seguir cria uma instância da `ZoomControl` classe e adiciona-a a um mapa.

```java
map.controls.add(new ZoomControl());
```

A imagem abaixo é de um controlo de zoom carregado num mapa.

![Controlo de zoom adicionado ao mapa](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Adicione o controlo do tom

Um controlo de arremesso adiciona botões para inclinar o tom para mapear em relação ao horizonte. A amostra de código a seguir cria uma instância da `PitchControl` classe e adiciona-a a um mapa.

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

A imagem abaixo é de um controlo de arremesso carregado num mapa.

![Controlo de tom adicionado ao mapa](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Adicionar controlo de bússola

Um controlo da bússola adiciona um botão para rodar o mapa. A amostra de código a seguir cria uma instância da `CompassControl` classe e adiciona-a a um mapa.

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

A imagem abaixo é de um controlo de bússola carregado num mapa.

![Controlo da bússola adicionado ao mapa](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Adicionar controlo de tráfego

Um controlo de tráfego adiciona um botão para mexer a visibilidade dos dados de tráfego no mapa. A amostra de código a seguir cria uma instância da `TrafficControl` classe e adiciona-a a um mapa.

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

A imagem abaixo é de um controlo de tráfego carregado num mapa.

![Controlo de tráfego adicionado ao mapa](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Um mapa com todos os controlos

Vários controlos podem ser colocados numa matriz e adicionados ao mapa de uma só vez e posicionados na mesma área do mapa para simplificar o desenvolvimento. O seguinte adiciona os controlos de navegação padrão ao mapa usando esta abordagem.

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

A imagem abaixo mostra todos os controlos carregados num mapa. Note que a ordem que são adicionadas ao mapa é a ordem que eles vão aparecer.

![Todos os controlos adicionados ao mapa](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](how-to-add-shapes-to-android-map.md)
