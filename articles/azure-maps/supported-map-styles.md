---
title: Estilos de mapa com suporte | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os diferentes estilos de renderização de mapa com suporte pelos mapas do Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 92e48486777d82589a56074790dd709c5d525859
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910628"
---
# <a name="azure-maps-supported-map-styles"></a>Estilos de mapa com suporte do Azure Maps
O Azure Maps dá suporte a vários estilos de mapa internos diferentes, conforme descrito abaixo.

## <a name="road"></a>rodoviário
Um mapa de **estrada** é um mapa padrão que exibe estradas, recursos naturais e artificiais juntamente com os rótulos para esses recursos.

![estilo de mapa de estrada](./media/supported-map-styles/road.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Bloco de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web
* Controle de mapa do Android

## <a name="blank-and-blank_accessible"></a>em branco e blank_accessible

Os estilos de mapa **em branco** e de **blank_accessible** fornecem uma tela em branco na qual os dados são visualizados. O estilo de **blank_accessible** continuará a fornecer atualizações de leitor de tela com detalhes de local de onde o mapa está localizado, mesmo que o mapa base não seja exibido.

> [!Note]
> No SDK Web, você pode alterar a cor do plano de fundo do mapa definindo o estilo de `background-color` CSS do elemento DIV do mapa.

**APIs aplicáveis:**
* Controle de mapa do SDK da Web

## <a name="satellite"></a>satellite 
O estilo **satélite** é uma combinação de imagens satélite e aéreas.

![estilo de mapa de bloco satélite](./media/supported-map-styles/satellite.png)

**APIs aplicáveis:**
* [Bloco satélite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controle de mapa do SDK da Web
* Controle de mapa do Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Esse estilo de mapa é um híbrido de estradas e rótulos sobrepostos sobre imagens satélite e aéreas.

![satellite_road_labels estilo de mapa](./media/supported-map-styles/satellite_road_labels.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Controle de mapa do Android

## <a name="grayscale_dark"></a>grayscale_dark
a **escala de cinza escura** é uma versão escura do estilo de mapa rodoviário.

![gray_scale estilo de mapa](./media/supported-map-styles/grayscale_dark.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Bloco de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web 
* Controle de mapa do Android


## <a name="grayscale_light"></a>grayscale_light
a **luz de escala de cinza** é uma versão leve do estilo de mapa rodoviário.

![estilo de mapa da luz em tons de cinza](./media/supported-map-styles/grayscale_light.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Controle de mapa do Android


## <a name="night"></a>noite
a **noite** é uma versão escura do estilo de mapa rodoviário com estradas e símbolos coloridos.

![estilo de mapa noturno](./media/supported-map-styles/night.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Controle de mapa do Android

## <a name="road_shaded_relief"></a>road_shaded_relief
o **alívio de estrada sombreado** é um estilo principal do Azure Maps concluído com contornos da terra.

![estilo de mapa de alívio sombreado](./media/supported-map-styles/shaded-relief.png)

**APIs aplicáveis:**
* [Bloco de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web
* Controle de mapa do Android


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como definir um estilo de mapa no Azure Maps:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
