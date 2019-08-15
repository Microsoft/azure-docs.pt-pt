---
title: Mostrar tráfego com mapas do Azure | Microsoft Docs
description: Como exibir dados de tráfego no SDK da Web do Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 145e2246703441a08868c8aae311573e95d4de42
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976443"
---
# <a name="show-traffic-on-the-map"></a>Mostrar o tráfego no mapa

Há dois tipos de dados de tráfego disponíveis no Azure Maps:

- Dados de incidentes – consistem em dados de ponto e de linha para coisas como construção, fechamentos de estrada e acidentes.
- Dados de fluxo-fornece métricas sobre o fluxo de tráfego nas estradas. Os dados de fluxo de tráfego geralmente são usados para colorir as estradas com base na quantidade de tráfego que está diminuindo o fluxo em relação ao limite de velocidade ou a alguma outra métrica. Os dados de fluxo de tráfego no Azure Maps têm três métricas diferentes de medida:
    - `relative`-é relativo à velocidade de fluxo livre da estrada.
    - `absolute`-é a velocidade absoluta de todos os veículos em trânsito.
    - `relative-delay`-exibe áreas que são mais lentas do que o atraso médio esperado.

O código a seguir mostra como exibir dados de tráfego no mapa.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Mostrar o tráfego em um mapa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Mostrar tráfego em um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opções de sobreposição de tráfego

A ferramenta a seguir permite alternar entre as diferentes configurações de sobreposição de tráfego para ver como a renderização é alterada. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de sobreposição de tráfego" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as opções de sobreposição de <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>tráfego</a> de caneta<a href='https://codepen.io/azuremaps'>@azuremaps</a>pelo Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Trafficoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Aprimore suas experiências de usuário:

> [!div class="nextstepaction"]
> [Mapear interação com eventos de mouse](map-events.md)

> [!div class="nextstepaction"]
> [Criando um mapa acessível](map-accessibility.md)

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
