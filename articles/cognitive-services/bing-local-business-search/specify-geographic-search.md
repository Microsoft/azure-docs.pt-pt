---
title: Use limites geográficos para filtrar os resultados da API local de pesquisa de negócios de Bing
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para saber filtrar os resultados da pesquisa a partir da API de Pesquisa de Negócios Local Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 213457bc583494bbe039269b96b25990f7d0a961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906251"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Use limites geográficos para filtrar os resultados da API local de pesquisa de negócios de Bing

A API de Pesquisa de Negócios Local Bing permite-lhe definir limites na `localCircularView` `localMapView` área geográfica específica que deseja pesquisar utilizando os parâmetros ou consultas. Certifique-se de que utiliza apenas um parâmetro nas suas consultas. 

Se um termo de pesquisa contiver uma localização geográfica explícita, a API local de Bing irá usá-lo automaticamente para definir limites para os resultados da pesquisa. Por exemplo, se o `sailing in San Diego`termo `San Diego` de pesquisa for , então será usado como a localização e quaisquer outros locais especificados nos parâmetros de consulta ou cabeçalhos do utilizador serão ignorados. 

Se uma localização geográfica não for detetada no período de pesquisa, e não for especificada nenhuma localização geográfica utilizando os parâmetros `X-Search-ClientIP` de `X-Search-Location` consulta, a API local de pesquisa de negócios de Bing tentará determinar a localização a partir dos cabeçalhos ou cabeçalhos do pedido. Se nenhum dos cabeçalhos for especificado, a API determinará a localização a partir do IP do cliente do pedido, ou coordenadas GPS para dispositivos móveis.

## <a name="localcircularview"></a>localCircularView

O `localCircularView` parâmetro cria uma área geográfica circular em torno de um conjunto de coordenadas de latitude/longitude, definidas por um raio. Ao utilizar este parâmetro, as respostas da API local de pesquisa de `localMapView` negócios de Bing apenas incluirão localizações dentro deste círculo, ao contrário do parâmetro que pode incluir localizações ligeiramente fora da área de pesquisa.

Para especificar uma área de pesquisa geográfica circular, escolha uma latitude e longitude para servir como o centro do círculo, e um raio em metros. Este parâmetro pode então ser anexado a uma `q=Restaurants&localCircularView=47.6421,-122.13715,5000`corda de consulta, por exemplo: .

Consulta completa:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

O `localMapView` parâmetro especifica uma área geográfica retangular para procurar, utilizando dois conjuntos de coordenadas para especificar os seus cantos sudeste e noroeste. Ao utilizar este parâmetro, as respostas da API local de pesquisa de negócios de `localCircularView` Bing podem incluir localizações dentro e fora da área especificada, ao contrário do parâmetro, que inclui apenas localizações dentro da área de pesquisa.

Para especificar uma área de busca retangular, escolha dois conjuntos de coordenadas de latitude/longitude para servir como os cantos sudeste e noroeste da fronteira. Certifique-se de definir as coordenadas sudeste `localMapView=47.619987,-122.181671,47.6421,-122.13715`primeiro, como no seguinte exemplo: .

Consulta completa:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Passos seguintes
- [Pesquisa de negócios locais Java Quickstart](quickstarts/local-search-java-quickstart.md)
- [Pesquisa de negócios local C# Quickstart](quickstarts/local-quickstart.md)
- [Nó de pesquisa de negócios local Quickstart](quickstarts/local-search-node-quickstart.md)
- [Pesquisa de negócios local Python quickstart](quickstarts/local-search-python-quickstart.md)
