---
title: 'Tutorial: Junte dados de sensores com dados de previsão meteorológica utilizando Cadernos Azure (Python) [ Python) [ Microsoft Azure Maps'
description: Este tutorial mostra-lhe como juntar dados de sensores com dados de previsão meteorológica do Microsoft Azure Maps Weather Service utilizando cadernos Azure (Python).
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e5292f5166e739264e9cf969480b70f415fcc75a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80333486"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Tutorial: Junte dados de sensores com dados de previsão meteorológica utilizando cadernos Azure (Python)

A energia eólica é uma fonte de energia alternativa para os combustíveis fósseis combaterem as alterações climáticas. Como o vento não é consistente por natureza, os operadores de energia eólica precisam de construir modelos de aprendizagem automática (ML) para prever a capacidade de energia eólica. Esta previsão é necessária para satisfazer a procura de eletricidade e garantir a estabilidade da rede. Neste tutorial, percorremos a forma como os dados da previsão meteorológica do Azure Maps são combinados com dados de demonstração para leituras meteorológicas. Os dados da previsão meteorológica são solicitados através do serviço De meteorologia Azure Maps.

Neste tutorial, irá:

> [!div class="checklist"]
> * Trabalhe com ficheiros de dados em [Cadernos Azure](https://docs.microsoft.com/azure/notebooks) na nuvem.
> * Carregue os dados de demonstração do ficheiro.
> * Ligue para o Azure Maps REST APIs em Python.
> * Entregue dados de localização no mapa.
> * Enriqueça os dados da demonstração com os dados meteorológicos da Azure Maps [Daily Forecast.](https://aka.ms/AzureMapsWeatherDailyForecast)
> * Traçar dados de previsão em gráficos.


## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, primeiro precisa:

1. Crie uma subscrição de conta Azure Maps no nível de preços S0 seguindo instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps).
2. Obtenha a chave de subscrição primária para a sua conta, siga as instruções para obter a [chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](./how-to-manage-authentication.md).

Para conhecer os cadernos Azure e saber como começar, siga as instruções [Criar um Caderno Azure](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> O ficheiro de caderno Jupyter para este projeto pode ser descarregado do [repositório de cadernos Weather Maps Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Carregue os módulos e quadros necessários

Para carregar todos os módulos e quadros necessários, execute o seguinte script:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Dados meteorológicos de importação

Para o bem deste tutorial, usaremos leituras de dados meteorológicos de sensores instalados em quatro turbinas eólicas diferentes. Os dados da amostra consistem em 30 dias de leituras meteorológicas. Estas leituras são recolhidas a partir de centros de dados meteorológicos perto de cada local da turbina. Os dados da demonstração contêm leituras de dados para temperatura, velocidade do vento e direção. Pode baixar os dados da demonstração a partir [daqui](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). O guião abaixo importa dados de demonstração para o Caderno Azure.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Solicitar dados de previsão diária

No nosso cenário, gostaríamos de solicitar previsões diárias para cada localização do sensor. O seguinte guião chama a [API de Previsão Diária](https://aka.ms/AzureMapsWeatherDailyForecast) do serviço meteorológico Azure Maps. Esta API devolve a previsão meteorológica para cada turbina eólica, para os próximos 15 dias a partir da data atual.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

O script abaixo torna as localizações da turbina no mapa, chamando o serviço Azure Maps [Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage).

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Localizações de turbinas](./media/weather-service-tutorial/location-map.png)


Vamos agrupar os dados da previsão com os dados da demonstração com base no ID da estação. A identificação da estação é para o centro de dados meteorológicos. Este agrupamento aumenta os dados da demonstração com os dados previstos.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

A tabela seguinte apresenta os dados históricos e de previsão combinados para um dos locais da turbina.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Dados agrupados](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Dados de previsão do enredo

Vamos traçar os valores previstos contra os dias para os quais estão previstos. Este enredo permite-nos ver as mudanças de velocidade e direção do vento para os próximos 15 dias.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Os gráficos abaixo visualizam os dados da previsão. Para a mudança da velocidade do vento, consulte o gráfico esquerdo. Para mudar na direção do vento, consulte o gráfico certo. Estes dados são previsão para os próximos 15 dias a partir do dia em que os dados são solicitados.

<center>

![Plano de](./media/weather-service-tutorial/speed-date-plot.png) ![velocidade do vento Enredo de direção vento enredo](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Passos seguintes

Neste tutorial aprendeu, como chamar AFS REST Do Azure Maps para obter dados de previsão meteorológica. Também aprendeu a visualizar os dados em gráficos.

Para saber mais sobre como ligar para O Azure Maps REST APIs dentro dos Cadernos Azure, consulte o [encaminhamento EV utilizando cadernos Azure](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Para explorar as APIs do Azure Maps que são usadas neste tutorial, consulte:

* [Previsão Diária](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Render - Obter Imagem do Mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Para obter uma lista completa de APIs REST DO Azure Maps, consulte [O PDP](https://docs.microsoft.com/azure/azure-maps/consumption-model)REST Do Azure Maps .

Para saber mais sobre os Cadernos Azure, consulte [os Cadernos Azure.](https://docs.microsoft.com/azure/notebooks)
