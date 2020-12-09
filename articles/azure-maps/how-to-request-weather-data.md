---
title: Solicite dados meteorológicos em tempo real e previstos utilizando os serviços meteorológicos Azure Maps (Pré-visualização)
description: Saiba como solicitar dados meteorológicos em tempo real (corrente) e previstos (minutos, horas, diariamente) utilizando os serviços de clima do Microsoft Azure Maps (Preview)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: a5ae262d2882bd76e31666f058fa9a7a703a9e1e
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906017"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services-preview"></a>Solicite dados meteorológicos em tempo real e previstos utilizando os serviços meteorológicos Azure Maps (Pré-visualização) 

> [!IMPORTANT]
> Os serviços Azure Maps Weather estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [serviços Azure](/rest/api/maps/weather) Maps Weather são um conjunto de APIs RESTful que permite aos desenvolvedores integrar dados e visualizações meteorológicas altamente dinâmicas e em tempo real e previsões nas suas soluções. Neste artigo, vamos mostrar-lhe como solicitar dados meteorológicos em tempo real e previstos.

Neste artigo você vai aprender, como:

* Solicite dados meteorológicos em tempo real (corrente) utilizando a [API get condições correntes.](/rest/api/maps/weather/getcurrentconditionspreview)
* Solicite alertas meteorológicos severos utilizando a [API de Alertas de Mau Tempo](/rest/api/maps/weather/getsevereweatheralertspreview).
* Solicite previsões diárias utilizando a [API get daily forecast](/rest/api/maps/weather/getdailyforecastpreview).
* Solicite previsões horárias utilizando a [API de Previsão Horária.](/rest/api/maps/weather/gethourlyforecastpreview)
* Solicite previsões minuto a minuto utilizando a [API de Previsão de Minutos](/rest/api/maps/weather/getminuteforecastpreview).

Este vídeo fornece exemplos para fazer chamadas REST para os serviços de clima Azure Maps.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player" width="960" height="540" allowFullScreen frameBorder="0" title="Serviços Azure Maps Weather para programadores - Microsoft Channel 9 Video"></iframe>

## <a name="prerequisites"></a>Pré-requisitos

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](./how-to-manage-authentication.md)

    >[!IMPORTANT]
    >A [API de Previsão de Minutos](/rest/api/maps/weather/getminuteforecastpreview) requer uma chave de nível de preços S1. Todas as outras APIs requerem uma chave de nível de preços S0.

Este tutorial usa a aplicação [Do Carteiro,](https://www.postman.com/) mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="request-real-time-weather-data"></a>Solicito dados meteorológicos em tempo real

A [API das Condições Correntes](/rest/api/maps/weather/getcurrentconditionspreview) devolve condições meteorológicas detalhadas, como precipitação, temperatura e vento para uma determinada localização coordenada. Além disso, as observações das últimas 6 ou 24 horas para um determinado local podem ser recuperadas. A resposta inclui detalhes como data e hora de observação, breve descrição das condições meteorológicas, ícone meteorológico, bandeiras indicadoras de precipitação e temperatura. RealFeel™ O índice de temperatura e ultravioleta (UV) também são devolvidos.

Neste exemplo, você usará a [API get condições atuais](/rest/api/maps/weather/getcurrentconditionspreview) para recuperar as condições meteorológicas atuais nas coordenadas localizadas em Seattle, WA.

1. Abra a aplicação do Carteiro. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.** Você usará esta coleção para o resto dos exemplos neste documento.

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada no passo anterior e, em seguida, **selecione Guardar**.

3. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Clique no botão **de enviar** azul. O corpo de resposta contém informações meteorológicas atuais.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>Solicite alertas meteorológicos severos

[Azure Maps Get Severe Weather Alerts API](/rest/api/maps/weather/getsevereweatheralertspreview) devolve os alertas meteorológicos severos que estão disponíveis em todo o mundo, tanto das Agências Meteorológicas Oficiais do Governo como da condução global dos fornecedores de alerta meteorológico regional. O serviço pode devolver detalhes como tipo de alerta, categoria, nível e descrições detalhadas sobre os alertas severos ativos para a localização solicitada, tais como furacões, trovoadas, relâmpagos, ondas de calor ou incêndios florestais. Como exemplo, os gestores de logística podem visualizar as condições meteorológicas severas num mapa, juntamente com localizações empresariais e rotas planeadas, e coordenar ainda mais com motoristas e trabalhadores locais.

Neste exemplo, você usará a [API de Alertas de Mau Tempo](/rest/api/maps/weather/getsevereweatheralertspreview) para recuperar as condições meteorológicas atuais nas coordenadas localizadas em Cheyenne, WY.

>[!NOTE]
>Este exemplo recupera alertas meteorológicos severos no momento desta escrita. É provável que não haja mais alertas meteorológicos severos no local solicitado. Para recuperar dados de alerta severos ao executar este exemplo, terá de recuperar dados num local diferente de coordenadas.

1. Abra a aplicação Carteiro, clique em **Novo** e selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada na secção anterior ou crie uma nova e, em seguida, **selecione Guardar**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Clique no botão **de enviar** azul. Se não houver alertas meteorológicos severos, o corpo de resposta conterá uma `results[]` matriz vazia. Se houver alertas meteorológicos severos, o corpo de resposta contém algo como a seguinte resposta JSON:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>Solicite dados diários de previsão do tempo

A [API get daily forecast](/rest/api/maps/weather/getdailyforecastpreview) retorna a previsão meteorológica detalhada, como temperatura e vento. O pedido pode especificar quantos dias para regressar: 1, 5, 10, 15, 25 ou 45 dias para um determinado local de coordenadas. A resposta inclui detalhes como temperatura, vento, precipitação, qualidade do ar e índice UV.  Neste exemplo, solicitamos cinco dias por `duration=5` definição.

>[!IMPORTANT]
>No nível de preços S0, pode solicitar a previsão diária para os próximos 1, 5, 10 e 15 dias. No nível de preços S1, também pode solicitar previsão diária para os próximos 25 dias, e 45 dias.

Neste exemplo, você usará a [API Get Daily Forecast](/rest/api/maps/weather/getdailyforecastpreview) para recuperar a previsão meteorológica de cinco dias para as coordenadas localizadas em Seattle, WA.

1. Abra a aplicação Carteiro, clique em **Novo** e selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada na secção anterior ou crie uma nova e, em seguida, **selecione Guardar**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Clique no botão **de enviar** azul. O corpo de resposta contém os dados da previsão meteorológica de cinco dias. Por uma questão de brevidade, a resposta do JSON abaixo mostra a previsão para o primeiro dia.
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>Solicitar dados de previsão do tempo de hora a hora

A [API de Previsão De Hora](/rest/api/maps/weather/gethourlyforecastpreview) de Saída devolve a previsão meteorológica detalhada por hora para os próximos 1, 12, 24 (1 dia), 72 (3 dias), 120 (5 dias) e 240 horas (10 dias) para a localização da coordenada dada. A API devolve detalhes como temperatura, humidade, vento, precipitação e índice UV.

>[!IMPORTANT]
>No nível de preços S0, pode solicitar a previsão horária para as próximas 1, 12, 24 horas (1 dia) e 72 horas (3 dias). No nível de preços S1, também pode solicitar previsão horária para os próximos 120 (5 dias) e 240 horas (10 dias).

Neste exemplo, você usará a [API get hourly previsão](/rest/api/maps/weather/gethourlyforecastpreview) para recuperar a previsão do tempo para as próximas 12 horas nas coordenadas localizadas em Seattle, WA.

1. Abra a aplicação Carteiro, clique em **Novo** e selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada na secção anterior ou crie uma nova e, em seguida, **selecione Guardar**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Clique no botão **de enviar** azul. O corpo de resposta contém dados de previsão meteorológica para as próximas 12 horas. Por uma questão de brevidade, a resposta do JSON abaixo mostra a previsão para a primeira hora.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>Solicitar dados de previsão meteorológica minuto a minuto

 A [API de Previsão de Minutos](/rest/api/maps/weather/getminuteforecastpreview) retorna as previsões minuto a minuto para um determinado local durante os próximos 120 minutos. Os utilizadores podem solicitar previsões meteorológicas em intervalos de 1, 5 e 15 minutos. A resposta inclui detalhes como o tipo de precipitação (incluindo chuva, neve ou uma mistura de ambos), hora de início e valor de intensidade de precipitação (dBZ).

Neste exemplo, você usará a [API get minute previsão](/rest/api/maps/weather/getminuteforecastpreview) para recuperar a previsão meteorológica minuto a minuto nas coordenadas localizadas em Seattle, WA. A previsão meteorológica é de 120 minutos. A nossa consulta solicita que a previsão seja dada em intervalos de 15 minutos, mas pode ajustar o parâmetro para ser de 1 ou 5 minutos.

1. Abra a aplicação Carteiro, clique em **Novo** e selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção criada na secção anterior ou crie uma nova e, em seguida, **selecione Guardar**.

2. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL. Para este pedido, e outros pedidos mencionados neste artigo, `{Azure-Maps-Primary-Subscription-key}` substitua-o pela sua chave de subscrição primária.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Clique no botão **de enviar** azul. O corpo de resposta contém dados de previsão meteorológica para os próximos 120 minutos, em intervalos de 15 minutos.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conceitos de serviços meteorológicos Azure Maps (Pré-visualização)](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [Serviços meteorológicos Azure Maps (Pré-visualização) REST API](/rest/api/maps/weather)