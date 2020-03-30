---
title: Boas práticas para o Serviço de Pesquisa do Azure Maps Microsoft Azure Maps
description: Saiba como aplicar as melhores práticas ao utilizar o Serviço de Pesquisa a partir do Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335311"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Boas práticas para o Serviço de Pesquisa de Mapas Azure

[O Serviço](https://docs.microsoft.com/rest/api/maps/search) de Pesquisa do Azure Maps inclui APIs que oferecem várias capacidades. Por exemplo, a API do Endereço de Pesquisa pode encontrar pontos de interesse (POI) ou dados em torno de uma localização específica. 

Este artigo explica como aplicar práticas de som quando liga dados do Serviço de Pesquisa do Azure Maps. Vai aprender a:

* Construa consultas para devolver fósforos relevantes.
* Limite os resultados da pesquisa.
* Aprenda as diferenças entre os tipos de resultados.
* Leia a estrutura de resposta à procura de endereços.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer chamadas para o serviço Azure Maps APIs, você precisa de uma conta Azure Maps e uma chave. Para mais informações, consulte [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) e obter uma chave [primária](quick-demo-map-app.md#get-the-primary-key-for-your-account). 

Para obter informações sobre a autenticação no Azure Maps, consulte [Gerir a autenticação no Azure Maps](./how-to-manage-authentication.md).

> [!TIP]
> Para consultar o Serviço de Pesquisa, pode utilizar a [aplicação Postman](https://www.getpostman.com/apps) para construir chamadas REST. Ou pode usar qualquer ambiente de desenvolvimento da API que prefira.

## <a name="best-practices-to-geocode-addresses"></a>Boas práticas para endereços de geocódigo

Quando procura um endereço completo ou parcial utilizando o Serviço de Pesquisa do Azure Maps, a API lê palavras-chave da sua consulta de pesquisa. Em seguida, devolve as coordenadas de longitude e latitude do endereço. Este processo chama-se *geocodificação.* 

A capacidade de geocodificação num país depende da disponibilidade de dados rodoviários e da precisão do serviço de geocodificação. Para obter mais informações sobre as capacidades de geocodificação do Azure Maps por país ou região, consulte a [cobertura da Geocoding](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Limitar os resultados da pesquisa

 A API de pesquisa de Mapas Azure pode ajudá-lo a limitar os resultados da pesquisa adequadamente. Limita os resultados para que possa apresentar dados relevantes aos seus utilizadores.

> [!NOTE]
> As APIs de pesquisa suportam mais parâmetros do que apenas aqueles que este artigo discute.

#### <a name="geobiased-search-results"></a>Resultados de pesquisa geobiased

Para obter resultados geobias para a área relevante para o seu utilizador, adicione sempre o maior número possível de detalhes de localização. É melhor restringir os resultados da pesquisa especificando alguns tipos de entrada:

* Defina `countrySet` o parâmetro. Pode `US,FR`defini-lo, por exemplo. Por padrão, a API procura em todo o mundo, para que possa devolver resultados desnecessários. Se a sua `countrySet` consulta não tiver parâmetro, então a pesquisa pode devolver resultados imprecisos. Por exemplo, uma busca por uma cidade chamada *Bellevue* devolve resultados dos EUA e da França porque ambos os países contêm uma cidade chamada *Bellevue.*

* Pode utilizar `btmRight` os `topleft` e parâmetros para definir a caixa de delimitação. Estes parâmetros restringem a procura a uma área específica no mapa.

* Para influenciar a área de relevância `lat` para `lon` os resultados, defina os parâmetros e coordenar. Utilize `radius` o parâmetro para definir o raio da área de busca.


#### <a name="fuzzy-search-parameters"></a>Parâmetros de pesquisa fuzzy

Recomendamos que utilize a [API De pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) de mapas azure quando não conhece as suas inputs de utilizador para uma consulta de pesquisa. A API combina pesquisa de POI e geocodificação numa pesquisa canónica *de linha única:* 

* Os `minFuzzyLevel` `maxFuzzyLevel` parâmetros e parâmetros ajudam a devolver os jogos relevantes mesmo quando os parâmetros de consulta não correspondem exatamente à informação que o utilizador quer. Para maximizar o desempenho e reduzir resultados incomuns, detete as consultas de pesquisa para os incumprimentos de `minFuzzyLevel=1` e `maxFuzzyLevel=2`. 

    Por exemplo, `maxFuzzyLevel` quando o parâmetro está definido para 2, o *termo* de pesquisa é combinado com o *restaurante*. Pode anular os níveis de predefinição quando necessário. 

* Utilize `idxSet` o parâmetro para priorizar o conjunto exato de tipos de resultados. Para priorizar um conjunto exato de resultados, pode submeter uma lista separada de índices. Na sua lista, a ordem de artigonão importa. O Azure Maps suporta os seguintes índices:

* `Addr` - **Intervalos**de endereços : Pontos de endereço que são interpolados desde o início e o fim da rua. Estes pontos são representados como intervalos de endereços.
* `Geo` - **Geografias**: Divisões administrativas de terra. Uma geografia pode ser um país, estado ou cidade, por exemplo.
* `PAD` - **Endereços de ponto**: Endereços que incluem nome de rua e número. Os endereços de ponto podem ser encontrados num índice. Um exemplo é *Soquel Dr 2501*. Um endereço de ponto fornece o mais alto nível de precisão disponível para endereços.  
* `POI` - **Pontos de interesse**: Pontos num mapa que são considerados dignos de atenção ou que podem ser interessantes. O [Endereço de Busca API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) não devolve POIs.  
* `Str` - **Ruas**: Ruas no mapa.
* `XStr` - Cruzamento de **ruas ou cruzamentos**: Entroncamentos ou locais onde duas ruas se cruzam.


#### <a name="usage-examples"></a>Exemplos de utilização

* `idxSet=POI`- Pesquisa só pois. 

* `idxSet=PAD,Addr`- Apenas endereços de pesquisa. `PAD`indica o endereço `Addr` de ponto e indica o intervalo de endereço.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Geocódigo inverso e filtro para um tipo de entidade de geografia

Quando então faz uma pesquisa de geocódigo inverso na [API inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)do endereço de busca, o serviço pode devolver polígonos para áreas administrativas.Para reduzir a procura a tipos específicos de entidades de geografia, inclua o `entityType` parâmetro nos seus pedidos. 

A resposta resultante contém o ID de geografia e o tipo de entidade que foi combinado. Se fornecer mais do que uma entidade, então o ponto final devolve a *entidade mais pequena disponível.* Você pode usar o ID de geometria devolvido para obter a geometria da geografia através do [serviço Search Polygon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

#### <a name="sample-request"></a>Pedido de amostra

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Resposta

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

### <a name="set-the-results-language"></a>Definir o idioma dos resultados

Utilize `language` o parâmetro para definir o idioma para os resultados de pesquisa devolvidos. Se o pedido não definir o idioma, então por padrão o Serviço de Pesquisa utiliza a língua mais comum no país ou região. Quando não existem dados disponíveis no idioma especificado, a linguagem predefinida é utilizada. 

Para mais informações, consulte [o Azure Maps idiomas suportados.](https://docs.microsoft.com/azure/azure-maps/supported-languages)


### <a name="use-predictive-mode-automatic-suggestions"></a>Utilize o modo preditivo (sugestões automáticas)

Para encontrar mais correspondências para `typeahead` consultas parciais, defina o parâmetro para `true`. Esta consulta é interpretada como uma entrada parcial, e a pesquisa entra no modo preditivo. Se não definir o `typeahead` parâmetro `true`para, então o serviço assume que todas as informações relevantes foram transmitidas.

Na seguinte consulta de amostra, o serviço 'Endereço de Busca' é consultado para *a Microso*. Aqui, `typeahead` o parâmetro `true`definido para . A resposta mostra que o serviço de pesquisa interpretou a consulta como uma consulta parcial. A resposta contém resultados para uma consulta automaticamente sugerida.

#### <a name="sample-query"></a>Consulta de exemplo

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Resposta

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


### <a name="encode-a-uri-to-handle-special-characters"></a>Codificar um URI para lidar com caracteres especiais 

Para encontrar endereços de rua cruzada, deve codificar o URI para lidar com caracteres especiais no endereço. Considere este endereço exemplo: *1ª Avenida & Union Street, Seattle*. Aqui, incidifique o caráter`&`ampersand () antes de enviar o pedido. 

Recomendamos que incida os dados de caracteres num URI. Num URI, codifica-se todos os caracteres`%`utilizando um sinal percentual e um valor hexadecimal de dois caracteres que corresponde ao código UTF-8 dos personagens.

#### <a name="usage-examples"></a>Exemplos de utilização

Comece com este endereço:

```
query=1st Avenue & E 111th St, New York
```

Codificar o endereço:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Pode utilizar os seguintes métodos.

JavaScript ou TypeScript:
```Javascript
encodeURIComponent(query)
```

C# ou Visual Basic:
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

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Vai:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Boas práticas para pesquisa de POI

Numa pesquisa de POI, pode solicitar resultados de POI pelo nome. Por exemplo, pode procurar um negócio pelo nome. 

Recomendamos vivamente que `countrySet` utilize o parâmetro para especificar países onde a sua aplicação necessita de cobertura. O comportamento padrão é procurar em todo o mundo. Esta ampla pesquisa pode devolver resultados desnecessários, e a pesquisa pode demorar muito tempo.

### <a name="brand-search"></a>Pesquisa de marcas

Para melhorar a relevância dos resultados e a informação na resposta, uma resposta de pesquisa poi inclui informações da marca. Pode utilizar esta informação para analisar ainda mais a resposta.

Num pedido, pode submeter uma lista separada de marcas separadas. Utilize a lista para restringir os resultados a marcas específicas, definindo o `brandSet` parâmetro. Na sua lista, a ordem de item não importa. Quando fornece várias listas de marcas, os resultados que são devolvidos devem pertencer a pelo menos uma das suas listas.

Para explorar a procura de marcas, vamos fazer um pedido de pesquisa de [categoria POI.](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) No exemplo seguinte, procuramos postos de gasolina perto do campus da Microsoft em Redmond, Washington. A resposta mostra informações da marca para cada POI que foi devolvido.

#### <a name="sample-query"></a>Consulta de exemplo

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Resposta

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


### <a name="airport-search"></a>Pesquisa no aeroporto

Ao utilizar a API PoI de Pesquisa, pode procurar aeroportos utilizando o seu código oficial. Por exemplo, você pode usar *o SEA* para encontrar o Aeroporto Internacional de Seattle-Tacoma: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Pesquisa nas proximidades

Para recuperar os resultados do POI em torno de um local específico, pode tentar utilizar a [API de pesquisa nas proximidades](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby). O ponto final devolve apenas resultados de POI. Não aceita um parâmetro de consulta de pesquisa. 

Para limitar os resultados, recomendamos que estabeleça o raio.

## <a name="understanding-the-responses"></a>Compreender as respostas

Vamos encontrar um endereço em Seattle fazendo um pedido de pesquisa de endereços para o Serviço de Pesquisa do Azure Maps. No seguinte pedido URL, `countrySet` definimos `US` o parâmetro para procurar o endereço nos EUA.

### <a name="sample-query"></a>Consulta de exemplo

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Tipos de resultados suportados

* **Endereço do ponto**: Pontos num mapa que tem um endereço específico com um nome de rua e número. O Endereço de Ponto fornece o mais alto nível de precisão para os endereços. 

* **Endereço :** O leque de pontos de endereço que são interpolados desde o início e o fim da rua.  

* **Geografia**: Áreas num mapa que representam divisões administrativas de uma terra, por exemplo, país, estado ou cidade. 

* **POI**: Pontos num mapa que merecem atenção e que pode ser interessante.

* **Rua**: Ruas no mapa. Os endereços são resolvidos para as coordenadas de latitude e longitude da rua que contém o endereço. O número da casa pode não ser processado. 

* **Cross Street:** Cruzamentos. Ruas cruzadas representam cruzamentos onde duas ruas se cruzam.

### <a name="response"></a>Resposta

Vamos ver a estrutura de resposta. Na resposta que se segue, os tipos dos objetos resultantes são diferentes. Se olhar com atenção, vê três tipos de objetos de resultado:

* Endereço de ponto
* Rua
* Rua Cross

Note que a pesquisa de endereços não devolve os POIs.  

O `Score` parâmetro para cada objeto de resposta indica como a pontuação correspondente se relaciona com as pontuações de outros objetos na mesma resposta. Para obter mais informações sobre os parâmetros do objeto de resposta, consulte [Obter Endereço de Busca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).

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

### <a name="geometry"></a>Geometria

Um tipo de resposta de *geometria* pode incluir `dataSources` o `geometry` ID `id`de geometria que é devolvido no objeto sob e . Por exemplo, pode utilizar o [serviço Search Polygon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) para solicitar os dados de geometria num formato GeoJSON. Ao utilizar este formato, pode obter um contorno de cidade ou aeroporto para um conjunto de entidades. Em seguida, pode utilizar estes dados de fronteira para [configurar uma geovete](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) ou pesquisa de [POIs dentro da geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


As respostas para a API do [Endereço de Busca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ou a API de Pesquisa [Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) podem incluir o ID de geometria que é devolvido no `dataSources` objeto abaixo `geometry` e `id`:


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte:

> [!div class="nextstepaction"]
> [Como construir pedidos do Serviço de Pesquisa do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Documentação da API do Serviço de Pesquisa](https://docs.microsoft.com/rest/api/maps/search)