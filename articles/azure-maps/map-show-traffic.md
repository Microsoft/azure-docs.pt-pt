---
title: Mostrar o tráfego com o Azure Maps | Documentos da Microsoft
description: Como exibir dados de tráfego num mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/10/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7cd7c0dbb375dad78927183dbaffe574a0dc10c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768840"
---
# <a name="show-traffic-on-the-map"></a>Tráfego de mostrar no mapa

Este artigo mostra-lhe como mostrar informações de tráfego e incidentes no mapa.

## <a name="understand-the-code"></a>Compreender o código

<iframe height='456' scrolling='no' title='Mostrar o tráfego num mapa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Mostrar tráfego num mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](map-create.md) para obter instruções.

O segundo bloco de código utiliza [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) função dentro do mapa [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) função para processar os fluxos de tráfego e incidentes no mapa.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Veja os artigos seguintes para exemplos de código completo:

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)

Melhore suas experiências de utilizador:

> [!div class="nextstepaction"]
> [Mapear a interação com eventos de mouse](./map-events.md)

> [!div class="nextstepaction"]
> [Criar um mapa acessível](./map-accessibility.md)
