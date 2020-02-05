---
title: Mostrar direções de rota em um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a exibir direções entre duas localizações num mapa usando o Microsoft Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 359f2b42ca6f56087be53a5aeb328fe43a478d63
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988292"
---
# <a name="show-directions-from-a-to-b"></a>Mostrar as direções de A para B

Este artigo mostra como fazer uma solicitação de rota e mostrar a rota no mapa.

Há duas maneiras de fazer isso. A primeira maneira é consultar a [API de rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) por meio de um módulo de serviço. A segunda maneira é usar a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação de pesquisa para a API de [rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Ambas as maneiras são discutidas abaixo.

## <a name="query-the-route-via-service-module"></a>Consultar a rota por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar instruções de A a B em um mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Mostrar instruções de a a B em um mapa (módulo de serviço)</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o símbolo de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma `TokenCredential` para autenticar solicitações HTTP para mapas do Azure com o token de acesso. Em seguida, ele passa o `TokenCredential` para `atlas.service.MapsURL.newPipeline()` e cria uma instância de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . O `routeURL` representa uma URL para operações de [rota](https://docs.microsoft.com/rest/api/maps/route) do Azure Maps.

O terceiro bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ao mapa.

O quarto bloco de código cria objetos Start e End [Points](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) e os adiciona ao objeto DataSource.

Uma linha é uma [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) para o LineString. Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza os objetos de linha encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte Propriedades de uma camada de linha em [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utiliza textos ou ícones para renderizar dados baseados em pontos embrulhados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Os textos ou os ícones são os símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa.

O sexto bloco de código consulta o serviço de roteamento do Azure Maps, que faz parte do [módulo de serviço](how-to-use-services-module.md). O método [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) do RouteUrl é usado para obter uma rota entre os pontos inicial e final. Uma coleção de recursos geojson da resposta é extraída usando o método `geojson.getFeatures()` e é adicionada à fonte de fontes. Em seguida, ele renderiza a resposta como uma rota no mapa. Para obter mais informações sobre como adicionar uma linha ao mapa, consulte [Adicionar uma linha no mapa](map-add-line-layer.md).

O último bloco de código define os limites do mapa usando a propriedade [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do mapa.

A consulta de rota, fonte de dados, símbolo, camadas de linha e os limites da câmara são criados e definidos dentro do ouvinte do [mapa.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) Esta estrutura de código garante que os resultados só são apresentados após a carga total do mapa.

## <a name="query-the-route-via-fetch-api"></a>Consultar a rota por meio da API de busca

<iframe height='500' scrolling='no' title='Mostrar instruções de a a B em um mapa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Mostrar instruções de a a B em um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o símbolo de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ao mapa.

O terceiro bloco de código cria os pontos inicial e de destino para a rota e os adiciona à fonte de dados. Você pode ver [Adicionar um PIN no mapa](map-add-pin.md) para obter instruções sobre como usar [addpins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza os objetos de linha encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte Propriedades de uma camada de linha em [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar os dados baseados em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa. Consulte Propriedades de uma camada de símbolo em [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

O próximo bloco de código cria `SouthWest` e `NorthEast` pontos dos pontos inicial e de destino e define os limites do mapa usando a propriedade [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do mapa.

O último bloco de código usa a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação de pesquisa para a [API de rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Em seguida, a resposta é analisada. Se a resposta foi bem-sucedida, as informações de latitude e longitude são usadas para criar uma matriz uma linha conectando esses pontos. Os dados de linha são então adicionados à fonte de dados para renderizar a rota no mapa. Você pode ver [Adicionar uma linha no mapa](map-add-line-layer.md) para obter instruções.

A consulta de rota, fonte de dados, símbolo, camadas de linha e os limites da câmara são criados e definidos dentro do ouvinte do [mapa.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) Mais uma vez, queremos garantir que os resultados são apresentados após a carga total do mapa.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar o tráfego no mapa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagindo com os eventos MAP-mouse](./map-events.md)
