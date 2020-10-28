---
title: Mostrar os resultados da pesquisa num mapa Microsoft Azure Maps
description: Neste artigo, você vai aprender a executar um pedido de pesquisa usando o Microsoft Azure Maps Web SDK e exibir os resultados no mapa.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 647a8fc25f27ef7f441ed7459ecd543d4f35581e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895278"
---
# <a name="show-search-results-on-the-map"></a>Mostrar os resultados da pesquisa no mapa

Este artigo mostra-lhe como procurar a localização do interesse e mostrar os resultados da pesquisa no mapa.

Há duas maneiras de procurar uma localização de interesse. Uma maneira é usar um módulo de serviço para fazer um pedido de pesquisa. A outra forma é fazer um pedido de pesquisa para [Azure Maps Fuzzy pesquisar API](/rest/api/maps/search/getsearchfuzzy) através da [Fetch API](https://fetch.spec.whatwg.org/). Ambos os caminhos são discutidos abaixo.

## <a name="make-a-search-request-via-service-module"></a>Faça um pedido de pesquisa através de módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar os resultados da pesquisa num mapa (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os resultados da pesquisa do Pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show num mapa (Módulo de Serviço)</a> por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o token de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de código cria um `TokenCredential` pedido HTTP para autenticar a Azure Maps com o token de acesso. Em seguida, passa `TokenCredential` o para e cria uma instância `atlas.service.MapsURL.newPipeline()` [pipeline.](/javascript/api/azure-maps-rest/atlas.service.pipeline) O `searchURL` representa uma URL para as operações de [pesquisa](/rest/api/maps/search) de mapas Azure.

O terceiro bloco de código cria um objeto de origem de dados utilizando a classe [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) e adiciona-lhe resultados de pesquisa. Uma [camada de símbolo](/javascript/api/azure-maps-control/atlas.layer.symbollayer) utiliza texto ou ícones para tornar os dados baseados em ponto embrulhados no [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) como símbolos no mapa.  Uma camada de símbolo é então criada. A fonte de dados é adicionada à camada de símbolo, que é adicionada ao mapa.

O quarto bloco de código utiliza o método [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) no [módulo de serviço](how-to-use-services-module.md). Permite-lhe realizar uma pesquisa de texto em formulário gratuito através da [API de repouso Get Search Fuzzy](/rest/api/maps/search/getsearchfuzzy) para procurar ponto de interesse. Obtenha pedidos para a API Search Fuzzy pode lidar com qualquer combinação de entradas difusas. Uma recolha de recursos GeoJSON da resposta é então extraída usando o `geojson.getFeatures()` método e adicionada à fonte de dados, o que resulta automaticamente na prestação de dados no mapa através da camada de símbolo.

O último bloco de código ajusta os limites da câmara para o mapa usando a propriedade [Do Mapa.](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)

O pedido de pesquisa, fonte de dados, camada de símbolo e limites de câmara estão dentro do [ouvinte](/javascript/api/azure-maps-control/atlas.map#events) do evento do mapa. Queremos garantir que os resultados são apresentados após a carga total do mapa.


## <a name="make-a-search-request-via-fetch-api"></a>Faça um pedido de pesquisa via Fetch API

<iframe height='500' scrolling='no' title='Mostrar os resultados da pesquisa num mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>resultados</a> da pesquisa do Pen Show num mapa de Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Define o mecanismo de autenticação para utilizar o token de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de código cria um URL para fazer um pedido de pesquisa para. Também cria duas matrizes para armazenar limites e pinos para resultados de pesquisa.

O terceiro bloco de código utiliza a [API Fetch](https://fetch.spec.whatwg.org/). A [API Fetch](https://fetch.spec.whatwg.org/) é usada para fazer um pedido à [Azure Maps Fuzzy pesquisar API](/rest/api/maps/search/getsearchfuzzy) para procurar os pontos de interesse. A API de pesquisa fuzzy pode lidar com qualquer combinação de entradas difusas. Em seguida, manuseia e analisa a resposta de pesquisa e adiciona os pinos de resultado à matriz de pinos de pesquisa.

O quarto bloco de código cria um objeto de origem de dados utilizando a classe [DataSource.](/javascript/api/azure-maps-control/atlas.source.datasource) No código, adicionamos resultados de pesquisa ao objeto de origem. Uma [camada de símbolo](/javascript/api/azure-maps-control/atlas.layer.symbollayer) utiliza texto ou ícones para tornar os dados baseados em ponto embrulhados no [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) como símbolos no mapa. Uma camada de símbolo é então criada. A fonte de dados é adicionada à camada de símbolo, que é adicionada ao mapa.

O último bloco de código cria um objeto [BoundingBox.](/javascript/api/azure-maps-control/atlas.data.boundingbox) Usa a matriz de resultados e, em seguida, ajusta os limites da câmara para o mapa usando o [conjunto do MapaCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Em seguida, torna os pinos de resultado.

O pedido de pesquisa, a fonte de dados, a camada de símbolo e os limites da câmara estão definidos dentro do ouvinte do [mapa](/javascript/api/azure-maps-control/atlas.map#events) para garantir que os resultados são apresentados após a carga total do mapa.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Melhores práticas para a utilização do serviço de pesquisa](how-to-use-best-practices-for-search.md)

Saiba mais sobre **a Fuzzy Search:**

> [!div class="nextstepaction"]
> [Azure Maps Fuzzy Search API](/rest/api/maps/search/getsearchfuzzy)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Obter informações a partir de uma coordenada](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Mostrar as direções de A para B](map-route.md)