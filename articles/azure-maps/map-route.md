---
title: Mostrar direções de rota num mapa Microsoft Azure Maps
description: Neste artigo, você vai aprender a exibir direções entre duas localizações num mapa usando o Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 54a196cc8323e676dfb054a5fad260302833fa53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090691"
---
# <a name="show-directions-from-a-to-b"></a>Mostrar as direções de A para B

Este artigo mostra-lhe como fazer um pedido de rota e mostrar a rota no mapa.

Há duas maneiras de fazê-lo. A primeira forma é consultar a API da Rota do [Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) através de um módulo de serviço. A segunda forma é usar a [API Fetch](https://fetch.spec.whatwg.org/) para fazer um pedido de pesquisa à API da Rota dos [Mapas Azure.](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) Ambos os caminhos são discutidos abaixo.

## <a name="query-the-route-via-service-module"></a>Consultar a rota através do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar direções de A a B num mapa (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/RBZbep/'>instruções pen show de A a B num mapa (Módulo de Serviço)</a> por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o token de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de código cria um `TokenCredential` pedido HTTP para autenticar a Azure Maps com o token de acesso. Em seguida, passa `TokenCredential` o para e cria uma instância `atlas.service.MapsURL.newPipeline()` [pipeline.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline) O `routeURL` representa uma URL para as operações da Rota [dos](https://docs.microsoft.com/rest/api/maps/route) Mapas Azure.

O terceiro bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) ao mapa.

O quarto bloco de código cria objetos de [pontos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point) de partida e de fim e adiciona-os ao objeto dataSource.

Uma linha é uma [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature) para o LineString. Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer) torna os objetos de linha embrulhados no  [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linha nas [Opções LinestringLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer) utiliza textos ou ícones para renderizar dados baseados em pontos embrulhados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource). Os textos ou os ícones tornam-se símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa.

O sexto bloco de consultas de código o serviço de encaminhamento Azure Maps, que faz parte do módulo de [serviço.](how-to-use-services-module.md) O método [de cálculoRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#methods) do RouteURL é utilizado para obter uma rota entre os pontos de partida e de fim. Uma coleção de recursos GeoJSON da resposta é então extraída usando o `geojson.getFeatures()` método e é adicionada à fonte de dados. Em seguida, torna a resposta como uma rota no mapa. Para obter mais informações sobre a adição de uma linha ao mapa, consulte [adicionar uma linha no mapa.](map-add-line-layer.md)

O último bloco de código define os limites do mapa usando a propriedade [do Map's SetCamera.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)

A consulta de rota, fonte de dados, símbolo, camadas de linha e limites de câmara são criados dentro do ouvinte do [evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events). Esta estrutura de código garante que os resultados só são apresentados depois de o mapa estar completamente carregado.

## <a name="query-the-route-via-fetch-api"></a>Consultar a rota via Fetch API

<iframe height='500' scrolling='no' title='Mostrar direções de A a B em um mapa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>instruções do</a> Pen Show de A a B num mapa da Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o token de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) ao mapa.

O terceiro bloco de código cria os pontos de partida e destino para a rota. Depois, adiciona-os à fonte de dados. Pode ver [adicionar um pino no mapa](map-add-pin.md) para obter instruções sobre a utilização de [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map).

Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer) torna os objetos de linha embrulhados no  [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linha nas [Opções LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer) utiliza texto ou ícones para tornar os dados baseados em ponto embrulhados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa. Consulte as propriedades de uma camada de símbolo nas [Opções SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions).

O próximo bloco de código cria `SouthWest` e aponta a partir dos `NorthEast` pontos de início e destino e define os limites do mapa usando a propriedade [SetCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

O último bloco de código utiliza a [API Fetch](https://fetch.spec.whatwg.org/) para fazer um pedido de pesquisa à API da Rota dos [Mapas Azure](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). A resposta é então analisada. Se a resposta foi bem sucedida, a informação de latitude e longitude é usada para criar uma matriz de uma linha ligando esses pontos. Os dados da linha são adicionados à fonte de dados para tornar a rota no mapa. Pode ver [adicionar uma linha no mapa](map-add-line-layer.md) para obter instruções.

A consulta de rota, fonte de dados, símbolo, camadas de linha e limites de câmara são criados dentro do ouvinte do [evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events). Mais uma vez, queremos garantir que os resultados são apresentados após as cargas do mapa completamente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Melhores práticas para a utilização do serviço de encaminhamento](how-to-use-best-practices-for-search.md)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar tráfego no mapa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagindo com o mapa - eventos de rato](./map-events.md)
