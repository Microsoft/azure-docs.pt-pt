---
title: Utilize limites geográficos para filtrar os resultados da API de Pesquisa local de Negócios Bing
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para aprender a filtrar os resultados da pesquisa da API de Pesquisa local de Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 2e72abd777ac4834385fd916b730d438b8540203
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490266"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Utilize limites geográficos para filtrar os resultados da API de Pesquisa local de Negócios Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A API de Pesquisa local de Bing permite-lhe definir limites na área geográfica específica que gostaria de pesquisar utilizando os `localCircularView` parâmetros ou `localMapView` parâmetros de consulta. Certifique-se de que utiliza apenas um parâmetro nas suas consultas. 

Se um termo de pesquisa contiver uma localização geográfica explícita, a API de negócios locais de Bing utilizará automaticamente para definir limites para os resultados da pesquisa. Por exemplo, se o termo de pesquisa for `sailing in San Diego` , então `San Diego` será usado como o local e quaisquer outros locais especificados nos parâmetros de consulta ou cabeçalhos do utilizador serão ignorados. 

Se uma localização geográfica não for detetada no termo de pesquisa, e nenhuma localização geográfica for especificada usando os parâmetros de consulta, a API de Pesquisa local de Bing tentará determinar a localização a partir dos `X-Search-ClientIP` indicados ou `X-Search-Location` cabeçalhos do pedido. Se nenhum dos cabeçalhos for especificado, a API determinará a localização a partir do IP do cliente do pedido, ou coordenadas GPS para dispositivos móveis.

## <a name="localcircularview"></a>localCircularView

O `localCircularView` parâmetro cria uma área geográfica circular em torno de um conjunto de coordenadas de latitude/longitude, definidas por um raio. Ao utilizar este parâmetro, as respostas da API de Pesquisa local de Bing incluem apenas localizações dentro deste círculo, ao contrário do `localMapView` parâmetro que pode incluir locais ligeiramente fora da área de pesquisa.

Para especificar uma área de pesquisa geográfica circular, escolha uma latitude e longitude para servir como o centro do círculo, e um raio de metros. Este parâmetro pode então ser anexado a uma cadeia de consulta, por exemplo: `q=Restaurants&localCircularView=47.6421,-122.13715,5000` .

Consulta completa:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

O `localMapView` parâmetro especifica uma área geográfica retangular para procurar, utilizando dois conjuntos de coordenadas para especificar os seus cantos sudeste e noroeste. Ao utilizar este parâmetro, as respostas da API de Pesquisa Local de Pesquisa de Negócios de Bing podem incluir localizações dentro e fora da área especificada, ao contrário do `localCircularView` parâmetro, que inclui apenas localizações dentro da área de pesquisa.

Para especificar uma área de pesquisa retangular, escolha dois conjuntos de coordenadas de latitude/longitude para servir como os cantos sudeste e noroeste da fronteira. Certifique-se de definir primeiro as coordenadas do sudeste, como no exemplo seguinte: `localMapView=47.619987,-122.181671,47.6421,-122.13715` .

Consulta completa:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Passos seguintes
- [Pesquisa de negócios local Java Quickstart](quickstarts/local-search-java-quickstart.md)
- [Pesquisa de negócios locais C# Quickstart](quickstarts/local-quickstart.md)
- [Nó de pesquisa de negócios local Quickstart](quickstarts/local-search-node-quickstart.md)
- [Pesquisa de negócios locais Python quickstart](quickstarts/local-search-python-quickstart.md)