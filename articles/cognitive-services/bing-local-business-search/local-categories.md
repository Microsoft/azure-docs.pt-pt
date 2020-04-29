---
title: Categorias de pesquisa para a API local de pesquisa de negócios bing
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para saber como especificar categorias de pesquisa para o ponto final de pesquisa de Negócios Locais Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 56b94d66eb0929d2fd0ca74a1a631d229330adfa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "69906402"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Categorias de pesquisa para a API local de pesquisa de negócios bing

A API de Pesquisa de Negócios Local Bing permite-lhe pesquisar entidades empresariais locais em várias categorias, com prioridade dada aos resultados que fecham a localização de um utilizador. Pode incluir estas pesquisas em pesquisas `localCircularView` juntamente com os e `localMapView` [parâmetros](specify-geographic-search.md).


## <a name="toplevel-categories"></a>Categorias de Nível Superior 

Os seguintes tipos definem as principais categorias de pesquisa.  Mais de uma categoria pode ser especificada utilizando uma lista `localCategories` delimitada de vírvia atribuída ao parâmetro.  
- EatDrink 
- Seedo 
- Loja 
- Hotéis AndMotels 
- BancosAndCreditUnions 
- Estacionamento 
- Hospitais 

## <a name="sub-categories"></a>Subcategorias
As subcategorias são aprovadas da mesma forma que `localCategories`. As subcategorias são categorias mais específicas. São subordinados no sentido em que, se especificar uma categoria C e uma das suas subcategorias S na mesma lista de comma delimitada, receberá os mesmos resultados que se especificasse apenas C.

### <a name="eat-drink"></a>Beber 
|  |  |  |  |
| - | - | - | - |
| CervejariasAndBrewPubs | CocktailLounges | Restaurantes Africanos |
| Restaurantes Americanos | Bagels | BarbecueRestaurants |
| Tabernas | SportsBars | Bares |
| BarsGrillsAndPubs | BuffetRestaurantes| Restaurantes belgas | 
| Restaurantes Britânicos | CaféRestaurantes | Restaurantes caribenhos |
| Restaurantes chineses | Café Andtea | Delicatessens | 
| Serviço de Entrega | Comensais | Lojas discount | 
| Donuts | FastFood | Restaurantes franceses | 
| Iogurte Congelado | Restaurantes Alemães | Supermercados | 
| Restaurantes gregos | Mercearias | Restaurantes havaianos | 
| Restaurantes húngaros | IceCream AndFrozenDesserts | Restaurantes Indianos | 
| Restaurantes italianos | Restaurantes japoneses | Sumos | 
| Restaurantes coreanos | Lojas de Bebidas | Restaurantes Mexicanos |
| Restaurantes do Médio Oriente | Pizza | Restaurantes Polacos | 
| PortuguesesRestaurantes | Pretzels | Restaurantes | 
| Restaurantes russos e ucranianos | Sanduíches | Restaurantes Seafood | 
| Restaurantes espanhóis | Restaurantes SteakHouse | SushiRestaurants | 
| Takeaway | Restaurantes Tailandeses | Restaurantes turcos | 
| Restaurantes Vegetarianos e Vegan | Restaurantes vietnamitas|  |
 
### <a name="see-do"></a>Ver Fazer 
|  |  |  |
| -- | -- | -- |
| Parques de Diversões | Atrações | Carnavalescos |
| Casinos | Marcos ESítios Históricos | MiniaturaGolfCourses |
| Cinemas | Museus | Parques |
| Passeios turísticos | Informação turística | Zoológicos |
 
### <a name="shop"></a>Loja 
|  |  |  |
| -- | -- | -- |
| Lojas De Antiguidades | Livrarias | Lojas CDAnd Record |
| Lojas ChildrensClothing | Tabaqueiras | ComicBookStores |
| Lojas de Departamentos | Lojas discount | FleaMarketsAndBazaars |
| Lojas de Móveis | HomeImprovementStores | Joalharia e Relógios |
| KitchenwareStores | Lojas de Bebidas | Centros ComerciaisAndShoppingCenters |
| MensClothingStores | Lojas de Música | Lojas Outlet |
| PetShops | PetSupplyStores | Lojas SchoolAndOfficeSupply |
| Lojas de Sapatos | Lojas SportingGoods | ToyAndGameStores |
| VitaminandSupplementstores | Lojas WomensClothing |  |


## <a name="examples-of-local-categories-search"></a>Exemplos de pesquisa de Categorias Locais

Os seguintes exemplos OBTÊM `localCategories` resultados de acordo com o parâmetro:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

A seguinte consulta limita o número de resultados 'hospitalares' aos três primeiros devolvidos da API local de pesquisa de negócios bing:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

O exemplo seguinte da resposta da JSON inclui três hospitais na área de Seattle:

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
- [Quickstart em C #](quickstarts/local-quickstart.md)
