---
title: Estilos de mapa suportados / Microsoft Azure Maps
description: Neste artigo, você aprenderá sobre diferentes estilos de renderização de mapas suportados pelo Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a65f20e04603403a1d3a180e364626d5b4440661
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664528"
---
# <a name="azure-maps-supported-map-styles"></a>Estilos de mapas suportados pelo Azure Maps
O Azure Maps suporta vários diferentes estilos de mapas incorporados, como descrito abaixo.

## <a name="road"></a>estrada
Um roteiro é um mapa padrão que exibe estradas, características naturais e artificiais juntamente com os rótulos para essas características.

![estilo de mapa de estrada](./media/supported-map-styles/road.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Azulejo do mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controlo do mapa Web SDK
* Controlo de mapas Android

## <a name="blank-and-blank_accessible"></a>em branco e blank_accessible

Os estilos de mapa **em branco** e **blank_accessible** fornecem uma tela em branco para visualizar dados. O estilo **blank_accessible** continuará a fornecer atualizações de leitores de ecrã com detalhes de localização do mapa, mesmo que o mapa base não seja exibido.

> [!Note]
> No SDK web pode alterar a cor de fundo do mapa definindo o estilo CSS `background-color` do elemento DIV do mapa.

**APIs aplicáveis:**
* Controlo do mapa Web SDK

## <a name="satellite"></a>satélite 
O estilo **satélite** é uma combinação de imagens de satélite e antena.

![estilo de mapa de azulejos de satélite](./media/supported-map-styles/satellite.png)

**APIs aplicáveis:**
* [Azulejo de satélite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controlo do mapa Web SDK
* Controlo de mapas Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Este estilo de mapa é um híbrido de estradas e rótulos sobrepostos em cima de imagens de satélite e antena.

![satellite_road_labels estilo mapa](./media/supported-map-styles/satellite-road-labels.png)

**APIs aplicáveis:**
* Controlo do mapa Web SDK
* Controlo de mapas Android

## <a name="grayscale_dark"></a>grayscale_dark
**cinza em tons escuros** é uma versão escura do estilo do roteiro.

![estilo de mapa gray_scale](./media/supported-map-styles/grayscale-dark.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Azulejo do mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controlo do mapa Web SDK 
* Controlo de mapas Android


## <a name="grayscale_light"></a>grayscale_light
**a luz em tons de cinza** é uma versão leve do estilo do roteiro.

![estilo de mapa de luz de escala cinzenta](./media/supported-map-styles/grayscale-light.png)

**APIs aplicáveis:**
* Controlo do mapa Web SDK
* Controlo de mapas Android


## <a name="night"></a>Noite
**noite** é uma versão escura do estilo do roteiro com estradas e símbolos coloridos.

![estilo mapa noturno](./media/supported-map-styles/night.png)

**APIs aplicáveis:**
* Controlo do mapa Web SDK
* Controlo de mapas Android

## <a name="road_shaded_relief"></a>road_shaded_relief
**o relevo sombreado** da estrada é um estilo principal do Azure Maps, completado com contornos da Terra.

![estilo mapa de alívio sombreado](./media/supported-map-styles/shaded-relief.png)

**APIs aplicáveis:**
* [Azulejo do mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controlo do mapa Web SDK
* Controlo de mapas Android

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** é um estilo de mapa escuro com um contraste maior do que os outros estilos.

![estilo de mapa escuro de alto contraste](./media/supported-map-styles/high-contrast-dark.png)

**APIs aplicáveis:**
* Controlo do mapa Web SDK

## <a name="next-steps"></a>Passos seguintes

Saiba como definir um estilo de mapa no Azure Maps:

> [!div class="nextstepaction"]
> [Escolha um estilo de mapa](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
