---
title: Categorias de pesquisa para a API de Pesquisa local de Negócios de Bing
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para saber como especificar categorias de pesquisa para o ponto final de pesquisa de API da Bing Local Business.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 19b769d1fff431f95c20e607c17747f2ff483d2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487189"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Categorias de pesquisa para a API de Pesquisa local de Negócios de Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A API de Pesquisa local de Negócios de Bing permite-lhe pesquisar entidades empresariais locais em diversas categorias, com prioridade dada aos resultados que fecham a localização de um utilizador. Pode incluir estas pesquisas em pesquisas juntamente com os `localCircularView` parâmetros e `localMapView` [parâmetros.](specify-geographic-search.md)


## <a name="toplevel-categories"></a>Categorias TopLevel 

Os seguintes tipos definem as principais categorias de pesquisa.  Mais de uma categoria pode ser especificada usando uma lista delimitada por vírgula atribuída ao `localCategories` parâmetro.  
- EatDrink 
- VerDo 
- Loja 
- HotéisAndMotels 
- BanksAndCreditUnions 
- Estacionamento 
- Hospitais 

## <a name="sub-categories"></a>Sub-Categorias
As subcategorias são aprovadas da mesma forma `localCategories` que. As subcategorias são categorias mais específicas. São subordinados no sentido de que se especificar uma categoria C e uma das suas subcategorias S na mesma lista delimitada em vírgula, receberá os mesmos resultados que se especificasse apenas C.

### <a name="eat-drink"></a>Comer Bebida

> CervejariasAndBrewPubs, CocktailLounges, AfricanRestaurants, AmericanRestaurants, Bagels, BarbecueRestaurants, Taverns, SportsBars, Bares, BarsGrillsAndPubs, BuffetRestaurants. BelgaRestaurants, BritishRestaurants, CafeRestaurants, CaribbeanRestaurants, ChinesesRestaurants, CoffeeAndTea, Delicatessens, DeliveryService, Diners, DiscountStores, Donuts, FastFood, FrenchRestaurants, FrozenYogurt, GermanRestaurants, Supermercados, GregoRestaurants, Mercearias, HawaiianRestaurants, HúngaroRestaurants, IceCreamAndFrozenDess, IndianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaus, ItalianRestaurants, ItalianRestas, ItalianRestaurants, ItalianRestaurants, ItalianRess, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaurants, ItalianRestaus, JaponesesRestaurants, Sumos, CoreanoRestaurants, Lojas de Licor, MexicanoRestaurants, MiddleEasternRestaurants, Pizza, PolacoRestaurants, PortuguesesRestaurants, Pretzels, Restaurantes, RussianAndUkrainianRestaurants Sanduíches, Frutos do MarRestaurants, EspanhóisRestaurants, SteakHouseRestaurants, SushiRestaurants, TakeAway, ThaiRestaurants, TurkishRestaurants, VegetarianAndVeganRestaurants, VietnamitaRestaurants

### <a name="see-do"></a>Ver Fazer

> Parques de Diversões, Atrações, Carnavales, Casinos, Marcos EHistoricalSites, MiniatureGolfCourses, MovieTheaters, Museus, Parques, SightseeingTours, Informação Turística, Jardins Zoológicos

### <a name="shop"></a>Loja

> Lojas de Antiguidades, Livrarias, CDAndRecordStores, Lojas de Roupa Infantil, CigarAndTobaccoShops, ComicBookStores, DepartmentStores, DiscountStores, FleaMarketsAndBazaars, Lojas de Mobiliário, HomeImprovementStores, JoalhariaAndWatchesStores, KitchenwareStores, Lojas de Licor, MallsAndShoppingCenters, Lojas de Roupas, Lojas de Música, Lojas de Lojas, PetShops, PetSupplyStores, SchoolAndOfficeSupplyS, ShoeStores, SportingGoodsstores, ToyAndGamestores, VitaminAndSupplements, WomenSstores, WomenSstores, WomenStores,


## <a name="examples-of-local-categories-search"></a>Exemplos de pesquisa de categorias locais

Os seguintes exemplos GET resultados de acordo com o `localCategories` parâmetro:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

A seguinte consulta limita o número de resultados 'hospitalares' aos três primeiros devolvidos da API de Pesquisa Local de Bing:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

O exemplo seguinte, a resposta JSON inclui três hospitais na grande área de Seattle:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Passos seguintes
- [Limites de pesquisa geográfica](specify-geographic-search.md)
- [Consulta e resposta](local-search-query-response.md)
- [Arranque rápido em C #](quickstarts/local-quickstart.md)