---
title: Mostrar informações sobre uma coordenada num mapa Microsoft Azure Maps
description: Saiba como exibir informações sobre um endereço no mapa quando um utilizador seleciona uma coordenada.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80371424"
---
# <a name="get-information-from-a-coordinate"></a>Obter informações a partir de uma coordenada

Este artigo mostra como fazer uma pesquisa de endereço supérreo que mostra o endereço de um local popup clicado.

Há duas maneiras de fazer uma pesquisa de endereço inversa. Uma maneira é consultar o [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) através de um módulo de serviço. A outra forma é usar a [Fetch API](https://fetch.spec.whatwg.org/) para fazer um pedido à API de pesquisa de [endereços invertidos do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para encontrar um endereço. Ambos os sentidos são inquiridos abaixo.

## <a name="make-a-reverse-search-request-via-service-module"></a>Faça um pedido de pesquisa inversa através do módulo de serviço

<iframe height='500' scrolling='no' title='Obtenha informações a partir de uma coordenada (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obtenha informações de uma coordenada (Módulo de Serviço)</a> do Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o símbolo de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco `TokenCredential` de código cria um pedido http para autenticar pedidos de HTTP para o Azure Maps com o sinal de acesso. Em seguida, `TokenCredential` `atlas.service.MapsURL.newPipeline()` passa o para e cria uma instância [pipeline.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) O `searchURL` representa um URL para operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) de mapas azure.

O terceiro bloco de código atualiza o estilo do cursor de rato a um ponteiro e cria um objeto [popup.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Pode ver [adicionar um pop-up no mapa](./map-add-popup.md) para instruções.

O quarto bloco de código adiciona um ouvinte de [evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)de clique de rato . Quando acionado, cria uma consulta de pesquisa com as coordenadas do ponto clicado. Em seguida, utiliza o método [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)para consultar a [API inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) do endereço de procura get para o endereço das coordenadas. Uma coleção de recursos GeoJSON `geojson.getFeatures()` é então extraída usando o método da resposta.

O quinto bloco de código configura o conteúdo popup HTML para mostrar o endereço de resposta para a posição de coordenada clicada.

A mudança do cursor, o objeto popup e o evento de clique são todos criados no ouvinte do evento de [carga](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)do mapa . Esta estrutura de código garante o mapa completamente carregado antes de recuperar a informação das coordenadas.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Faça um pedido de pesquisa inversa via Fetch API

Clique no mapa para fazer um pedido de geocódigo inverso para esse local usando a fetch.

<iframe height='500' scrolling='no' title='Obter informações a partir de uma coordenada' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Obtenha informações de uma coordenada</a> da Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o símbolo de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de código atualiza o estilo do cursor do rato a um ponteiro. Instantaneamente um objeto [pop-up.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Pode ver [adicionar um pop-up no mapa](./map-add-popup.md) para instruções.

O terceiro bloco de código adiciona um ouvinte de eventopara cliques de rato. Ao clicar num rato, utiliza a [Fetch API](https://fetch.spec.whatwg.org/) para consultar a API de pesquisa de [endereços invertidos do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para o endereço de coordenadas clicado. Para uma resposta bem sucedida, recolhe o endereço para a localização clicada. Define o conteúdo pop-up e a posição utilizando a função [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) da classe popup.

A mudança do cursor, o objeto popup e o evento de clique são todos criados no ouvinte do evento de [carga](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)do mapa . Esta estrutura de código garante que o mapa se carregue completamente antes de recuperar a informação das coordenadas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Boas práticas para usar o serviço de pesquisa](how-to-use-best-practices-for-search.md)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar as direções de A para B](./map-route.md)

> [!div class="nextstepaction"]
> [Mostrar trânsito](./map-show-traffic.md)
