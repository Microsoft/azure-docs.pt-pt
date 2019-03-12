---
title: Utilizar limites geográficos para filtrar os resultados da API de pesquisa de negócios locais do Bing | Documentos da Microsoft
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para saber como filtrar os resultados da pesquisa da API de pesquisa de negócios locais do Bing.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 193d0b25276b90f6047943ded83f2000dd4d3436
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775718"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Utilizar limites geográficos para filtrar os resultados da API de pesquisa de negócios locais do Bing

A API de pesquisa de negócios locais do Bing permite-lhe definir limites da área geográfica específica que pretende pesquisar, utilizando o `localCircularView` ou `localMapView` parâmetros de consulta. Certifique-se de que usar apenas um parâmetro em suas consultas. 

Se um termo de pesquisa contém uma localização geográfica explícita, a API de negócios locais do Bing utilizarão automaticamente-lo para definir limites para os resultados da pesquisa. Por exemplo, se é o termo de pesquisa `sailing in San Diego`, em seguida, `San Diego` será utilizado como a localização e qualquer outro especificado localizações nos parâmetros de consulta ou cabeçalhos de usuário serão ignorados. 

Se uma localização geográfica não é detetada o termo de pesquisa, e nenhuma localização geográfica for especificada, utilizando os parâmetros de consulta, o Local Business API de pesquisa Bing tentará determinar a localização a partir da solicitação `X-Search-ClientIP` ou `X-Search-Location` cabeçalhos. Se não for especificado nenhum cabeçalho, a API irá determinar a localização do IP de cliente do pedido ou as coordenadas GPS para dispositivos móveis.

## <a name="localcircularview"></a>localCircularView

O `localCircularView` parâmetro cria uma área geográfica circular em torno de um conjunto de coordenadas de latitude/longitude, definido por um raio. Ao utilizar este parâmetro, respostas a partir da API de pesquisa de negócios Local do Bing apenas incluirá localizações dentro este círculo, ao contrário do `localMapView` parâmetro que pode incluir a localizações ligeiramente fora da área de pesquisa.

Para especificar uma área de pesquisa geográfica circular, escolha uma latitude e longitude para servir como o centro do círculo e um raio em metros. Este parâmetro pode, em seguida, ser anexado a uma cadeia de consulta, por exemplo: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`.

Consulta concluída:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

O `localMapView` parâmetro especifica uma área retangular geográfica para pesquisar, com dois conjuntos de coordenadas para especificar o Sudeste da Ásia e noroeste cantos. Ao utilizar este parâmetro, respostas a partir da API de pesquisa de negócios Local do Bing podem incluir locais dentro e fora da área especificada, ao contrário do `localCircularView` parâmetro, que inclui apenas as localizações dentro da área de pesquisa.

Para especificar uma área retangular da pesquisa, escolha dois conjuntos de coordenadas de latitude/longitude para servir o Sudeste da Ásia e cantos Noroeste do limite. Certifique-se de que definem as coordenadas Sudeste em primeiro lugar, como no seguinte exemplo: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Consulta concluída:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido de Java de pesquisa comercial](quickstarts/local-search-java-quickstart.md)
- [Pesquisa de negócios locais C# início rápido](quickstarts/local-quickstart.md)
- [Início rápido do nó de pesquisa comercial](quickstarts/local-search-node-quickstart.md)
- [Guia de introdução de Python de pesquisa de negócios local](quickstarts/local-search-python-quickstart.md)
