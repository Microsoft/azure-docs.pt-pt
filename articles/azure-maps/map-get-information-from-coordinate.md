---
title: Mostrar informações sobre uma coordenada em um mapa | Mapas do Microsoft Azure
description: Saiba como exibir informações sobre um endereço no mapa quando um usuário seleciona uma coordenada.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0b1ab7b2c233eb1e6e231b0ae7935b6c24363948
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988537"
---
# <a name="get-information-from-a-coordinate"></a>Obter informações a partir de uma coordenada

Este artigo mostra como fazer uma pesquisa de endereço inverso que mostra o endereço de um local de pop-up clicado.

Há duas maneiras de fazer uma pesquisa de endereço inverso. Uma maneira é consultar a [API de pesquisa de endereço reverso do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) por meio de um módulo de serviço. A outra maneira é utilizar a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação para a [API de pesquisa de endereço reverso do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para localizar um endereço. As duas formas são pesquisadas abaixo.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa inversa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada (módulo de serviço)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>obter informações de uma coordenada (módulo de serviço)</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o símbolo de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma `TokenCredential` para autenticar pedidos de HTTP para o Azure Maps com o sinal de acesso. Em seguida, ele passa o `TokenCredential` para `atlas.service.MapsURL.newPipeline()` e cria uma instância de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . O `searchURL` representa uma URL para operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Maps.

O terceiro bloco de código atualiza o estilo do cursor de rato a um ponteiro e cria um objeto [popup.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Você pode ver [Adicionar um pop-up no mapa](./map-add-popup.md) para obter instruções.

O quarto bloco de código adiciona um [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)de clique do mouse. Quando disparado, ele cria uma consulta de pesquisa com as coordenadas do ponto clicado. Em seguida, utiliza o método [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)para consultar a [API inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) do endereço de procura get para o endereço das coordenadas. Uma coleção de recursos GeoJSON é então extraída utilizando o método `geojson.getFeatures()` da resposta.

O quinto bloco de código configura o conteúdo do pop-up HTML para exibir o endereço de resposta para a posição da coordenada clicada.

A mudança de cursor, um objeto popup, e o evento de clique são todos criados no ouvinte do evento de [carga](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa para garantir as cargas do mapa completamente antes de a informação coordenada ser recuperada.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fazer uma solicitação de pesquisa inversa por meio da API de busca

Clique no mapa para fazer uma solicitação de geocódigo inversa para esse local usando Fetch.

<iframe height='500' scrolling='no' title='Obter informações a partir de uma coordenada' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>obter informações de uma coordenada</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o símbolo de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código atualiza o estilo do cursor do rato a um ponteiro. Instantaneamente um objeto [pop-up.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Você pode ver [Adicionar um pop-up no mapa](./map-add-popup.md) para obter instruções.

O terceiro bloco de código adiciona um ouvinte de eventos para cliques do mouse. Ao clicar num rato, utiliza a [Fetch API](https://fetch.spec.whatwg.org/) para consultar a API de pesquisa de [endereços invertidos do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para o endereço de coordenadas clicado. Para uma resposta bem sucedida, recolhe o endereço para a localização clicada. Define o conteúdo pop-up e a posição utilizando a função [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) da classe popup.

A mudança de cursor, um objeto popup, e o evento de clique são todos criados no ouvinte do evento de [carga](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa para garantir as cargas do mapa completamente antes de as coordenadas serem recuperadas informações.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar instruções de a a B](./map-route.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego](./map-show-traffic.md)
