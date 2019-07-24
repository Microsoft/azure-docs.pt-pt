---
title: Usar limites geográficos para filtrar os resultados da API de pesquisa comercial local do Bing
titleSuffix: Azure Cognitive Services
description: Use este artigo para aprender a filtrar os resultados da pesquisa da API de pesquisa de negócios local do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: e47a2ab8db17089773fd9a439b6dff225d6a8a29
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423308"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Usar limites geográficos para filtrar os resultados da API de pesquisa comercial local do Bing

A API de pesquisa de negócios local do Bing permite definir limites na área geográfica específica que você gostaria de Pesquisar usando os parâmetros `localCircularView` de `localMapView` consulta ou. Certifique-se de usar apenas um parâmetro em suas consultas. 

Se um termo de pesquisa contiver uma localização geográfica explícita, a API comercial local do Bing a usará automaticamente para definir limites para os resultados da pesquisa. Por exemplo, se o termo de pesquisa `sailing in San Diego`for `San Diego` , será usado como o local e quaisquer outros locais especificados nos parâmetros de consulta ou nos cabeçalhos de usuário serão ignorados. 

Se uma localização geográfica não for detectada no termo de pesquisa e nenhuma localização geográfica for especificada usando os parâmetros de consulta, a API de pesquisa de negócios local do Bing tentará determinar o `X-Search-ClientIP` local `X-Search-Location` a partir dos cabeçalhos ou das solicitações. Se nenhum cabeçalho for especificado, a API determinará o local a partir do IP do cliente da solicitação ou das coordenadas de GPS para dispositivos móveis.

## <a name="localcircularview"></a>localCircularView

O `localCircularView` parâmetro cria uma área geográfica circular em um conjunto de coordenadas de latitude/longitude, definido por um raio. Ao usar esse parâmetro, as respostas da API de pesquisa de negócios local do Bing só incluirão locais dentro deste círculo `localMapView` , diferentemente do parâmetro que pode incluir locais um pouco fora da área de pesquisa.

Para especificar uma área de pesquisa geográfica circular, escolha uma latitude e longitude para servir como o centro do círculo e um raio em metros. Esse parâmetro pode então ser anexado a uma cadeia de caracteres de consulta, por `q=Restaurants&localCircularView=47.6421,-122.13715,5000`exemplo:.

Consulta completa:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

O `localMapView` parâmetro especifica uma área geográfica retangular a ser pesquisada, usando dois conjuntos de coordenadas para especificar seus cantos sudeste e noroeste. Ao usar esse parâmetro, as respostas da API de pesquisa de negócios local do Bing podem incluir locais dentro e apenas fora da área especificada `localCircularView` , ao contrário do parâmetro, que inclui apenas locais na área de pesquisa.

Para especificar uma área de pesquisa retangular, escolha dois conjuntos de coordenadas de latitude/longitude para servir como os cantos sudeste e noroeste do limite. Certifique-se de definir as coordenadas do sudeste primeiro, como no exemplo a `localMapView=47.619987,-122.181671,47.6421,-122.13715`seguir:.

Consulta completa:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido do Java da pesquisa de negócios local](quickstarts/local-search-java-quickstart.md)
- [Início rápido de C# pesquisa comercial local](quickstarts/local-quickstart.md)
- [Início rápido do nó de pesquisa comercial local](quickstarts/local-search-node-quickstart.md)
- [Início rápido do Python de pesquisa comercial local](quickstarts/local-search-python-quickstart.md)
