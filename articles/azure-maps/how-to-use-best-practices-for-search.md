---
title: Pesquisar com eficiência usando o Azure Maps Serviço de Pesquisa | Mapas do Microsoft Azure
description: Saiba como aplicar as melhores práticas para o serviço de pesquisa utilizando o Microsoft Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845749"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Práticas recomendadas para usar o Azure Maps Serviço de Pesquisa

[O Serviço de Pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Maps inclui APIs com várias capacidades. Por exemplo, a API de Pesquisa de Endereços é utilizada para pesquisar Ponto de Interesse (POI) ou dados em torno de uma localização específica. Este artigo demonstra práticas sólidas a aplicar ao ligar dados dos Serviços de Pesquisa do Azure Maps. Vai aprender a:

* Criar consultas para retornar correspondências relevantes
* Limitar os resultados da pesquisa
* Aprenda a diferença entre vários tipos de resultados
* Ler a estrutura de resposta de pesquisa de endereço


## <a name="prerequisites"></a>Pré-requisitos

Para fazer chamadas para as APIs de serviço maps, você precisa de uma conta Azure Maps e uma chave. Se precisar, siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) e obtenha uma chave [primária](quick-demo-map-app.md#get-the-primary-key-for-your-account). Para obter mais informações sobre autenticação no Azure Maps, consulte [gerenciar a autenticação no Azure Maps](./how-to-manage-authentication.md).

> [!Tip]
> Para consultar o serviço de pesquisa, você pode usar o [aplicativo de postmaster](https://www.getpostman.com/apps) para criar chamadas REST ou pode usar qualquer ambiente de desenvolvimento de API que preferir.


## <a name="best-practices-for-geocoding-address-search"></a>Práticas recomendadas para geocodificação (pesquisa de endereço)

Quando procura um endereço completo ou parcial utilizando o Serviço de Pesquisa do Azure Maps, a API lê palavras-chave da sua consulta de pesquisa e devolve as coordenadas de longitude e latitude do endereço. Esse processo é chamado de geocodificação. A capacidade de geocódigo em um país depende da cobertura de dados de estrada e da precisão de geocodificação do serviço de geocodificação.

Consulte [cobertura de geocodificação](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) para saber mais sobre os recursos de geocodificação do Azure Maps por país/região.

### <a name="limit-search-results"></a>Limitar os resultados da pesquisa

 A API de pesquisa do Azure Maps pode ajudá-lo a limitar os resultados da pesquisa de forma adequada, para que possa apresentar dados relevantes aos seus utilizadores.

   > [!Note]
   > Nem todos os parâmetros suportados pela pesquisa apis estão listados abaixo

   **Resultados da pesquisa de tendência geográfica**

   Para fazer a tendência geográfica dos resultados para a área relevante para o usuário, você sempre deve adicionar a entrada de local detalhada máxima possível. Para restringir os resultados da pesquisa, considere adicionar os seguintes tipos de entrada:

   1. Defina o parâmetro `countrySet`, por exemplo, "US, FR". O comportamento de pesquisa padrão é Pesquisar o mundo inteiro, potencialmente retornando resultados desnecessários. Se a sua consulta tiver o parâmetro `countrySet`, a pesquisa pode retornar resultados imprecisos. Por exemplo, a procura por uma cidade chamada **Bellevue** devolverá os resultados dos EUA e da França, uma vez que há cidades chamadas **Bellevue** no caldo da França e dos EUA.

   2. Pode utilizar os parâmetros `btmRight` e `topleft` para definir a caixa de delimitação, para restringir a procura a uma área específica no mapa.

   3. Para influenciar a área de relevância dos resultados, você pode definir os parâmetros de coordenação `lat`e `lon` e definir o raio da área de pesquisa usando o parâmetro `radius`.


   **Parâmetros de pesquisa difusa**
   
  A [API de pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) do Azure Maps é o serviço recomendado a ser usado quando você não sabe quais são suas entradas de usuário para uma consulta de pesquisa. A API combina a pesquisa de POI (ponto de interesse) e geocodificação em uma pesquisa canônica de *linha única*.

   1. O `minFuzzyLevel` e `maxFuzzyLevel` ajudam a retornar os jogos relevantes mesmo quando os parâmetros de consulta não correspondem exatamente às informações desejadas. Para obter desempenho e reduzir resultados incomuns, as consultas de pesquisa padrão para `minFuzzyLevel=1` e `maxFuzzyLevel=2` . Veja um exemplo de termo de pesquisa "restrant", que é correspondido a "restaurante" quando o `maxFuzzyLevel` é definido como 2. Os níveis de predefinição podem ser ultrapassados conforme necessário.  

   2. Você também pode priorizar o conjunto exato de tipos de resultados a serem retornados usando o parâmetro `idxSet`. Para essa finalidade, você pode enviar uma lista de índices separados por vírgulas; a ordem dos itens não importa. Há suporte para os seguintes índices:

       * `Addr` - **Address Ranges**: Para algumas ruas, há pontos de endereço, que são interpolados desde o início e o fim da rua. Estes pontos são representados como intervalos de endereços.
       * `Geo` - **geografia**: áreas em um mapa que representam a divisão administrativa de um terreno, ou seja, país, estado, cidade.
       * `PAD`**endereço de ponto**de  - : pontos em um mapa em que um endereço específico com um nome e número de rua pode ser encontrado em um índice, por exemplo, Soquel Dr 2501. Este valor idxSet é o mais alto nível de precisão disponível para endereços.  
       * `POI`**pontos de  - de interesse**: pontos em um mapa que vale a pena dar atenção e podem ser interessantes.  [Obter o endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) não retornará depois.  
       * `Str` - **Streets**: representação de ruas no mapa.
       * `XStr` - **entre ruas/interseções**: representação de junções; locais onde duas ruas se cruzam.


       **Exemplos de uso**:

       * idxSet = POI (somente pontos de pesquisa de interesse) 

       * idxSet = PAD, addr (somente endereços de pesquisa, PAD = endereço de ponto, addr = intervalo de endereços)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Reverter o filtro de tipo de entidade geográfica e geography

Ao fazer uma pesquisa de geocódigo inversa com [API Reverse Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)De busca, o serviço tem capacidade para devolver polígonos para as áreas administrativas. Ao fornecer o parâmetro `entityType` no pedido, pode reduzir a procura de tipos específicos de entidades de geografia. A resposta resultante conterá o ID de geografia e o tipo de entidade combinado. Se você fornecer mais de uma entidade, Endpoint retornará a **menor entidade disponível**. A ID geometry retornada pode ser usada para obter a geometria dessa geografia por meio do [serviço de polígono](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Exemplo de solicitação:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Responde**

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Idioma dos resultados da pesquisa

O parâmetro `language` permite selecionar o idioma dos resultados, devolvido pela API. Se o idioma não estiver definido no pedido, o serviço de pesquisa falha automaticamente na língua mais comum do país/região. Além disso, quando os dados no idioma especificado não estão disponíveis, o idioma padrão é usado. Consulte [as línguas apoiadas](https://docs.microsoft.com/azure/azure-maps/supported-languages) para obter uma lista de línguas apoiadas dos serviços Azure Maps por país/região.


### <a name="predictive-mode-autosuggest"></a>Modo preditivo (Autosuggest)

Para encontrar mais correspondências para consultas parciais, `typeahead` parâmetro deve ser definido como ' true '. A consulta será interpretada como uma entrada parcial e a pesquisa entrará no modo de previsão. Caso contrário, o serviço assumirá que todas as informações relevantes foram passadas.

Na consulta de amostra abaixo, pode ver que o serviço de endereço de pesquisa é consultado para "Microso" com o parâmetro `typeahead` definido para **verdade**. Se observar a resposta, pode ver que o serviço de pesquisa interpretou a consulta como uma consulta parcial. A resposta contém resultados para consulta auto-sugerida.

**Consulta de exemplo:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Responde**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>Codificação de URI para manipular caracteres especiais 

Para encontrar endereços de rua cruzada, deve codificar o URI para lidar com caracteres especiais no endereço. Considere este endereço exemplo: "1ª Avenida & Union Street, Seattle". O caráter especial '&' precisa de ser codificado antes de enviar o pedido. Recomendamos codificar dados de caracteres num URI, onde todos os caracteres são codificados usando um caráter '%' e um valor hexê-hex de dois caracteres correspondente ao seu carácter UTF-8.

**Exemplos de uso**:

Obter endereço de pesquisa:

```
query=1st Avenue & E 111th St, New York
```

 deve ser codificado como:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Aqui estão os diferentes métodos a serem usados para diferentes idiomas: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Rubi
```Ruby
CGI::escape(query) 
```

Swift
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Vá
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Práticas recomendadas para pesquisa do POI

A pesquisa de POI (pontos de interesse) permite solicitar resultados de POI por nome, por exemplo, Pesquisar negócios por nome. Recomendamos que você use o parâmetro `countrySet` para especificar os países em que seu aplicativo precisa de cobertura, pois o comportamento padrão será Pesquisar o mundo inteiro, potencialmente retornando resultados desnecessários e/ou resultará em tempos de pesquisa mais longos.

### <a name="brand-search"></a>Pesquisa de marca

Para melhorar a relevância dos resultados e as informações na resposta, a resposta de pesquisa de POI (ponto de interesse) inclui as informações de marca que podem ser usadas ainda mais para analisar a resposta.

Você também pode enviar uma lista separada por vírgulas de nomes de marca na solicitação. Você pode usar a lista para restringir os resultados a marcas específicas usando o parâmetro `brandSet`. A ordem dos itens não importa. Quando várias marcas são fornecidas, apenas os resultados que pertencem (pelo menos) a uma das listas fornecidas são devolvidos.

Vamos fazer uma solicitação de [pesquisa de categoria POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) para estações de gás próximas à Microsoft campus (Redmond, WA). Se você observar a resposta, poderá ver informações de marca para cada POI retornado.


**Consulta de exemplo:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Responde**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Pesquisa de aeroportos

A pesquisa do POI dá suporte à pesquisa de aeroportos usando os códigos oficiais do aeroporto. Por exemplo, **mar** (Aeroporto Internacional de Seattle-Tacoma). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Pesquisa próxima

Para recuperar apenas os resultados de POI em um local específico, a [API de pesquisa próxima](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) pode ser a escolha certa. Esse ponto de extremidade retornará apenas resultados de POI e não usará um parâmetro de consulta de pesquisa. Para limitar os resultados, é recomendável definir o raio.

## <a name="understanding-the-responses"></a>Noções básicas sobre as respostas

Vamos fazer uma solicitação de pesquisa de endereço para o [serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Maps para um endereço em Seattle. Se você olhar atentamente a URL de solicitação abaixo, definimos o parâmetro `countrySet` como **nós** para pesquisar o endereço na Estados Unidos da América.

**Consulta de exemplo:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Vamos dar uma olhada na estrutura de resposta abaixo. Os tipos de resultado dos objetos de resultado na resposta são diferentes. Se você observar com cuidado, pode ver que temos três tipos diferentes de objetos de resultado, que são "endereço de ponto", "Street" e "Cross Street". Observe que a pesquisa de endereço não retorna depois. O parâmetro `Score` para cada objeto de resposta indica a pontuação de correspondência relativa para pontuações de outros objetos na mesma resposta. Consulte [obter o endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) para saber mais sobre os parâmetros do objeto de resposta.

**Tipos de resultado com suporte:**

* **Endereço do ponto:** Pontos em um mapa com endereço específico com um nome e número de rua. O nível mais alto de precisão disponível para endereços. 

* **Intervalo de endereços:**  Para algumas ruas, há pontos de endereço que são interpolados do início e do fim da rua; esses pontos são representados como intervalos de endereços. 

* **Geografia:** Áreas em um mapa que representam a divisão administrativa de um terreno, ou seja, país, estado, cidade. 

* **POI-(pontos de interesse):** Pontos em um mapa que vale a pena dar atenção e podem ser interessantes.

* **Rua:** Representação de ruas no mapa. Os endereços são resolvidos para a coordenada de latitude/longitude da rua que contém o endereço. O número da casa não pode ser processado. 

* **Entre as ruas:** Interseções. Representações de junções; locais onde duas ruas se cruzam.

**Responde**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometry

Quando o tipo de resposta é **Geometry**, ele pode incluir a ID geometry retornada no objeto **DataSources** em "Geometry" e "ID". Por exemplo, o [serviço Get Polygon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) permite-lhe solicitar os dados de geometria no formato GeoJSON. Como um contorno de cidade ou aeroporto para um conjunto de entidades. Você pode usar esses dados de limite para o [isolamento geográfico](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) ou [Pesquisar por dentro da geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


As respostas de [endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ou de pesquisa de API [difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) podem incluir a **ID de geometria** retornada no objeto de fontes de origem em "Geometry" e "ID".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Passos seguintes

* Saiba [como criar solicitações de serviço de pesquisa do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Explore a documentação da [API do serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search)do Azure Maps. 
