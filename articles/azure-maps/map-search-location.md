---
title: Mostrar resultados da pesquisa em um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a executar um pedido de pesquisa usando o Microsoft Azure Maps Web SDK e exibirá os resultados no mapa.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 08d9245201a8e0899d47847eb10dcdff3c0d8d16
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984681"
---
# <a name="show-search-results-on-the-map"></a>Mostrar resultados da pesquisa no mapa

Este artigo mostra como procurar localização de interesse e mostrar os resultados da pesquisa no mapa.

Há duas maneiras de procurar um local de interesse. Uma maneira é usar um módulo de serviço para fazer uma solicitação de pesquisa. A outra maneira é fazer uma solicitação de pesquisa para a [API de pesquisa difusa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) por meio da [API de busca](https://fetch.spec.whatwg.org/). Ambas as maneiras são discutidas abaixo.

## <a name="make-a-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa em um mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Mostrar resultados da pesquisa em um mapa (módulo de serviço)</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco constrói um objeto de mapa e define o mecanismo de autenticação para utilizar o símbolo de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma `TokenCredential` para autenticar solicitações HTTP para mapas do Azure com o token de acesso. Em seguida, ele passa o `TokenCredential` para `atlas.service.MapsURL.newPipeline()` e cria uma instância de [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . O `searchURL` representa uma URL para operações de [pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Maps.

O terceiro bloco de código cria um objeto de fonte de dados usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e adiciona os resultados da pesquisa a ele. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar os dados baseados em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  Uma camada de símbolo é então criada. A fonte de dados é adicionada à camada de símbolo, que é adicionada ao mapa.

O quarto bloco de código usa o método [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) no [módulo de serviço](how-to-use-services-module.md). Ele permite que você execute uma pesquisa de texto de forma livre por meio da [API REST de pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para pesquisar o ponto de interesse. Obtenha pedidos para a API De pesquisa fuzzy pode lidar com qualquer combinação de inputs difusos. Uma coleção de recursos geojson da resposta é extraída usando o método `geojson.getFeatures()` e adicionada à fonte de dados, o que resulta automaticamente nos dados sendo renderizados no mapa por meio da camada de símbolo.

O último bloco de código ajusta os limites da câmara para o mapa utilizando a propriedade [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

O pedido de pesquisa, fonte de dados, camada de símbolo e limites da câmara estão dentro do ouvinte pronto do [mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Queremos garantir que os resultados são apresentados após a carga total do mapa.


## <a name="make-a-search-request-via-fetch-api"></a>Fazer uma solicitação de pesquisa por meio da API de busca

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa em um mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Mostrar resultados da pesquisa em um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Define o mecanismo de autenticação para utilizar o símbolo de acesso. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria uma URL para a qual fazer uma solicitação de pesquisa. Ele também cria duas matrizes para armazenar limites e Pins para os resultados da pesquisa.

O terceiro bloco de código utiliza a [Fetch API](https://fetch.spec.whatwg.org/). A [Fetch API](https://fetch.spec.whatwg.org/) é usada para fazer um pedido à [Pesquisa De Fuzzy Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar os pontos de interesse. A API de pesquisa difusa pode lidar com qualquer combinação de entradas difusas. Em seguida, ele manipula e analisa a resposta de pesquisa e adiciona os Pins de resultado à matriz searchPins.

O quarto bloco de código cria um objeto de origem de dados utilizando a classe [DataSource.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) No código, adicionamos resultados de pesquisa ao objeto de origem. Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar os dados baseados em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. Uma camada de símbolo é então criada. A fonte de dados é adicionada à camada de símbolo, que é adicionada ao mapa.

O último bloco de código cria um objeto [DelimitadorBox.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) Utiliza a matriz de resultados e, em seguida, ajusta os limites da câmara para o mapa utilizando o [conjunto Camera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)do Mapa . Em seguida, ele renderiza os Pins de resultado.

O pedido de pesquisa, a fonte de dados, a camada de símbolo e os limites da câmara estão definidos dentro do ouvinte do [mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que os resultados são apresentados após a carga total do mapa.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a **pesquisa difusa**:

> [!div class="nextstepaction"]
> [API de pesquisa difusa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Obter informações de uma coordenada](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Mostrar instruções de a a B](./map-route.md)
