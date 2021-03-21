---
title: Solicite dados de trânsito com os serviços de mobilidade do Microsoft Azure Maps (Pré-visualização)
description: Saiba como utilizar os serviços de Mobilidade Azure Maps (Preview) para solicitar dados de trânsito público, tais como IDs da área metropolitana, paragens de trânsito, rotas e itinerários de rota.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 740080d742f535f868b2ae194b24bebe5ac6ac24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906034"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>Solicitar dados de trânsito público utilizando os serviços de Mobilidade Azure Maps (Pré-visualização) 

> [!IMPORTANT]
> Os serviços de mobilidade Azure Maps estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este artigo mostra-lhe como utilizar [os serviços de Mobilidade](/rest/api/maps/mobility) Azure Maps para solicitar dados de trânsito público. Os dados de trânsito incluem paragens de trânsito, informações de rotas e estimativas do tempo de viagem.

Neste artigo você vai aprender, como:

* Obtenha um ID de área do metrô usando a [API da Área do Metrô](/rest/api/maps/mobility/getmetroareapreview)
* Solicite paragens de trânsito nas proximidades usando o serviço [Get Nearby Transit.](/rest/api/maps/mobility/getnearbytransitpreview)
* Consulta [Obter Rotas de Trânsito API](/rest/api/maps/mobility/gettransitroutepreview) para planear uma rota usando o trânsito público.
* Solicite geometria da rota de trânsito e horário detalhado para a rota utilizando a [API do Itinerário de Trânsito.](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)

## <a name="prerequisites"></a>Pré-requisitos

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](./how-to-manage-authentication.md)

Este tutorial usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="get-a-metro-area-id"></a>Obtenha uma iD da área do metrô

Para solicitar informações detalhadas sobre agências de trânsito e tipos de trânsito apoiados para uma determinada área metropolitana, você precisará `metroId` dessa área. A [API get metro area](/rest/api/maps/mobility/getmetroareapreview) permite-lhe solicitar áreas de metrô, nas quais estão disponíveis os serviços de Mobilidade Azure Maps. A resposta inclui detalhes como o `metroId` `metroName` , e a representação da geometria da área metropolitana no formato GeoJSON.

Vamos fazer um pedido para obter a área do metro para a Seattle-Tacoma identificação da área metropolitana. Para solicitar a iD para uma área de metrô, complete os seguintes passos:

1. Abra a aplicação Do Carteiro e vamos criar uma coleção para armazenar os pedidos. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.**

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção que criou no passo anterior como o local para guardar o pedido. Em seguida, **selecione Save**.
  
    ![Criar um pedido no Carteiro](./media/how-to-request-transit-data/postman-new.png)

3. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL para criar um pedido GET. `{subscription-key}`Substitua- se por a sua tecla primária Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. Após um pedido bem sucedido, receberá a seguinte resposta:

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="request-nearby-transit-stops"></a>Pedido de paragens de trânsito nas proximidades

O serviço Azure Maps [Get Nearby Transit](/rest/api/maps/mobility/getnearbytransitpreview) permite-lhe pesquisar objetos de trânsito. A API devolve os detalhes do objeto de trânsito, como paragens de trânsito público e bicicletas partilhadas em torno de um determinado local. Em seguida, faremos um pedido ao serviço para procurar paragens de trânsito público nas proximidades num raio de 300 metros em torno do local.

Para fazer um pedido ao [Trânsito De proximidade,](/rest/api/maps/mobility/getnearbytransitpreview)siga os passos abaixo:

1. No Carteiro, clique em **Novo Pedido**  |  **GET e** nomeie-o Obter **paragens próximas**.

2. No separador Construtor, selecione o método **GET** HTTP, introduza o seguinte URL de pedido para o seu ponto final da API e clique em **Enviar**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. Após um pedido bem sucedido, a estrutura de resposta deve parecer a seguinte:

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    } 
    ```

Se observar cuidadosamente a estrutura de resposta, verá que contém parâmetros para cada objeto de trânsito. Cada objeto de trânsito tem parâmetros `id` como, `type` , , , , e a `stopName` `mainTransitType` `mainAgencyName` posição, nas coordenadas, do objeto.

Para aprender, usaremos uma `id` das paragens de autocarro como origem, para a nossa rota na próxima secção.  

## <a name="request-a-transit-route"></a>Solicite uma rota de trânsito

O Azure Maps [Get Transit Routes API](/rest/api/maps/mobility/gettransitroutepreview) permite o planeamento de viagens. Devolve as melhores opções de rota possível de origem para um destino. O serviço fornece diferentes tipos de modos de viagem, incluindo passeios, ciclismo e trânsito público. Em seguida, vamos procurar uma rota desde a paragem de autocarro mais próxima até à torre Space Needle em Seattle.

### <a name="get-location-coordinates-for-destination"></a>Obtenha coordenadas de localização para o destino

Para obter as coordenadas de localização da torre space needle, usaremos o serviço de [pesquisa fuzzy Azure](/rest/api/maps/search/getsearchfuzzy)Maps.

Para fazer um pedido ao serviço de pesquisa Fuzzy, siga os passos abaixo:

1. No Carteiro, clique em **Novo**  |  **Pedido GET** e nomeie-o Obtenha as **coordenadas de localização**.

2. No separador Construtor, selecione o método **GET** HTTP, introduza o seguinte URL de pedido e clique em **Enviar**.

    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```

3. Se olhar cuidadosamente para a resposta, contém vários locais nos resultados da pesquisa da Agulha Espacial. Cada resultado contém as coordenadas de localização sob a **posição**. Copie o `lat` e `lon` sob a **posição** do primeiro resultado.

   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
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
            ...,
            ...,
            ...
        ]
    }
    ```

### <a name="request-route"></a>Rota de pedido

Para fazer um pedido de rota, complete os passos abaixo:

1. No Carteiro, clique em **Novo Pedido**  |  **GET e** nomeie-o Obter **informações sobre Rota**.

2. No separador Construtor, selecione o método **GET** HTTP, introduza o seguinte URL de pedido para o seu ponto final da API e clique em **Enviar**.

    Vamos solicitar rotas de trânsito público para um autocarro especificando os `modeType` parâmetros e `transitType` parâmetros. O URL de pedido contém as localizações recuperadas nas secções anteriores. Para o `originType` , temos agora um **stopId**. E para `destionationType` o, temos a **posição.**

    Consulte a [lista de parâmetros URI](/rest/api/maps/mobility/gettransitroutepreview#uri-parameters) que pode utilizar no seu pedido para a [API das Rotas de Trânsito.](/rest/api/maps/mobility/gettransitroutepreview)
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. Após um pedido bem sucedido, a estrutura de resposta deve parecer a seguinte:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            }
        ]
    }
    ```

4. Se observar com atenção, existem várias rotas **de autocarro** na resposta. Cada rota tem um **ID de itinerário** único, um resumo que descreve cada etapa da rota, e um `itineraryFare` que dá tanto o preço itemado como o preço total para as passagens de ônibus. Uma perna de rota é a parte da rota entre dois pontos de paragem. Em seguida, vamos pedir detalhes para a rota mais rápida usando a `itineraryId` resposta.

## <a name="request-fastest-route-itinerary"></a>Solicite o itinerário de rota mais rápido

O serviço Azure Maps [Get Transit Itinerário](/rest/api/maps/mobility/gettransititinerarypreview) permite-lhe solicitar dados para uma determinada rota utilizando o **ID** do itinerário da rota devolvido pelo serviço [API das Rotas de Trânsito.](/rest/api/maps/mobility/gettransitroutepreview) Para fazer um pedido, complete os passos abaixo:

1. No Carteiro, clique em **Novo Pedido**  |  **GET e** nomeie-o Obtenha **informações de Trânsito.**

2. No separador 'Construtor', selecione o método **GET** HTTP. Introduza o seguinte URL de pedido para o seu ponto final da API e clique em **Enviar**.

    Vamos definir o `detailType` parâmetro para a **geometria** para que a resposta contenha informações de paragem para o trânsito público e navegação por turnos para caminhadas e pernas de bicicleta da rota.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```

3. Após um pedido bem sucedido, a estrutura de resposta deve parecer a de baixo.  Se observar a resposta do JSON, notará que cada perna de autocarro contém um `legfare` elemento. O `legfare` elemento contém o custo, em cêntimos, de cada rota de autocarro que é comprada separadamente. No final da resposta, verá um `itineraryFare` elemento que contém o custo, em cêntimos, de toda a rota. Neste exemplo, há quatro rotas de autocarros em `$2.75` cada um. No entanto, se comprar um único bilhete para toda a rota, o custo `$5.50` é.

    ```JSON
   {
        "departureTime": "2020-07-22T19:54:47Z",
        "arrivalTime": "2020-07-22T21:12:21Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T19:54:47Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    },
                    {
                        "stopId": "522---2062263",
                        "stopKey": "85630",
                        "stopName": "Bellevue Tc",
                        "stopCode": "85630",
                        "position": {
                            "latitude": 47.615591,
                            "longitude": -122.196491
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T20:15:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                       ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:20:00Z",
                "legEndTime": "2020-07-22T20:28:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666071",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "241",
                    "caption1": "Eastgate P&R - Bellevue Transit Center",
                    "caption2": "241 Eastgate P&R - Bellevue Transit Center",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756619",
                    "lineGroupId": "522---666071",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "241",
                    "lineDestination": "Eastgate P&R Factoria"
                },
                "stops": [
                ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                   ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:31:00Z",
                "legEndTime": "2020-07-22T20:54:13Z",
                "lineGroup": {
                    "lineGroupId": "522---312636",
                    "agencyId": "522---854535",
                    "agencyName": "Sound Transit",
                    "lineNumber": "550",
                    "caption1": "Bellevue - Seattle",
                    "caption2": "550 Bellevue - Seattle",
                    "color": "00008B",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---962201",
                    "lineGroupId": "522---312636",
                    "direction": "backward",
                    "agencyId": "522---854535",
                    "lineNumber": "550",
                    "lineDestination": "Seattle"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                 ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:57:00Z",
                "legEndTime": "2020-07-22T21:06:00Z",
                "lineGroup": {
                    "lineGroupId": "522---480518",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "13",
                    "caption1": "Seattle Pacific - Downtown Seattle",
                    "caption2": "13 Seattle Pacific - Downtown Seattle",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---1744932",
                    "lineGroupId": "522---480518",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "13",
                    "lineDestination": "Seattle Pacific University Seattle Center W"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                      ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
        ],
        "itineraryFare": {
            "price": {
                "amount": 550,
                "currencyCode": "USD"
            },
            "tickets": [
                {
                    "amount": 275,
                    "currencyCode": "USD"
                },
                {
                    "amount": 275,
                    "currencyCode": "USD"
                }
            ]
        }
    }

    ```

## <a name="next-steps"></a>Passos seguintes

Saiba como solicitar dados em tempo real utilizando serviços de mobilidade (Pré-visualização):

> [!div class="nextstepaction"]
> [Como solicitar dados em tempo real](how-to-request-real-time-data.md)

Explore a documentação da API dos serviços de mobilidade Azure Maps (Preview)

> [!div class="nextstepaction"]
> [Documentação de serviços de mobilidade](/rest/api/maps/mobility)