---
title: Como pesquisar eficiente, utilizando o serviço de pesquisa de mapas do Azure | Documentos da Microsoft
description: Saiba como utilizar as melhores práticas para pesquisa com o serviço de pesquisa de mapas do Azure
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3a9c5ad92494dd82500c4faee82c119e99346c7a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288160"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Melhores práticas para utilizar o serviço de pesquisa de mapas do Azure

O Azure Maps [serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search) inclui APIs com vários recursos, por exemplo, a partir da pesquisa de endereço para pesquisar dados do ponto de interesse (POI) em torno de uma localização específica. Neste artigo, iremos irá partilhar as melhores práticas para chamar a dados por meio de serviços de pesquisa de mapas do Azure. Vai aprender a:

* Criar consultas devolver correspondências relevantes
* Resultados da pesquisa de limite
* Saiba a diferença entre vários tipos de resultado
* Ler a estrutura de resposta de pesquisa de endereço


## <a name="prerequisites"></a>Pré-requisitos

Para fazer todas as chamadas para as APIs do serviço de mapas, terá de uma conta de mapas e a chave. Para obter informações sobre como criar uma conta e obter uma chave, consulte [como gerir a sua conta do Azure Maps e as chaves](how-to-manage-account-keys.md).

> [!Tip]
> Para consultar o serviço de pesquisa, pode utilizar o [aplicação Postman](https://www.getpostman.com/apps) para criar o REST chamadas ou pode usar qualquer ambiente de desenvolvimento de API que preferir.


## <a name="best-practices-for-geocoding"></a>Melhores práticas para a codificação geográfica

Quando procurar um endereço de total ou parcial para o serviço de pesquisa de mapas do Azure, ele usa o termo de pesquisa e retorna as coordenadas de longitude e latitude do endereço. Este processo é denominado geocodificação. A capacidade de geocode num país é dependente da cobertura de dados de estrada e a precisão da geocodificação do serviço de geocodificação.

Ver [cobertura de geocodificação](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) para saber mais sobre as capacidades de codificação geográfica do Azure Maps por país/região.

### <a name="limit-search-results"></a>Resultados da pesquisa de limite

   Nesta secção, irá aprender a utilizar APIs de pesquisa de mapas do Azure para limitar os resultados da pesquisa. 

   > [!Note]
   > Pesquisa de nem todas as APIs suportam totalmente a parâmetros listados abaixo

   **Resultados da pesquisa de tendência de geo**

   Por ordem para geo-tendência os resultados para a área relevante para o utilizador, deve sempre adicionar o número máximo possível detalhado entrada de localização. Para restringir os resultados da pesquisa, considere adicionar os seguintes tipos de entrada:

   1. Definir o `countrySet` parâmetro, por exemplo "US, FR". O comportamento de pesquisa padrão é pesquisar todo o mundo, potencialmente, retornando resultados desnecessários. Se a sua consulta não incluir `countrySet` parâmetro, a pesquisa pode devolver resultados imprecisos. Por exemplo, procure uma com o nome de cidade **Bellevue** irá devolver resultados dos EUA e em França, uma vez que existem com o nome de cidades **Bellevue** em França e nos EUA.

   2. Pode utilizar o `btmRight` e `topleft` parâmetros para definir a delimitação de caixa para restringir a pesquisa a uma determinada área do mapa.

   3. Para influenciar a área de relevância para os resultados, pode definir o `lat`e `lon` coordenar parâmetros e defina o radius da área de pesquisa com o `radius` parâmetro.


   **Parâmetros de pesquisa difusa**

   1. O `minFuzzyLevel` e `maxFuzzyLevel`, ajudar a devolver correspondências relevantes, mesmo quando os parâmetros de consulta não exatamente correspondem para as informações desejadas. A maioria das consultas de pesquisa como padrão `minFuzzyLevel=1` e `maxFuzzyLevel=2` para obter o desempenho e reduzir os resultados invulgares. Dar um exemplo de um termo de pesquisa "restrant", ele é correspondido a "restaurante" quando o `maxFuzzyLevel` é definida como 2. Os níveis de difusa padrão podem ser substituídos de acordo com as necessidades de pedido. 

   2. Também pode especificar o conjunto exato de tipos de resultados a serem retornados ao utilizar o `idxSet` parâmetro. Para este fim, pode enviar a lista separada por vírgulas de índices, a ordem de item não é relevante. Seguem-se os índices suportados:

       * `Addr` - **Intervalos de endereços**: Para alguns ruas, existem pontos de endereço que são interpolados a partir de início e no fim da rua; esses pontos são representados como intervalos de endereços.
       * `Geo` - **As geografias**: Áreas num mapa que representam a divisão administrativa de um ' s land, ou seja, país, estado, cidade.
       * `PAD` - **Endereço de ponto**:  Pontos num mapa onde endereço específico com um nome da rua e o número pode ser encontrado num índice, por exemplo, Soquel 2501 de Dr. É o maior nível de precisão disponível para endereços.  
       * `POI` - **Pontos de interesse**: Pontos num mapa que vale a pena atenção e podem ser interessante.  [Obter o endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) não retornará POIs.  
       * `Str` - **Streets**: Representação de ruas no mapa.
       * `XStr` - **Entre as ruas/interseções**:  Representação de junções; coloca em que dois streets intersect.


       **Exemplos de utilização**:

       * idxSet = POI (apenas procurar pontos de interesse) 

       * idxSet = PAD Ender (apenas, de pesquisa endereços quadro = o endereço de ponto, Ender = intervalo de endereços)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Inverter geocode e geografia filtro do tipo de entidade

Ao realizar uma pesquisa inversa geocode com [API de pesquisa inversa de endereço](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), o serviço tem capacidade para devolver os polígonos para as áreas administrativas. Ao fornecer o parâmetro `entityType` na solicitação, pode restringir a pesquisa para tipos de entidade de geografia especificado. A resposta resultante irá conter o ID de geografia, bem como o tipo de entidade correspondidas. Se fornecer mais de uma entidade, o ponto final irá devolver a **entidade menor disponível**. Devolveu o ID de geometria pode ser utilizado para obter a geometria nessa área geográfica através de [serviço de polígono obter](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Pedido de exemplo:**

```HTTP
https://atlas.microsoft.com/search/address/json?api-version=1.0&subscription-key={subscription-key}&query=MicrosoftWay&entityType=Municipality
```

**Resposta:**

```JSON
{
    "summary": {
        "queryTime": 8,
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
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
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

### <a name="search-results-language"></a>Idioma de resultados de pesquisa

O `language` parâmetro permite-lhe definir em qual pesquisa de idioma devem ser retornados resultados. Se o idioma não está definido no pedido, serviço de pesquisa automaticamente por predefinição, a linguagem mais comum no país/região. Além disso, quando os dados no idioma especificado não estão disponíveis, é utilizado o idioma predefinido. Ver [idiomas suportados](https://docs.microsoft.com/azure/azure-maps/supported-languages) para obter uma lista dos idiomas com suporte em relação aos serviços do Azure Maps por país/região.


### <a name="predictive-mode-auto-suggest"></a>Modo preditivo (Sugestão automática)

Para localizar mais correspondências para consultas parciais, `typeHead` parâmetro deve ser definido como 'true'. A consulta será interpretada como uma entrada parcial e a pesquisa entrará no modo de previsão. Caso contrário, o serviço supor que foi transmitidas em todas as informações relevantes.

No exemplo de consulta abaixo, pode ver que o serviço de endereço de pesquisa é consultada para "Microsoft" com o `typehead` parâmetro definido como **true**. Se observar a resposta, pode ver que o serviço de pesquisa interpretado a consulta como parcial consulta e resposta contém resultados de consulta de sugeridas automática.

**Consulta de exemplo:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Resposta:**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>URI de codificação para lidar com carateres especiais 

Para localizar a rua em vários endereços, ou seja, Avenida de dia 1 & União rua, Seattle, caráter especial ' &' tem de ser codificado antes de enviar o pedido. Recomendamos que a codificação de dados de caracteres num URI, onde todos os caracteres são codificados usando um caractere de "%" e um valor hexadecimal de dois caracteres correspondentes aos seus caracteres UTF-8.

**Exemplos de utilização**:

Obter o endereço de pesquisa:

```
query=1st Avenue & E 111th St, New York shall be encoded as query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York 
```


Aqui estão os diferentes métodos para utilizar para diferentes idiomas: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```C#
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

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Aceda a:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Melhores práticas para pesquisa POI

Pontos de interesse (POI) pesquisa permite-lhe pedir POI resultados por nome, por exemplo, os negócios de pesquisa por nome. É altamente recomendável que use o `countrySet` parâmetro para especificar os países que seu aplicativo precisa cobertura, como o comportamento padrão será a pesquisar todo o mundo, potencialmente, retornando resultados desnecessários e/ou resultar em períodos de pesquisa.

### <a name="brand-search"></a>Pesquisa de marca

Para melhorar a relevância dos resultados e as informações na resposta, a resposta de pesquisa de ponto de interesse (POI) inclui as informações de marca que podem ser utilizadas para analisar a resposta ainda mais.

Vamos criar um [POI categoria pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) pedido de postos de combustível perto campus da Microsoft (Redmond, WA). Se observar a resposta, pode ver informações de marca para cada POI devolvido.

**Consulta de exemplo:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Resposta:**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
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
                "url": "www.chevron.com",
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
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/7728133",
            "score": 5.663,
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
                "url": "www.76.com/",
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
                "streetName": "148th Ave NE",
                "municipalitySubdivision": "Redmond, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148th Ave NE, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
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
                "url": "www.texaco.com/",
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
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Pesquisa do aeroporto

Pesquisa POI suporta pesquisa aeroportos utilizando os códigos de aeroporto oficiais. Por exemplo, **mar** (aeroporto internacional de Seattle-Tacoma). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Pesquisa próximas

Para obter apenas os resultados POI em torno de uma localização específica, o [nas proximidades de API de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) pode ser a escolha certa. Este ponto final só irá devolver resultados POI e não assume um parâmetro de consulta de pesquisa. Para limitar os resultados, é recomendado para definir o radius.

## <a name="understanding-the-responses"></a>Noções básicas sobre as respostas

Vamos fazer uma solicitação de pesquisa de endereço para o Azure Maps [serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search) para um endereço em Seattle. Se examinar cuidadosamente o URL do pedido abaixo, poderíamos ter definido o `countrySet` parâmetro **E.U.A.** para procurar o endereço dos Estados Unidos da América.

**Consulta de exemplo:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400BroadSt,Seattle,WA&countrySet=US
```

Ainda mais Vamos dar uma olhada a estrutura de resposta abaixo. Os tipos de resultado de objetos de resultado na resposta são diferentes. Se observar com cuidado que pode ver que temos três tipos diferentes de objetos de resultados, que são endereços de ponto, rua e entre rua. Tenha em atenção que a pesquisa endereço não devolve POIs. O `Score` parâmetro para cada objeto de resposta indica a classificação correspondente relativa pontuações de outros objetos na resposta da mesma. Ver [obter o endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) para saber mais sobre os parâmetros do objeto de resposta.

**Tipos suportados do resultado:**

**Endereço de ponto:** Pontos num mapa com um endereço específico com um nome da rua e um número. O maior nível de precisão disponível para endereços. 

**Intervalo de endereços:**  Para alguns ruas, existem pontos de endereço que são interpolados a partir de início e no fim da rua; esses pontos são representados como intervalos de endereços. 

**Geografia:** Áreas num mapa que representam a divisão administrativa de um ' s land, ou seja, país, estado, cidade. 

**POI - (pontos de interesse):** Pontos num mapa que vale a pena atenção e podem ser interessante.

**Rua:** Representação de ruas no mapa. Endereços são resolvidos para as coordenadas de latitude/longitude da rua que contém o endereço. O número de casa não pode ser processado. 

**Rua de entre:** Interseções. Representações de junções; coloca em que dois streets intersect.

**Resposta:**

```JSON
{
    "summary": {
        "query": "400 broad st seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometry

O tipo de resposta for **Geometry**, pode incluir o ID de geometria que é devolvido na **origens de dados** objeto em "geometria" e "id". Por exemplo, [serviço de polígono obter](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) permite-lhe pedir os dados de geometria no formato GeoJSON, por exemplo, um contorno de cidade ou aeroporto para um conjunto de entidades. Pode usar esses dados de limites para [barreira geográfica](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) ou [pesquisa POIs dentro da geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Procurar endereço](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ou [pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) respostas de API podem incluir a **ID de geometria** que é retornado no objeto de origens de dados em "geometria" e "id".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais [como criar pedidos de serviço de pesquisa de mapas do Azure](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Explorar o Azure Maps [documentação de API do serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search). 