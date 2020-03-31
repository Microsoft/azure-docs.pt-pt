---
title: Mostrar resultados de pesquisa num mapa Microsoft Azure Maps
description: Neste artigo, você aprenderá a executar um pedido de pesquisa usando o Microsoft Azure Maps Web SDK e exibirá os resultados no mapa.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e82a1daee381c7bad19c83fa735d0028bef2010e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371403"
---
# <a name="show-search-results-on-the-map"></a>Mostrar resultados de pesquisa no mapa

Este artigo mostra-lhe como procurar a localização do interesse e mostrar os resultados da pesquisa no mapa.

Há duas maneiras de procurar um local de interesse. Uma maneira é usar um módulo de serviço para fazer um pedido de pesquisa. A outra forma é fazer um pedido de pesquisa para a API de pesquisa de [Azure Maps Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) através da [Fetch API](https://fetch.spec.whatwg.org/). Ambos os lados são discutidos abaixo.

## <a name="make-a-search-request-via-service-module"></a>Faça um pedido de pesquisa através do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar resultados de pesquisa num mapa (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja os resultados de pesquisa do Pen Show num<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapa <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>(Módulo de Serviço)</a> por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o símbolo de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de `TokenCredential` código cria um pedido de HTTP para autenticar pedidos de HTTP para o Azure Maps com o sinal de acesso. Em seguida, `TokenCredential` `atlas.service.MapsURL.newPipeline()` passa o para e cria uma instância [pipeline.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) O `searchURL` representa um URL para operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) de mapas azure.

O terceiro bloco de código cria um objeto de origem de dados utilizando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e adiciona-lhe resultados de pesquisa. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utiliza texto ou ícones para tornar os dados baseados em pontos embrulhados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  Uma camada de símbolo é então criada. A fonte de dados é adicionada à camada de símbolo, que é adicionada ao mapa.

O quarto bloco de código utiliza o método [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) no [módulo de serviço](how-to-use-services-module.md). Permite-lhe efetuar uma pesquisa de texto gratuita através da API de [repouso Get Search Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar ponto de interesse. Obtenha pedidos para a API De pesquisa fuzzy pode lidar com qualquer combinação de inputs difusos. Uma recolha de funcionalidades GeoJSON da `geojson.getFeatures()` resposta é então extraída utilizando o método e adicionada à fonte de dados, o que resulta automaticamente na prestação dos dados no mapa através da camada de símbolo.

O último bloco de código ajusta os limites da câmara para o mapa utilizando a propriedade [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

O pedido de pesquisa, fonte de dados, camada de símbolo e limites da câmara estão dentro do ouvinte do [mapa.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) Queremos garantir que os resultados são apresentados após a carga total do mapa.


## <a name="make-a-search-request-via-fetch-api"></a>Faça um pedido de pesquisa via Fetch API

<iframe height='500' scrolling='no' title='Mostrar resultados de pesquisa num mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja os <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>resultados</a> de pesquisa do Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>Show num mapa do Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Define o mecanismo de autenticação para utilizar o símbolo de acesso. Pode ver [criar um mapa](./map-create.md) para instruções.

O segundo bloco de código cria um URL para fazer um pedido de pesquisa. Também cria duas matrizes para armazenar limites e pinos para resultados de pesquisa.

O terceiro bloco de código utiliza a [Fetch API](https://fetch.spec.whatwg.org/). A [Fetch API](https://fetch.spec.whatwg.org/) é usada para fazer um pedido à [Pesquisa De Fuzzy Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar os pontos de interesse. A API de pesquisa fuzzy pode lidar com qualquer combinação de inputs difusos. Em seguida, lida e analisa a resposta de pesquisa e adiciona os pinos de resultado à matriz de pesquisaPins.

O quarto bloco de código cria um objeto de origem de dados utilizando a classe [DataSource.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) No código, adicionamos resultados de pesquisa ao objeto de origem. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) utiliza texto ou ícones para tornar os dados baseados em pontos embrulhados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. Uma camada de símbolo é então criada. A fonte de dados é adicionada à camada de símbolo, que é adicionada ao mapa.

O último bloco de código cria um objeto [DelimitadorBox.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) Utiliza a matriz de resultados e, em seguida, ajusta os limites da câmara para o mapa utilizando o [conjunto Camera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)do Mapa . Em seguida, torna os pinos de resultado.

O pedido de pesquisa, a fonte de dados, a camada de símbolo e os limites da câmara estão definidos dentro do ouvinte do [mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que os resultados são apresentados após a carga total do mapa.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Boas práticas para usar o serviço de pesquisa](how-to-use-best-practices-for-search.md)

Saiba mais sobre **a Pesquisa Fuzzy:**

> [!div class="nextstepaction"]
> [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Obter informações a partir de uma coordenada](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Mostrar as direções de A para B](map-route.md)
