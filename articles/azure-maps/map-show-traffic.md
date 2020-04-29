---
title: Mostrar tráfego num mapa Microsoft Azure Maps
description: Neste artigo você vai aprender, como exibir dados de tráfego em um mapa usando o Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79534819"
---
# <a name="show-traffic-on-the-map"></a>Mostrar tráfego no mapa

Existem dois tipos de dados de tráfego disponíveis no Azure Maps:

- Os dados relativos aos incidentes - consistem em dados de pontoe de linha para coisas como construção, encerramento de estradas e acidentes.
- Dados de fluxo - fornece métricas sobre o fluxo de tráfego nas estradas. Muitas vezes, os dados do fluxo de tráfego são usados para colorir as estradas. As cores baseiam-se na quantidade de tráfego que está a abrandar o fluxo, em relação ao limite de velocidade, ou noutra métrica. Os dados do fluxo de tráfego no Azure Maps têm três métricas diferentes de medição:
    - `relative`- é relativamente à velocidade de fluxo livre da estrada.
    - `absolute`- é a velocidade absoluta de todos os veículos na estrada.
    - `relative-delay`- exibe áreas mais lentas do que o atraso esperado médio.

O código que se segue mostra como exibir dados de tráfego no mapa.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Abaixo está a amostra completa de código de execução da funcionalidade acima referida.

<br/>

<iframe height='500' scrolling='no' title='Mostrar tráfego em um mapa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o tráfego pen show em um<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>mapa</a> de Azure Maps ( ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opções de sobreposição de tráfego

A seguinte ferramenta permite alternar entre as diferentes definições de sobreposição de tráfego para ver como a renderização muda. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de sobreposição de tráfego" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as opções de <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>sobreposição</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>de tráfego de canetas por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opções de Tráfego](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Melhore as experiências do utilizador:

> [!div class="nextstepaction"]
> [Interação do mapa com eventos de rato](map-events.md)

> [!div class="nextstepaction"]
> [Construção de um mapa acessível](map-accessibility.md)

> [!div class="nextstepaction"]
> [Página de amostra de código](https://aka.ms/AzureMapsSamples)
