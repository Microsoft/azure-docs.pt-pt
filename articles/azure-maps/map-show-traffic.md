---
title: Mostrar tráfego num mapa Microsoft Azure Maps
description: Neste artigo você vai aprender, como exibir dados de tráfego em um mapa usando o Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 5bb088c4650cfdfec59383a7bcf8f340952e5d55
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87273019"
---
# <a name="show-traffic-on-the-map"></a>Mostrar tráfego no mapa

Existem dois tipos de dados de tráfego disponíveis no Azure Maps:

- Dados de incidentes - consiste em dados de pontos e linhas para coisas como construção, encerramentos de estradas e acidentes.
- Flow data - fornece métricas sobre o fluxo de tráfego nas estradas. Muitas vezes, os dados de fluxo de tráfego são usados para colorir as estradas. As cores baseiam-se na quantidade de tráfego que está a abrandar o fluxo, em relação ao limite de velocidade, ou noutra métrica. Os dados de fluxo de tráfego no Azure Maps têm três métricas diferentes de medição:
    - `relative`- é relativo à velocidade de livre circulação da estrada.
    - `absolute`- é a velocidade absoluta de todos os veículos na estrada.
    - `relative-delay`- exibe áreas mais lentas do que a média esperada.

O código que se segue mostra como exibir dados de tráfego no mapa.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Abaixo está a amostra completa do código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Mostrar tráfego em um mapa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o tráfego do Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show num mapa</a> da Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opções de sobreposição de tráfego

A seguinte ferramenta permite alternar entre as diferentes definições de sobreposição de tráfego para ver como a renderização muda. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de sobreposição de tráfego" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>opções de sobreposição de tráfego de canetas</a> por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opções de Tráfego](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Melhore as suas experiências de utilizador:

> [!div class="nextstepaction"]
> [Interteração do mapa com eventos de rato](map-events.md)

> [!div class="nextstepaction"]
> [Construção de um mapa acessível](map-accessibility.md)

> [!div class="nextstepaction"]
> [Página de amostra de código](https://aka.ms/AzureMapsSamples)
