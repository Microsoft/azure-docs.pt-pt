---
title: Mostrar informações sobre uma coordenada com o Azure Maps | Documentos da Microsoft
description: Como exibir informações sobre um endereço no mapa, quando um usuário seleciona uma coordenada
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4fdf2e884ca896be1dcb8eea810958076b8b8eb7
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668672"
---
# <a name="get-information-from-a-coordinate"></a>Obter informações a partir de uma coordenada

Este artigo mostra como fazer uma pesquisa inversa de endereços que mostra o endereço de uma localização de pop-up clicado.

Existem duas formas de fazer uma pesquisa inversa de endereço. Uma delas é a consulta a [API de pesquisa de endereço de inverter mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) por meio de um módulo de serviço. A outra forma consiste em efetuar uma [XMLHttpRequest](https://xhr.spec.whatwg.org/) para a API para localizar um endereço. Ambas as formas são pesquisadas abaixo.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fazer um pedido de pesquisa inversa através do módulo de serviço

<iframe height='500' scrolling='no' title='Obtenha informações a partir de uma coordenada (módulo de serviço)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>obter informações a partir de uma coordenada (módulo de serviço)</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

A linha no segundo bloco de código cria uma instância de um serviço de cliente.

O terceiro bloco de código atualiza o estilo de cursor do mouse num ponteiro e [pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objeto. Pode ver [adicionar um pop-up no mapa](./map-add-popup.md) para obter instruções.

O quarto bloco de código adiciona uma [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para cliques do mouse. Após um clique do mouse, ele cria uma consulta de pesquisa com as coordenadas do ponto clicado. Em seguida, utiliza o mapa [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchaddressreverseresponse?view=azure-iot-typescript-latest) ponto final para o endereço para as coordenadas de consulta.

Para uma resposta com êxito, recolhe o endereço para a localização de cliques e define o conteúdo do popup e a posição via [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) função da classe pop-up.

A alteração de cursor, um objeto de pop-up e o evento de clique são criados do mapa [serviço de escuta de eventos de carga](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o mapa cargas totalmente antes das informações de coordenadas podem ser obtidas.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Fazer um pedido de pesquisa inversa via XMLHttpRequest

<iframe height='500' scrolling='no' title='Obter informações a partir de uma coordenada' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>obter informações a partir de uma coordenada</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código atualiza o estilo de cursor do mouse num ponteiro e [pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objeto. Pode ver [adicionar um pop-up no mapa](./map-add-popup.md) para obter instruções.

O terceiro bloco de código adiciona uma escuta de eventos de cliques do mouse. Após um clique do mouse, envia uma [XMLHttpRequest](https://xhr.spec.whatwg.org/) ao [API de pesquisa de endereço de inverter mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) a consulta para o endereço de coordenadas clicado. Para uma resposta com êxito, recolhe o endereço para a localização de cliques e define o conteúdo do popup e a posição via [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) função da classe pop-up.

A alteração de cursor, um objeto de pop-up e o evento de clique são criados do mapa [serviço de escuta de eventos de carga](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o mapa cargas totalmente antes das informações de coordenadas podem ser obtidas.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Veja os artigos seguintes para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar as direções da para B](./map-route.md)

> [!div class="nextstepaction"]
> [Mostrar trânsito](./map-show-traffic.md)
