---
title: Estilos de mapa do Azure Maps incorporados suportados
description: Conheça os estilos de mapa incorporado que o Azure Maps suporta, como estrada, blank_accessible, satélite, satellite_road_labels, road_shaded_relief e noite.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3404c02dc159309ed1e9fcd6f9f6bb593fc7552
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896944"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps suportado estilos de mapa incorporado

O Azure Maps suporta vários estilos de mapa incorporados diferentes, como descrito abaixo.

## <a name="road"></a>estrada

Um **roteiro** é um mapa padrão que exibe estradas. Também exibe características naturais e artificiais, e os rótulos para essas características.

![estilo de mapa de estrada](./media/supported-map-styles/road.png)

**APIs aplicáveis:**

* [Imagem do mapa](/rest/api/maps/render/getmapimage)
* [Azulejo do mapa](/rest/api/maps/render/getmaptile)
* Controlo de mapas Web SDK
* Controlo de mapas android
* Elemento visual do Power BI

## <a name="blank-and-blank_accessible"></a>em branco e blank_accessible

Os **estilos de** mapa em branco e **blank_accessible** fornecem uma tela em branco para visualizar dados. O estilo **blank_accessible** continuará a fornecer atualizações de leitores de ecrã com os detalhes da localização do mapa, mesmo que o mapa base não seja apresentado.

> [!Note]
> No Web SDK, pode alterar a cor de fundo do mapa definindo o estilo CSS `background-color` do elemento DIV do mapa.

**APIs aplicáveis:**

* Controlo de mapas Web SDK

## <a name="satellite"></a>satellite

O estilo **satélite** é uma combinação de imagens de satélite e aéreas.

![estilo de mapa de azulejos de satélite](./media/supported-map-styles/satellite.png)

**APIs aplicáveis:**

* [Azulejo de satélite](/rest/api/maps/render/getmapimagerytilepreview)
* Controlo de mapas Web SDK
* Controlo de mapas android
* Elemento visual do Power BI

## <a name="satellite_road_labels"></a>satellite_road_labels

Este estilo de mapa é um híbrido de estradas e rótulos sobrepostos em cima de imagens de satélite e aéreas.

![estilo de mapa satellite_road_labels](./media/supported-map-styles/satellite-road-labels.png)

**APIs aplicáveis:**

* Controlo de mapas Web SDK
* Controlo de mapas android
* Elemento visual do Power BI

## <a name="grayscale_dark"></a>grayscale_dark

**grayscale dark** é uma versão escura do estilo roteiro mapa.

![estilo de mapa de gray_scale](./media/supported-map-styles/grayscale-dark.png)

**APIs aplicáveis:**

* [Imagem do mapa](/rest/api/maps/render/getmapimage)
* [Azulejo do mapa](/rest/api/maps/render/getmaptile)
* Controlo de mapas Web SDK
* Controlo de mapas android
* Elemento visual do Power BI

## <a name="grayscale_light"></a>grayscale_light

**luz em tons de cinza** é uma versão leve do estilo roteiro mapa.

![estilo de mapa de luz em tons de cinza](./media/supported-map-styles/grayscale-light.png)

**APIs aplicáveis:**
* Controlo de mapas Web SDK
* Controlo de mapas android
* Elemento visual do Power BI

## <a name="night"></a>noite

**noite** é uma versão escura do estilo roteiro com estradas coloridas e símbolos.

![estilo de mapa noturno](./media/supported-map-styles/night.png)

**APIs aplicáveis:**

* Controlo de mapas Web SDK
* Controlo de mapas android
* Elemento visual do Power BI

## <a name="road_shaded_relief"></a>road_shaded_relief

**road shaded relief** é um estilo principal Azure Maps completo com contornos da Terra.

![estilo de mapa de alívio sombreado](./media/supported-map-styles/shaded-relief.png)

**APIs aplicáveis:**

* [Azulejo do mapa](/rest/api/maps/render/getmaptile)
* Controlo de mapas Web SDK
* Controlo de mapas android
* Elemento visual do Power BI

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** é um estilo de mapa escuro com um contraste maior do que os outros estilos.

![estilo de mapa escuro de alto contraste](./media/supported-map-styles/high-contrast-dark.png)

**APIs aplicáveis:**

* Controlo de mapas Web SDK
* Elemento visual do Power BI

## <a name="next-steps"></a>Passos seguintes

Saiba como definir um estilo de mapa no Azure Maps:

[Escolher um estilo de mapa](./choose-map-style.md)