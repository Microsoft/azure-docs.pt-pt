---
title: 'Tutorial: unir dados de sensor com dados de previsão do tempo usando Azure Notebooks (Python) | Microsoft Docs'
description: 'Tutorial: Este tutorial mostra como unir dados de sensor com dados de previsão do tempo do serviço de clima do Azure Maps usando o Azure Notebooks (Python).'
author: walsehgal
ms.author: v-musehg
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 809c188dc37aba64de27e89e38acd8692c7de032
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613572"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Tutorial: unir dados de sensor com dados de previsão do tempo usando Azure Notebooks (Python)

A energia do vento é uma fonte de energia alternativa para Combustívels combustíveis fósseis para combater a alteração climática. Como o vento não é consistente por natureza, os operadores de energia de vento precisam criar modelos ML (Machine Learning) para prever a capacidade de energia do vento para atender à demanda de eletricidade e garantir a estabilidade da grade. Neste tutorial, percorreremos como os dados de previsão do clima do Azure podem ser combinados com o conjunto de dados de demonstração de locais de sensor com leituras de clima. Os dados de previsão do tempo são solicitados chamando o serviço meteorológico do Azure Maps.

Neste tutorial, irá:

> [!div class="checklist"]
> * Trabalhar com arquivos de dados em [Azure notebooks](https://docs.microsoft.com/azure/notebooks) na nuvem.
> * Carregue dados de demonstração do arquivo.
> * Chamar as APIs REST do Azure Maps no Python.
> * Renderizar dados de localização no mapa.
> * Enriquecer os dados de demonstração com os dados meteorológicos de [previsão diária](https://aka.ms/AzureMapsWeatherDailyForecast) do Azure Maps.
> * Plotar dados de previsão em grafos.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, primeiro você precisa:

1. Crie uma assinatura de conta do Azure Maps no tipo de preço S0 seguindo as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps).
2. Obtenha a chave de assinatura primária para sua conta, siga as instruções em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Para obter mais detalhes sobre a autenticação no Azure Maps, consulte [gerenciar a autenticação no Azure Maps](./how-to-manage-authentication.md).

Para se familiarizar com os blocos de anotações do Azure e saber como começar, siga as instruções [criar um bloco de anotações do Azure](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> O arquivo do bloco de anotações Jupyter para este projeto pode ser baixado do [repositório de blocos de anotações do Jupyter do clima Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Carregar os módulos e as estruturas necessárias

Para carregar todos os módulos e estruturas necessários, execute o seguinte script:

```python
import aiohttp
import pandas as pd
import datetime
from IPython.display import Image, display
```

## <a name="import-weather-data"></a>Importar dados meteorológicos

Para este tutorial, usaremos as leituras de dados meteorológicos de sensores instalados em quatro turbinas de vento diferentes. Os dados de exemplo consistem em 30 dias de leituras de clima coletados de data centers meteorológicos próximos a cada local de turbina. Os dados de demonstração contêm leituras de dados para temperatura, velocidade do vento e direção. Você pode baixar os dados de demonstração [aqui](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). O script abaixo importa dados de demonstração para o bloco de anotações do Azure.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Solicitar dados de previsão diária

Em nosso cenário de exemplo, gostaríamos de solicitar uma previsão diária para cada local do sensor. O script a seguir chama a [API de previsão diária](https://aka.ms/AzureMapsWeatherDailyForecast) do serviço de clima do Azure Maps para obter a previsão do tempo diária para cada turbina do vento, nos próximos 15 dias a partir da data atual.


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

O script abaixo renderiza os locais de turbina no mapa chamando o serviço de obtenção de [imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)do Azure Maps.

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

![Locais de turbina](./media/weather-service-tutorial/location-map.png)


Para aumentar os dados de demonstração com os dados de previsão, agruparemos os dados de previsão com os dados de demonstração com base na ID da estação para o clima data center.

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

A tabela a seguir exibe os dados históricos e de previsão combinados para um dos locais de turbina.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![de dados agrupados](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Plotar dados de previsão

Para ver como a velocidade e a direção do vento são alteradas no decorrer dos próximos 15 dias, plotaremos os valores previstos em relação aos dias pelos quais eles são previstos.

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

Os grafos abaixo visualizam os dados de previsão para a alteração da velocidade do vento (grafo esquerdo) e a direção (grafo à direita) nos próximos 15 dias a partir do dia em que os dados são solicitados.

<center>

gráfico de velocidade de vento ![](./media/weather-service-tutorial/speed-date-plot.png) ![direção do vento](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu, como chamar as APIs REST do Azure Maps para obter dados de previsão do clima e visualizar os dados em grafos.

Para saber mais sobre como chamar as APIs REST do Azure Maps dentro de Azure Notebooks, consulte [Roteamento de EV usando Azure notebooks](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Para explorar as APIs do Azure Maps que são usadas neste tutorial, consulte:

* [Previsão diária](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Render-obter imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Para obter uma lista completa de APIs REST do Azure Maps, consulte [APIs REST do Azure Maps](https://docs.microsoft.com/azure/azure-maps/consumption-model).

Para saber mais sobre Azure Notebooks, consulte [Azure notebooks](https://docs.microsoft.com/azure/notebooks).
