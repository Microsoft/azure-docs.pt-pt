---
title: Mostrar as direções com o Azure Maps | Documentos da Microsoft
description: Como exibir as direções entre duas localizações num mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 4f999df23b5c08125b107f15091d90d85a045908
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586295"
---
# <a name="show-directions-from-a-to-b"></a>Mostrar as direções de A para B

Este artigo mostra-lhe como fazer um pedido de rota e mostrar a rota no mapa.

Existem duas formas de fazê-lo. A primeira maneira é a consulta a [API do Azure Maps rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) por meio de um módulo de serviço. A segunda maneira é fazer uma [XMLHttpRequest](https://xhr.spec.whatwg.org/) para a API. Ambas as formas são abordadas abaixo.

## <a name="query-the-route-via-service-module"></a>Consulta a rota através do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar as direções da para B num mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/RBZbep/'>mostrar as direções da para B num mapa (módulo de serviço)</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

A linha no segundo bloco de código cria uma instância de um serviço de cliente.

A terceira cria e adiciona uma [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objeto ao mapa.

 Uma linha é uma [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de LineString. Uma [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) composições linha objetos encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adicionar uma camada de linhas ao mapa. Ver as propriedades de uma camada de linha em [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa ou ícones de texto para processar dados com base no ponto encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O quinto bloco de código cria e adicionar uma camada de símbolo ao mapa.

O sexto bloco de código cria o início e de fim [pontos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) de objeto e adiciona-as para o objeto de origem de dados.

O sétimo bloco de código define os limites do mapa usando o mapa [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) propriedade.

O último bloco de código consulta o serviço de encaminhamento de mapas do Azure, que faz parte do [módulo do serviço](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). O [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) método é usado para obter uma rota entre os pontos inicial e final. A resposta, em seguida, é analisada para utilizar o formato GeoJSON a [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routegeojson?view=azure-iot-typescript-latest) método. Ela então processa a resposta como uma rota no mapa. Para obter mais informações sobre como adicionar uma linha para o mapa, consulte [adicionar uma linha no mapa](./map-add-shape.md#addALine).

A consulta de rota, a origem de dados, símbolo e camadas de linha e os limites de câmera são criados e definidos dentro do mapa [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que os resultados são apresentados depois do mapa é totalmente carregado.

## <a name="query-the-route-via-xmlhttprequest"></a>Consulta a rota via XMLHttpRequest

<iframe height='500' scrolling='no' title='Mostrar as direções da para B num mapa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>mostrar as direções da para B num mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria e adiciona uma [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objeto ao mapa.

O terceiro bloco de código cria os pontos de início e de destino para a rota e adiciona-as para a origem de dados. Pode ver [adicionar um pin no mapa](map-add-pin.md) para obter instruções sobre como utilizar [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

 Uma [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) composições linha objetos encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linhas ao mapa. Ver as propriedades de uma camada de linha em [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa ou ícones de texto para processar dados com base no ponto encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O quinto bloco de código cria e adicionar uma camada de símbolo ao mapa. Ver as propriedades de uma camada de símbolo em [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

O próximo bloco de código cria `SouthWest` e `NorthEast` aponta a partir dos pontos de início e de destino e define os limites do mapa usando o mapa [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) propriedade.

O último bloco de código envia um [XMLHttpRequest](https://xhr.spec.whatwg.org/) ao [API de rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Em seguida, ele analisa a resposta recebida. E para uma resposta com êxito, recolhe as informações de latitude e longitude para cada ponto de rota e cria uma matriz de linhas ao ligar esses pontos. Em seguida, adiciona todas as linhas para a origem de dados para processar a rota no mapa. Pode ver [adicionar uma linha no mapa](./map-add-shape.md#addALine) para obter instruções.

A consulta de rota, a origem de dados, símbolo e camadas de linha e os limites de câmera são criados e definidos dentro do mapa [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que os resultados são apresentados depois do mapa é totalmente carregado.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Veja os artigos seguintes para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Tráfego de mostrar no mapa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagir com o mapa - eventos de mouse](./map-events.md)
