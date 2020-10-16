---
title: Mostrar informações sobre uma coordenada num mapa Microsoft Azure Maps
description: Saiba como exibir informações sobre um endereço no mapa quando um utilizador seleciona uma coordenada.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: af31ab04653beb440655c4ab1a75946bed17c01b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285099"
---
# <a name="get-information-from-a-coordinate"></a>Obter informações a partir de uma coordenada

Este artigo mostra como fazer uma pesquisa de endereço inverso que mostra o endereço de uma localização popup clicada.

Há duas maneiras de fazer uma pesquisa de endereço inverso. Uma maneira é consultar a [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) através de um módulo de serviço. A outra forma é usar a [API Fetch](https://fetch.spec.whatwg.org/) para fazer um pedido à [API de Pesquisa de Endereço Inverso Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para encontrar um endereço. Ambos os caminhos são inquiridos abaixo.

## <a name="make-a-reverse-search-request-via-service-module"></a>Faça um pedido de pesquisa inversa através do módulo de serviço

<iframe height='500' scrolling='no' title='Obtenha informações a partir de uma coordenada (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obter informações de uma coordenada (Módulo de Serviço)</a> por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o token de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de código cria um `TokenCredential` para autenticar pedidos HTTP para Azure Maps com o token de acesso. Em seguida, passa `TokenCredential` o para e cria uma instância `atlas.service.MapsURL.newPipeline()` [pipeline.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline) O `searchURL` representa uma URL para as operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) de mapas Azure.

O terceiro bloco de código atualiza o estilo do cursor do rato a um ponteiro e cria um objeto [pop-up.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup#open) Pode ver [adicionar um pop-up no mapa](./map-add-popup.md) para obter instruções.

O quarto bloco de código adiciona um ouvinte [do evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events)de clique de rato . Quando acionado, cria uma consulta de pesquisa com as coordenadas do ponto clicado. Em seguida, utiliza o método [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)para consultar a [API reversa do endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para o endereço das coordenadas. Uma coleção de recurso GeoJSON é então extraída usando o `geojson.getFeatures()` método da resposta.

O quinto bloco de código configura o conteúdo popup HTML para exibir o endereço de resposta para a posição de coordenada clicada.

A mudança de cursor, o objeto popup e o evento de clique são todos criados no ouvinte do evento de [carga](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events)do mapa. Esta estrutura de código garante que o mapa é totalmente carregado antes de recuperar as informações das coordenadas.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Faça um pedido de pesquisa inversa via Fetch API

Clique no mapa para fazer um pedido de geocódigo invertido para esse local usando a fetch.

<iframe height='500' scrolling='no' title='Obter informações a partir de uma coordenada' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Obter informações de uma coordenada</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o token de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de código atualiza o estilo do cursor do rato para um ponteiro. Instantisa um objeto [pop-up.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup#open) Pode ver [adicionar um pop-up no mapa](./map-add-popup.md) para obter instruções.

O terceiro bloco de código adiciona um ouvinte de eventos para cliques de rato. Ao clicar num clique de rato, utiliza a [API Fetch](https://fetch.spec.whatwg.org/) para consultar a API de Procura de Endereço Inverso de [Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para o endereço de coordenadas clicada. Para obter uma resposta bem sucedida, recolhe o endereço para a localização clicada. Define o conteúdo e a posição pop-up utilizando a função [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) da classe popup.

A mudança de cursor, o objeto popup e o evento de clique são todos criados no ouvinte do evento de [carga](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events)do mapa. Esta estrutura de código garante que o mapa carrega completamente antes de recuperar as informações das coordenadas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Melhores práticas para a utilização do serviço de pesquisa](how-to-use-best-practices-for-search.md)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar as direções de A para B](./map-route.md)

> [!div class="nextstepaction"]
> [Mostrar trânsito](./map-show-traffic.md)
