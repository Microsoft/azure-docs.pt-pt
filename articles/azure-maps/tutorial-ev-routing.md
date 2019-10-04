---
title: Roteamento de veículo elétrico usando o Azure Notebooks (Python) | Microsoft Docs
description: Roteamento de EV usando APIs de roteamento do Azure Maps e Azure Notebooks.
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: c4b46bc952782fc7c9b56d6f0c049fe17b63d0f2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836398"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>Roteamento de veículo elétrico usando o Azure Notebooks (Python)

O Azure Maps é um portfólio de APIs de serviço geoespaciais nativamente integrados ao Azure que permitem que desenvolvedores, empresas e ISVs criem aplicativos com reconhecimento de local e soluções de IoT, mobilidade, logística e acompanhamento de ativos. As APIs REST do Azure Maps podem ser chamadas de linguagens como Python e R para habilitar a análise de dados geoespaciais e cenários de aprendizado de máquina. O mapas do Azure oferece um conjunto robusto de [APIs de roteamento](https://docs.microsoft.com/rest/api/maps/route) que permite aos usuários calcular as rotas entre vários pontos de dados com base em várias condições, como tipo de veículo ou área alcançável. Neste tutorial, veremos um cenário para ajudar um driver de veículo elétrico, cujo veículo está com pouca carga de bateria, para encontrar a estação de cobrança mais próxima possível em relação ao tempo da unidade.

Neste tutorial, você vai:

> [!div class="checklist"]
> * Criar e executar um Jupyter Notebook em [Azure notebooks](https://docs.microsoft.com/azure/notebooks) na nuvem
> * Chamar as APIs REST do Azure Maps no Python
> * Pesquise um intervalo acessível com base no modelo de consumo do veículo elétrico.
> * Pesquise pelas estações de carregamento do veículo elétrico dentro do intervalo acessível (ou isochrone).
> * Renderizar as estações de limite e carga de intervalo acessíveis em um mapa.
> * Localize e visualize a rota para a estação de carregamento de veículo elétrico mais próxima com base no tempo.


## <a name="prerequisites"></a>Pré-requisitos 

Para concluir as etapas deste tutorial, primeiro você precisa criar uma conta do Azure Maps e obter sua chave primária (chave de assinatura). Siga as instruções em [gerenciar conta](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) para criar uma assinatura de conta do Azure Maps com o tipo de preço S1 e siga as etapas em [obter chave primária](./tutorial-search-location.md#getkey) para obter a chave de assinatura primária para sua conta.

## <a name="create-an-azure-notebook"></a>Criar um bloco de anotações do Azure

Para acompanhar este tutorial, você precisará criar um projeto de bloco de anotações do Azure e baixar e executar o arquivo do bloco de anotações Jupyter. O arquivo de bloco de anotações contém o código Python, que implementa o cenário neste tutorial. Siga as etapas abaixo para criar um projeto de bloco de anotações do Azure e carregar o documento do bloco de anotações Jupyter nele.

1. Aceda a [blocos de notas do Azure](https://notebooks.azure.com) e iniciar sessão. Para obter mais informações, consulte [início rápido](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
2. Na página de seu perfil público, selecione **meus projetos** na parte superior da página.

    ![meu projeto](./media/tutorial-ev-routing/myproject.png)

3. Na página **meus projetos** , selecione **novo projeto**.
 
   ![Novo projeto](./media/tutorial-ev-routing/create-project.png)

4. No pop-up **criar novo projeto** que aparece, insira as informações a seguir e clique em **criar**:
    * Nome do Projeto
    * ID do Projeto
 
    ![Criar projeto](./media/tutorial-ev-routing/create-project-window.png)

5. Depois que o projeto for criado, baixe o [arquivo de documento do notebook Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) do [repositório do Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

6. Selecione o projeto na lista projetos na página **meus projetos** e clique em **carregar** para carregar o arquivo de documento do notebook Jupyter. Carregue o arquivo do seu computador e clique em **concluído**.

    ![carregar bloco de anotações](./media/tutorial-ev-routing/upload-notebook.png)

7. Após um upload bem-sucedido, você verá o arquivo na página do projeto. Clique no arquivo do bloco de anotações para abri-lo como um Jupyter Notebook.

Para entender melhor a funcionalidade implementada no arquivo do bloco de anotações, recomendamos que você execute o código no bloco de anotações uma célula por vez. Você pode executar o código em cada célula clicando no botão **executar** na parte superior do aplicativo do bloco de anotações.

  ![Executar](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Instalar pacotes de nível de projeto

Para executar o código no bloco de anotações, você precisará instalar pacotes no nível do projeto. Siga as etapas abaixo para instalar os pacotes necessários:

1. Baixe o arquivo ["requirements. txt"](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) do [repositório Jupyter notebook do Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) e carregue-o em seu projeto.
2. No dashboard do projeto, selecione **definições do projeto**. 
3. No pop-up que aparece, selecione a **guia ambiente**e, em seguida, selecione **Adicionar**.
4. Em **etapas de configuração do ambiente**, 
    * No primeiro controle suspenso, escolha **requirements. txt**.
    * No segundo controle suspenso, escolha o arquivo "requirements. txt".
    * No terceiro controle suspenso, escolha Python versão 3,6 como a versão do Python.
7. Selecione **Guardar**.

    ![Instalar pacotes](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>Carregar módulos e estruturas necessárias

Execute o script a seguir para carregar todos os módulos e estruturas necessários.

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>Solicitação de limite de intervalo acessível

Em nosso cenário, uma empresa de entrega de pacote tem alguns veículos elétricos em sua frota. Durante o dia, os veículos elétricos precisam ser recobrados sem a necessidade de retornar ao depósito. Sempre que o encargo restante atual para o veículo elétrico é menor que uma hora (o veículo elétrico está com um custo baixo), precisamos Pesquisar um conjunto de estações de carregamento que estejam dentro do intervalo acessível e obter as informações de limite para esse intervalo. Como a empresa prefere usar rotas balanceadas pela economia e velocidade, o routetype solicitado é ' eco '. O script a seguir chama a [API obter intervalo de rotas](https://docs.microsoft.com/rest/api/maps/route/getrouterange) do serviço de roteamento do Azure Maps com parâmetros para o modelo de consumo do veículo e analisa a resposta para criar um objeto Polygon do formato geojson que representa o intervalo máximo de alcançável do carro .

Execute o script na célula abaixo para obter limites para o intervalo acessível do veículo elétrico.

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get bounds for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>Pesquisar estações de carregamento do veículo elétrico dentro do intervalo acessível

Assim que tivermos o intervalo alcançável (isochrone) para o veículo elétrico, podemos Pesquisar estações de carregamento nesse intervalo. O script a seguir chama a [pesquisa de postagem](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) do Azure Maps dentro da API de geometria para pesquisar estações de carregamento de veículos elétricos dentro dos limites do intervalo máximo de alcançável do carro e, em seguida, analisa a resposta para uma matriz de locais acessíveis.

Execute o script a seguir para procurar estações de carregamento de veículos elétricos dentro do intervalo acessível.

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Carregar intervalo acessível e pontos de cobrança para o serviço de dados do Azure Maps

Para visualizar as estações de carregamento e limites para o intervalo máximo acessível do veículo elétrico no mapa, precisamos carregar os dados de limite e os dados das estações de carregamento como objetos geojson para o serviço de dados do Azure Maps, usando a [API de carregamento de dados ](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Execute as duas células a seguir para carregar os dados de limite e de ponto de carregamento para o serviço de dados do Azure Maps.

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload range data to Azure Maps data service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload EV charging stations data to Azure Maps data service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-charging-stations-and-reachable-range-on-map"></a>Renderizar estações de carregamento e intervalo alcançável no mapa

Depois que os dados forem carregados para o serviço de dados, agora executaremos o script a seguir para chamar o [serviço de imagem do mapa Get](https://docs.microsoft.com/rest/api/maps/render/getmapimage) do Azure Maps para renderizar os pontos de carregamento e o limite máximo de alcançável na imagem do mapa estático.

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![intervalo de localização](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>Encontre a estação de cobrança ideal para parar

Depois que tivermos todas as possíveis estações de carregamento dentro do intervalo acessível, queremos saber qual delas pode ser alcançada no período mínimo de tempo. O script a seguir chama a [API de roteamento de matriz](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) do Azure Maps retornando para o local do veículo fornecido o tempo de viagem e a distância para cada local de estação de cobrança especificado. O script na próxima célula, analisa a resposta para obter o local da estação de cobrança acessível mais próxima em relação ao tempo.

Execute a célula a seguir para localizar a estação de cobrança acessível mais próxima que pode ser alcançada no período mínimo de tempo.

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>Calcular a rota para a estação de cobrança mais próxima

Agora que encontramos a estação de cobrança mais próxima, vamos chamar a [API obter direções de rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) para solicitar a rota detalhada do local atual do veículo elétrico para a estação de cobrança.

Execute o script na célula a seguir para obter a rota e analise a resposta para criar um objeto geojson que representa a rota.

```python
# Get route from current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Visualizar a rota

Para visualizar a rota, primeiro carregaremos os dados de rota como um objeto geojson no serviço de dados do Azure Maps usando a [API de carregamento de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)do Azure Maps. Em seguida, chame o serviço de processamento, [obtenha a API de mapa de imagem](https://docs.microsoft.com/rest/api/maps/render/getmapimage) para renderizar a rota no mapa e visualizá-la.

Execute o script a seguir para obter uma imagem para a rota renderizada no mapa.

```python
# Upload route data to Azure data service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![rota](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a chamar as APIs REST do Azure Maps diretamente e visualizar os dados do Azure Maps usando o Python.

Para explorar as APIs do Azure Maps usadas neste tutorial, consulte:

* [Obter intervalo de rotas](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Postar pesquisa dentro da geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Carregamento de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Render-obter imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Lançar matriz de rota](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Obter direções de rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Para obter uma lista completa das APIs REST do Azure Maps, consulte:

* [APIs REST do Azure Maps](https://docs.microsoft.com/azure/azure-maps/#reference)

Para saber mais sobre Azure Notebooks, consulte:

* [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)