---
title: 'Tutorial: rotear veículos elétricos usando o Azure Notebooks (Python) | Mapas do Microsoft Azure'
description: Encaminhar veículos elétricos usando Microsoft Azure mapeia APIs de roteamento e Azure Notebooks.
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f45859370ae178fb186399fdd2648bf37f0985aa
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910912"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Tutorial: rotear veículos elétricos usando o Azure Notebooks (Python)

O mapas do Azure é um portfólio de APIs de serviço geoespaciais que são nativamente integrados ao Azure. Com essas APIs, os desenvolvedores, as empresas e os ISVs podem criar aplicativos com reconhecimento de local e soluções de IoT, mobilidade, logística e acompanhamento de ativos. 

As APIs REST do Azure Maps podem ser chamadas de idiomas como Python e R para habilitar a análise de dados geoespaciais e cenários de aprendizado de máquina. O mapas do Azure oferece um conjunto robusto de [APIs de roteamento](https://docs.microsoft.com/rest/api/maps/route) que permitem que os usuários calculem rotas entre vários pontos de dados. Os cálculos se baseiam em várias condições, como tipo de veículo ou área alcançável. 

Neste tutorial, você percorre um cenário para ajudar um driver cuja carga de bateria do veículo elétrico é baixa para encontrar a estação de cobrança mais próxima possível, com base no tempo da unidade do local do veículo.

Neste tutorial, irá:

> [!div class="checklist"]
> * Crie e execute um bloco de anotações Jupyter em [Azure notebooks](https://docs.microsoft.com/azure/notebooks) na nuvem.
> * Chamar as APIs REST do Azure Maps no Python.
> * Pesquise um intervalo acessível com base no modelo de consumo do veículo elétrico.
> * Pesquise pelas estações de carregamento do veículo elétrico dentro do intervalo acessível ou isochrone.
> * Renderizar as estações de limite e carga de intervalo acessíveis em um mapa.
> * Localize e visualize uma rota para a estação de carregamento de veículo elétrico mais próxima com base no tempo da unidade.


## <a name="prerequisites"></a>Pré-requisitos 

Para concluir este tutorial, primeiro você precisa criar uma conta do Azure Maps e obter sua chave primária (chave de assinatura). 

Para criar uma assinatura de conta do Azure Maps no tipo de preço S1, siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura de conta do Azure Maps com o tipo de preço S1. 

Para obter a chave de assinatura primária para sua conta, siga as instruções em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Para obter mais detalhes sobre a autenticação no Azure Maps, consulte [gerenciar a autenticação no Azure Maps](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebook"></a>Criar um bloco de anotações do Azure

Para acompanhar este tutorial, você precisa criar um projeto de bloco de anotações do Azure e baixar e executar o arquivo do bloco de anotações Jupyter. O arquivo de bloco de anotações contém o código Python, que implementa o cenário neste tutorial. Para criar um projeto de bloco de anotações do Azure e carregar o documento do bloco de anotações Jupyter para ele, faça o seguinte:

1. Aceda a [blocos de notas do Azure](https://notebooks.azure.com) e iniciar sessão. Para obter mais informações, consulte [início rápido: entrar e definir uma ID de usuário](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
1. Na parte superior da sua página de perfil público, selecione **meus projetos**.

    ![O botão meus projetos](./media/tutorial-ev-routing/myproject.png)

1. Na página **meus projetos** , selecione **novo projeto**.
 
   ![O botão novo projeto](./media/tutorial-ev-routing/create-project.png)

1. No painel **criar novo projeto** , insira um nome de projeto e uma ID de projeto.
 
    ![O painel criar novo projeto](./media/tutorial-ev-routing/create-project-window.png)

1. Selecione **Criar**.

1. Depois que o projeto for criado, baixe o [arquivo de documento do notebook Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) do [repositório do bloco de anotações Jupyter do Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

1. Na lista projetos na página **meus projetos** , selecione seu projeto e, em seguida, selecione **carregar** para carregar o arquivo de documento do notebook Jupyter. 

    ![carregar bloco de anotações](./media/tutorial-ev-routing/upload-notebook.png)

1. Carregue o arquivo do seu computador e, em seguida, selecione **concluído**.

1. Depois que o carregamento for concluído com êxito, o arquivo será exibido na página do projeto. Selecione o arquivo para abri-lo como um notebook Jupyter.

Para ajudá-lo a entender melhor a funcionalidade implementada no arquivo do bloco de anotações, é recomendável executar o código no bloco de anotações uma célula por vez. Você pode executar o código em cada célula selecionando o botão **executar** na parte superior do aplicativo do bloco de anotações.

  ![O botão executar](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Instalar pacotes de nível de projeto

Para executar o código no bloco de anotações, instale os pacotes no nível do projeto fazendo o seguinte:

1. Baixe o arquivo [*requirements. txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) do [repositório do bloco de anotações Jupyter do Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)e, em seguida, carregue-o em seu projeto.
1. No dashboard do projeto, selecione **definições do projeto**. 
1. No painel **configurações do projeto** , selecione a guia **ambiente** e, em seguida, selecione **Adicionar**.
1. Em **etapas de configuração do ambiente**, faça o seguinte:   
    a. Na primeira lista suspensa, selecione **requirements. txt**.  
    b. Na segunda lista suspensa, selecione seu arquivo *requirements. txt* .  
    c. Na terceira lista suspensa, selecione **Python versão 3,6** como sua versão.
1. Selecione **Guardar**.

    ![Instalar pacotes](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Carregar os módulos e as estruturas necessárias

Para carregar todos os módulos e estruturas necessários, execute o seguinte script:

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Solicitar o limite de intervalo acessível

Em nosso cenário, uma empresa de entrega de pacote tem alguns veículos elétricos em sua frota. Durante o dia, os veículos elétricos precisam ser recobrados sem a necessidade de retornar ao depósito. Toda vez que a cobrança restante atual cai para menos de uma hora (ou seja, a bateria está com pouca carga), você pesquisa um conjunto de estações de carregamento que estão dentro de um intervalo acessível e obtém as informações de limite para esse intervalo. 

Como a empresa prefere usar rotas que exigem um equilíbrio de economia e velocidade, o routetype solicitado é o *eco*. O script a seguir chama a [API obter intervalo de rotas](https://docs.microsoft.com/rest/api/maps/route/getrouterange) do serviço de roteamento do Azure Maps usando parâmetros para o modelo de consumo do veículo. Em seguida, o script analisa a resposta para criar um objeto Polygon do formato geojson, que representa o intervalo máximo de alcançável do carro.

Para determinar os limites do intervalo alcançável do veículo elétrico, execute o script na seguinte célula:

```python
subscriptionKey = "Your Azure Maps key"
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


# Get boundaries for the electric vehicle's reachable range.
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

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Pesquisar estações de carregamento do veículo elétrico dentro do intervalo acessível

Depois de determinar o intervalo alcançável (isochrone) para o veículo elétrico, você pode pesquisar por estações de carregamento dentro desse intervalo. 

O script a seguir chama a [pesquisa de postagem](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)do Azure Maps dentro da API de geometria. Ele pesquisa estações de cobrança de veículo elétrica dentro dos limites do intervalo máximo de alcance do carro e, em seguida, analisa a resposta para uma matriz de locais acessíveis.

Para procurar estações de carregamento do veículo elétrico dentro do intervalo acessível, execute o seguinte script:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Carregar o intervalo acessível e os pontos de cobrança para o serviço de dados do Azure Maps

Convém visualizar em um mapa as estações de cobrança e limites para o intervalo máximo acessível do veículo elétrico. Para fazer isso, carregue os dados de limite e os dados das estações de carregamento como objetos geojson no serviço de dados do Azure Maps usando a [API de carregamento de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Para carregar os dados de limite e de ponto de carregamento para o serviço de dados do Azure Maps, execute as duas células a seguir:

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

# Upload the range data to Azure Maps Data Service.
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

# Upload the electric vehicle charging station data to Azure Maps Data Service.
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

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Renderizar as estações de carregamento e o intervalo alcançável em um mapa

Depois de carregar os dados para o serviço de dados, chame o [serviço obter imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage) do Azure Maps para renderizar os pontos de carregamento e o limite máximo de alcançável na imagem do mapa estático executando o seguinte script:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
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

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Um mapa que mostra o intervalo de localização](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Encontre a estação de cobrança ideal

Depois de determinar todas as possíveis estações de carregamento dentro do intervalo acessível, você deseja saber quais delas podem ser atingidas em um período mínimo de tempo. 

O script a seguir chama a [API de roteamento de matriz](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)do Azure Maps, que retorna para o local do veículo especificado o tempo de viagem e a distância de cada estação de cobrança. O script na próxima célula analisa a resposta para localizar a estação de cobrança acessível mais próxima em relação ao tempo.

Para localizar a estação de cobrança acessível mais próxima que pode ser alcançada na menor quantidade de tempo, execute o script na seguinte célula:

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

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Calcular a rota para a estação de cobrança mais próxima

Agora que você encontrou a estação de cobrança mais próxima, você pode chamar a [API obter direções de rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) para solicitar a rota detalhada do local atual do veículo elétrico para a estação de cobrança.

Para obter a rota para a estação de cobrança e analisar a resposta para criar um objeto geojson que representa a rota, execute o script na seguinte célula:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
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

Para ajudar a visualizar a rota, primeiro carregue os dados de rota como um objeto geojson no serviço de dados do Azure Maps usando a [API de carregamento de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)do Azure Maps. Em seguida, você chama o serviço de renderização, [obter API de mapa de imagem](https://docs.microsoft.com/rest/api/maps/render/getmapimage), para renderizar a rota no mapa e visualizá-la.

Para obter uma imagem para a rota renderizada no mapa, execute o seguinte script:

```python
# Upload the route data to Azure Maps Data Service.
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


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Um mapa que mostra a rota](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a chamar as APIs REST do Azure Maps diretamente e visualizar os dados do Azure Maps usando o Python.

Para explorar as APIs do Azure Maps que são usadas neste tutorial, consulte:

* [Obter intervalo de rotas](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Postar pesquisa dentro da geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Carregamento de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Render-obter imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Lançar matriz de rota](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)
* [Obter direções de rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Para obter uma lista completa de APIs REST do Azure Maps, consulte [APIs REST do Azure Maps](https://docs.microsoft.com/azure/azure-maps/consumption-model).

Para saber mais sobre Azure Notebooks, consulte [Azure notebooks](https://docs.microsoft.com/azure/notebooks).
