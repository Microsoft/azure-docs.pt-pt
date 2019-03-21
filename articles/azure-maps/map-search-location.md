---
title: Mostrar resultados da pesquisa com o Azure Maps | Documentos da Microsoft
description: Como efetuar um pedido de pesquisa com o Azure Maps, em seguida, exibir os resultados num mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8ae6c8a20a05df723d3f6b394e0639f218896a85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845142"
---
# <a name="show-search-results-on-the-map"></a>Mostrar resultados da pesquisa no mapa

Este artigo mostra como procurar a localização de interesse e mostrar os resultados da pesquisa no mapa.

Existem duas formas de procurar uma localização de interesse. Uma forma é utilizar um módulo de serviço para fazer um pedido de pesquisa. A outra forma consiste em fazer um pedido de pesquisa para [API de pesquisa difusa de mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) por meio do [obter API](https://fetch.spec.whatwg.org/). Ambas as formas são abordadas abaixo.

## <a name="make-a-search-request-via-service-module"></a>Fazer um pedido de pesquisa através do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa num mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Mostrar resultados da pesquisa num mapa (módulo de serviço)</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar a chave de subscrição. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um **SubscriptionKeyCredentialPolicy** para autenticar pedidos HTTP para o Azure Maps com a chave de subscrição. Em seguida, o **atlas.service.MapsURL.newPipeline()** aceita a **SubscriptionKeyCredential** política e cria um [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instância. O **searchURL** representa um URL para o Azure Maps [pesquisa](https://docs.microsoft.com/rest/api/maps/search) operações.

O terceiro bloco de código cria uma origem de dados objeto utilizando a [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) de classe e adicionar-lhe os resultados da pesquisa. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa ou ícones de texto para processar dados com base no ponto encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  Uma camada de símbolo, em seguida, é criada e a origem de dados é adicionada para a camada de símbolo, o que é então adicionada ao mapa.

O quarto bloco de código utiliza a [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) método na [módulo do serviço](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2). Permite-lhe executar uma pesquisa de texto de forma livre através da [obter pesquisa difusa rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar o ponto de interesse. Obter a API de pesquisa difusa pode lidar com qualquer combinação de entradas difusas. Uma coleção de recursos de GeoJSON da resposta é extraída, em seguida, utilizar o **geojson.getFeatures()** método e adicionado à origem de dados, o que resulta automaticamente nos dados que está sendo renderizados no mapa através da camada de símbolo.

O último bloco de código ajusta os limites de câmera para o mapa através do mapa [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) propriedade.

A pesquisa de solicitação, a origem de dados e a camada de símbolo e os limites de câmera são criados e conjunto no mapa do pronto [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que os resultados são apresentados depois do mapa é totalmente carregado.


## <a name="make-a-search-request-via-fetch-api"></a>Fazer um pedido de pesquisa através da API de obtenção

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa num mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show pesquisa resulta num mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar a chave de subscrição. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um URL para fazer um pedido de pesquisa para. Ele também cria duas matrizes para armazenar os limites e pins para resultados da pesquisa.

O terceiro bloco de código utiliza a [API buscar](https://fetch.spec.whatwg.org/) para fazer um pedido para [API de pesquisa do Azure Maps difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar os pontos de interesse. A API de pesquisa difusa pode lidar com qualquer combinação de entradas difusas. Ele, em seguida, processa e analisa a resposta da pesquisa e adiciona os pins de resultado para a matriz de searchPins.

O quarto bloco de código cria uma origem de dados objeto utilizando a [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) de classe e adicionar-lhe os resultados da pesquisa. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa ou ícones de texto para processar dados com base no ponto encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. Uma camada de símbolo, em seguida, é criada e a origem de dados é adicionada para a camada de símbolo, o que é então adicionada ao mapa.

O último bloco de código cria um [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) com a matriz de resultados de objeto e, em seguida, ajusta os limites de câmera para o mapa através do mapa [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Ela então processa os pins de resultado.

A solicitação de pesquisa, a origem de dados e a camada de símbolo e os limites de câmera são definidos dentro do mapa [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que os resultados são apresentados depois do mapa é totalmente carregado.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre **a pesquisa difusa**:

> [!div class="nextstepaction"]
> [API de pesquisa difusa de mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Veja os artigos seguintes para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Obtenha informações a partir de uma coordenada](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Mostrar as direções da para B](./map-route.md)
