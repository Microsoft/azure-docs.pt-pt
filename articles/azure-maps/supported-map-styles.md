---
title: Estilos de mapa com suporte no Azure Maps | Microsoft Docs
description: Estilos de mapa suportados pelo Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1aad2284c0f64c92efaefe3f9145d95c4aabec67
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839441"
---
# <a name="azure-maps-supported-map-styles"></a>Estilos de mapa com suporte do Azure Maps
O Azure Maps dá suporte a vários estilos de mapa internos diferentes, conforme descrito abaixo.

## <a name="road"></a>rodoviário
Um mapa de **estrada** é um mapa padrão que exibe estradas, recursos naturais e artificiais juntamente com os rótulos para esses recursos.

![rodoviário](./media/supported-map-styles/road.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Bloco de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web
* Controle de mapa do Android

## <a name="blank-and-blank_accessible"></a>em branco e blank_accessible

Os estilos de mapa **em branco** e **blank_accessible** fornecem uma tela em branco na qual os dados são visualizados. O estilo **blank_accessible** continuará a fornecer atualizações de leitor de tela com detalhes de local de onde o mapa está localizado, mesmo que o mapa base não seja exibido.

> [!Note]
> No SDK da Web, você pode alterar a cor do plano de fundo do mapa definindo `background-color` o estilo CSS do elemento do mapa div.

**APIs aplicáveis:**
* Controle de mapa do SDK da Web

## <a name="satellite"></a>escritório 
O estilo **satélite** é uma combinação de imagens satélite e aéreas.

![escritório](./media/supported-map-styles/satellite.png)

**APIs aplicáveis:**
* [Bloco satélite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controle de mapa do SDK da Web
* Controle de mapa do Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Esse estilo de mapa é um híbrido de estradas e rótulos sobrepostos sobre imagens satélite e aéreas.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Controle de mapa do Android

## <a name="grayscale_dark"></a>grayscale_dark
a **escala de cinza escura** é uma versão escura do estilo de mapa rodoviário.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web 
* Controle de mapa do Android


## <a name="grayscale_light"></a>grayscale_light
a **luz de escala de cinza** é uma versão leve do estilo de mapa rodoviário.

![luz de tons de cinza](./media/supported-map-styles/grayscale_light.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Controle de mapa do Android


## <a name="night"></a>jogo
a **noite** é uma versão escura do estilo de mapa rodoviário com estradas e símbolos coloridos.

![jogo](./media/supported-map-styles/night.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Controle de mapa do Android

## <a name="road_shaded_relief"></a>road_shaded_relief
o **alívio de estrada sombreado** é um estilo principal do Azure Maps concluído com contornos da terra.

![alívio sombreado](./media/supported-map-styles/shaded-relief.png)

**APIs aplicáveis:**
* [Bloco de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web
* Controle de mapa do Android
