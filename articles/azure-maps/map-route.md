---
title: Mostrar instruções com mapas do Azure | Microsoft Docs
description: Como exibir instruções entre dois locais em um mapa com o SDK da Web do Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: cf997d4ae120f3e9309892b112f9954bde97bc76
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976479"
---
# <a name="show-directions-from-a-to-b"></a>Mostrar as direções de A para B

Este artigo mostra como fazer uma solicitação de rota e mostrar a rota no mapa.

Há duas maneiras de fazer isso. A primeira maneira é consultar a [API de rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) por meio de um módulo de serviço. A segunda maneira é usar a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação de pesquisa para a API de [rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Ambas as maneiras são discutidas abaixo.

## <a name="query-the-route-via-service-module"></a>Consultar a rota por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar instruções de A a B em um mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Mostrar instruções de a a B em um mapa (módulo de serviço)</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um `TokenCredential` para autenticar solicitações HTTP para mapas do Azure com o token de acesso. Em seguida, ele `TokenCredential` passa `atlas.service.MapsURL.newPipeline()` o para e cria uma instância de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . O `routeURL` representa uma URL para operações de [rota](https://docs.microsoft.com/rest/api/maps/route) do Azure Maps.

O terceiro bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ao mapa.

O quarto bloco de código cria objetos Start e [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) end points e os adiciona ao objeto DataSource.

Uma linha é um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de LineString. Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza os objetos de linha encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte Propriedades de uma camada de linha em [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar os dados baseados em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa.

O sexto bloco de código consulta o serviço de roteamento do Azure Maps, que faz parte do [módulo de serviço](how-to-use-services-module.md). O método [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) do RouteUrl é usado para obter uma rota entre os pontos inicial e final. Uma coleção de recursos geojson da resposta é extraída usando o `geojson.getFeatures()` método e é adicionada à fonte de fontes. Em seguida, ele renderiza a resposta como uma rota no mapa. Para obter mais informações sobre como adicionar uma linha ao mapa, consulte [Adicionar uma linha no mapa](map-add-line-layer.md).

O último bloco de código define os limites do mapa usando a propriedade setcamera do [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapa.

A consulta de rota, a fonte de dados, o símbolo e as camadas de linha e os limites da câmera são criados e definidos dentro do ouvinte de [eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa para garantir que os resultados sejam exibidos depois que o mapa for totalmente carregado.

## <a name="query-the-route-via-fetch-api"></a>Consultar a rota por meio da API de busca

<iframe height='500' scrolling='no' title='Mostrar instruções de a a B em um mapa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Mostrar instruções de a a B em um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ao mapa.

O terceiro bloco de código cria os pontos inicial e de destino para a rota e os adiciona à fonte de dados. Você pode ver [Adicionar um PIN no mapa](map-add-pin.md) para obter instruções sobre como [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)usar addpins.

Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza os objetos de linha encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte Propriedades de uma camada de linha em [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar os dados baseados em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa. Consulte Propriedades de uma camada de símbolo em [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

O próximo bloco de código `SouthWest` cria `NorthEast` e aponta dos pontos inicial e de destino e define os limites do mapa usando a propriedade setcamera [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do mapa.

O último bloco de código usa a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação de pesquisa para a [API de rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Em seguida, a resposta é analisada. Se a resposta foi bem-sucedida, as informações de latitude e longitude são usadas para criar uma matriz uma linha conectando esses pontos. Os dados de linha são então adicionados à fonte de dados para renderizar a rota no mapa. Você pode ver [Adicionar uma linha no mapa](map-add-line-layer.md) para obter instruções.

A consulta de rota, a fonte de dados, o símbolo e as camadas de linha e os limites da câmera são criados e definidos dentro do ouvinte de [eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa para garantir que os resultados sejam exibidos depois que o mapa for totalmente carregado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar o tráfego no mapa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagindo com os eventos MAP-mouse](./map-events.md)
