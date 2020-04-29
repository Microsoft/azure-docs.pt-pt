---
title: Mostrar direções de rota num mapa Microsoft Azure Maps
description: Neste artigo, você aprenderá a exibir direções entre duas localizações num mapa usando o Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: dde9264d0cb65726b624b918982cfa01985b63ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80371414"
---
# <a name="show-directions-from-a-to-b"></a>Mostrar as direções de A para B

Este artigo mostra-lhe como fazer um pedido de rota e mostrar a rota no mapa.

Há duas maneiras de fazê-lo. A primeira maneira é consultar a [API da Rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) dos Mapas Azure através de um módulo de serviço. A segunda forma é usar a [Fetch API](https://fetch.spec.whatwg.org/) para fazer um pedido de pesquisa à [API da Rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)dos Mapas Azure . Ambos os lados são discutidos abaixo.

## <a name="query-the-route-via-service-module"></a>Consulta da rota através do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar direções de A a B num mapa (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as instruções pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>show de A a B num mapa (Módulo de Serviço)</a> por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o símbolo de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de `TokenCredential` código cria um pedido de HTTP para autenticar pedidos de HTTP para o Azure Maps com o sinal de acesso. Em seguida, `TokenCredential` `atlas.service.MapsURL.newPipeline()` passa o para e cria uma instância [pipeline.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) O `routeURL` representa um URL para operações da [Rota](https://docs.microsoft.com/rest/api/maps/route) dos Mapas Azure.

O terceiro bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ao mapa.

O quarto bloco de código cria objetos de [pontos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) de início e de fim e adiciona-os ao objeto dataSource.

Uma linha é uma [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) para o LineString. Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) torna os objetos de linha embrulhados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linha em [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utiliza textos ou ícones para renderizar dados baseados em pontos embrulhados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Os textos ou os ícones são os símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa.

O sexto bloco de código consulta o serviço de encaminhamento Do Azure Maps, que faz parte do módulo de [serviço.](how-to-use-services-module.md) O método [de calcular RouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) do RouteURL é utilizado para obter uma rota entre os pontos de partida e de fim. Uma recolha de funcionalidades GeoJSON da `geojson.getFeatures()` resposta é então extraída utilizando o método e adicionada ao datasource. Em seguida, torna a resposta como uma rota no mapa. Para obter mais informações sobre a adição de uma linha ao mapa, consulte [adicionar uma linha no mapa](map-add-line-layer.md).

O último bloco de código define os limites do mapa utilizando a propriedade [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

A consulta de rota, fonte de dados, símbolo, camadas de linha e limites da câmara são criados dentro do ouvinte do [evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Esta estrutura de código garante que os resultados só são apresentados após a carga total do mapa.

## <a name="query-the-route-via-fetch-api"></a>Consulta da rota via Fetch API

<iframe height='500' scrolling='no' title='Mostrar direções de A a B em um mapa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja as <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>instruções do</a> Pen Show de A a<a href='https://codepen.io/azuremaps'>@azuremaps</a>B num mapa do Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o símbolo de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ao mapa.

O terceiro bloco de código cria os pontos de partida e destino para a rota. Em seguida, adiciona-os à fonte de dados. Pode ver [adicionar um pino no mapa](map-add-pin.md) para obter instruções sobre a utilização de [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) torna os objetos de linha embrulhados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linha no [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utiliza texto ou ícones para tornar os dados baseados em pontos embrulhados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa. Consulte as propriedades de uma camada de símbolo no [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

O próximo bloco `SouthWest` `NorthEast` de código cria e aponta a partir dos pontos de partida e destino e define os limites do mapa usando a propriedade [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

O último bloco de código utiliza a [Fetch API](https://fetch.spec.whatwg.org/) para fazer um pedido de pesquisa à [API da Rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)dos Mapas Azure . A resposta é então analisada. Se a resposta foi bem sucedida, a informação sobre latitude e longitude é usada para criar uma linha de uma linha, ligando esses pontos. Os dados da linha são adicionados à fonte de dados para tornar a rota no mapa. Pode ver [adicionar uma linha no mapa](map-add-line-layer.md) para instruções.

A consulta de rota, fonte de dados, símbolo, camadas de linha e limites da câmara são criados dentro do ouvinte do [evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Mais uma vez, queremos garantir que os resultados são apresentados após a carga total do mapa.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Boas práticas para usar o serviço de encaminhamento](how-to-use-best-practices-for-search.md)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar tráfego no mapa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagindo com o mapa - eventos de rato](./map-events.md)
