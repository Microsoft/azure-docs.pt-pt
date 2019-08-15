---
title: Mostrar resultados da pesquisa com o Azure Maps | Microsoft Docs
description: Como executar uma solicitação de pesquisa com o Azure Maps, em seguida, exibir os resultados no SDK da Web do Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7c4c78e1d21754d42391a3762e9f7ed199a7376b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975970"
---
# <a name="show-search-results-on-the-map"></a>Mostrar resultados da pesquisa no mapa

Este artigo mostra como procurar localização de interesse e mostrar os resultados da pesquisa no mapa.

Há duas maneiras de procurar um local de interesse. Uma maneira é usar um módulo de serviço para fazer uma solicitação de pesquisa. A outra maneira é fazer uma solicitação de pesquisa para a [API de pesquisa difusa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) por meio da [API de busca](https://fetch.spec.whatwg.org/). Ambas as maneiras são discutidas abaixo.

## <a name="make-a-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa em um mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Mostrar resultados da pesquisa em um mapa (módulo de serviço)</a> pelo Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um `TokenCredential` para autenticar solicitações HTTP para mapas do Azure com o token de acesso. Em seguida, ele `TokenCredential` passa `atlas.service.MapsURL.newPipeline()` o para e cria uma instância de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . O `searchURL` representa uma URL para operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Maps.

O terceiro bloco de código cria um objeto de fonte de dados usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e adiciona os resultados da pesquisa a ele. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar os dados baseados em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  Em seguida, uma camada de símbolo é criada e a fonte de dados é adicionada à camada de símbolo, que é adicionada ao mapa.

O quarto bloco de código usa o método [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) no [módulo de serviço](how-to-use-services-module.md). Ele permite que você execute uma pesquisa de texto de forma livre por meio da [API REST de pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para pesquisar o ponto de interesse. Get Search API difusa pode lidar com qualquer combinação de entradas difusas. Uma coleção de recursos geojson da resposta é então extraída usando `geojson.getFeatures()` o método e adicionada à fonte de dados, o que resulta automaticamente nos dados sendo processados no mapa por meio da camada de símbolo.

O último bloco de código ajusta os limites da câmera para o mapa usando a propriedade setcamera do [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapa.

A solicitação de pesquisa, a fonte de dados e a camada de símbolo e os limites da câmera são criados e definidos no ouvinte de [evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pronto do mapa para garantir que os resultados sejam exibidos depois que o mapa for totalmente carregado.


## <a name="make-a-search-request-via-fetch-api"></a>Fazer uma solicitação de pesquisa por meio da API de busca

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa em um mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Mostrar resultados da pesquisa em um mapa</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o token de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma URL para a qual fazer uma solicitação de pesquisa. Ele também cria duas matrizes para armazenar limites e Pins para os resultados da pesquisa.

O terceiro bloco de código usa a [API de busca](https://fetch.spec.whatwg.org/) para fazer uma solicitação para a [API de pesquisa difusa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para pesquisar os pontos de interesse. A API de pesquisa difusa pode lidar com qualquer combinação de entradas difusas. Em seguida, ele manipula e analisa a resposta de pesquisa e adiciona os Pins de resultado à matriz searchPins.

O quarto bloco de código cria um objeto de fonte de dados usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e adiciona os resultados da pesquisa a ele. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar os dados baseados em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. Em seguida, uma camada de símbolo é criada e a fonte de dados é adicionada à camada de símbolo, que é adicionada ao mapa.

O último bloco de código cria um objeto [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) usando a matriz de resultados e, em seguida, ajusta os limites da câmera para o mapa usando a microcamera do mapa. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) Em seguida, ele renderiza os Pins de resultado.

A solicitação de pesquisa, a fonte de dados e a camada de símbolo e os limites da câmera são definidos dentro do ouvinte de [eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa para garantir que os resultados sejam exibidos depois que o mapa for totalmente carregado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a **pesquisa difusa**:

> [!div class="nextstepaction"]
> [API de pesquisa difusa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Obter informações de uma coordenada](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Mostrar instruções de a a B](./map-route.md)
