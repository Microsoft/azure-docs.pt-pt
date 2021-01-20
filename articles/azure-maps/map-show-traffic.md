---
title: Mostrar tráfego em um mapa | Microsoft Azure Maps
description: Descubra como adicionar dados de tráfego aos mapas. Saiba mais sobre os dados de fluxo e veja como usar o Azure Maps Web SDK para adicionar dados de incidentes e dados de fluxo para mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b6fcf39e05c4649503f0b7a80aadaaa16df24674
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599550"
---
# <a name="show-traffic-on-the-map"></a>Mostrar tráfego no mapa

Existem dois tipos de dados de tráfego disponíveis no Azure Maps:

- Dados de incidentes - consiste em dados de pontos e linhas para coisas como construção, encerramentos de estradas e acidentes.
- Flow data - fornece métricas sobre o fluxo de tráfego nas estradas. Muitas vezes, os dados de fluxo de tráfego são usados para colorir as estradas. As cores baseiam-se na quantidade de tráfego que está a abrandar o fluxo, em relação ao limite de velocidade, ou noutra métrica. Há quatro valores que podem ser passados para a opção de tráfego `flow` do mapa.

    |Valor do fluxo | Descrição|
    | :-- | :-- |
    | `none` | Não exibe dados de tráfego no mapa |
    | `relative` | Mostra dados de tráfego relativos à velocidade de fluxo livre da estrada |
    | `relative-delay` | Exibe áreas mais lentas do que a média esperada |
    | `absolute` | Mostra a velocidade absoluta de todos os veículos na estrada |

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

<iframe height='500' scrolling='no' title='Mostrar tráfego em um mapa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o tráfego do Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show num mapa</a> da Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opções de sobreposição de tráfego

A seguinte ferramenta permite alternar entre as diferentes definições de sobreposição de tráfego para ver como a renderização muda. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de sobreposição de tráfego" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>opções de sobreposição de tráfego de canetas</a> por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="add-traffic-controls"></a>Adicionar controlos de tráfego

Existem dois controlos de tráfego diferentes que podem ser adicionados ao mapa. O primeiro comando, `TrafficControl` adiciona um botão de toggle que pode ser usado para ligar e desligar o tráfego. As opções para este controlo permitem especificar quando as definições de tráfego devem ser utilizadas quando mostrar o tráfego. Por predefinição, este controlo apresentará dados relativos de fluxo de tráfego e incidentes, no entanto, poderá alterá-lo para mostrar fluxo de tráfego absoluto e sem incidentes se desejar. O segundo controlo, `TrafficLegendControl` adiciona uma lenda do fluxo de tráfego ao mapa que ajuda o utilizador a entender o que os destaques da estrada de código de cor significam. Este controlo só aparecerá no mapa quando os dados de fluxo de tráfego estiverem expostos no mapa e serão escondidos em todos os outros momentos.

O código que se segue mostra como adicionar os controlos de tráfego ao mapa.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Controlos de tráfego" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte os <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>controlos de tráfego de canetas</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opções de Tráfego](/javascript/api/azure-maps-control/atlas.trafficoptions)

Melhore as suas experiências de utilizador:

> [!div class="nextstepaction"]
> [Interteração do mapa com eventos de rato](map-events.md)

> [!div class="nextstepaction"]
> [Construção de um mapa acessível](map-accessibility.md)

> [!div class="nextstepaction"]
> [Página de amostra de código](https://aka.ms/AzureMapsSamples)