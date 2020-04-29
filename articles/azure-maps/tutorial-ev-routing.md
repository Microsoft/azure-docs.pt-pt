---
title: 'Tutorial: Rota dos veículos elétricos utilizando cadernos Azure (Python) [ Microsoft Azure Maps'
description: Encaminhe veículos elétricos utilizando apis de encaminhamento do Microsoft Azure Maps e cadernos Azure.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3118ca39ec0efd42c9f7b622c91f857034ef4b03
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80333824"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Tutorial: Rota dos veículos elétricos utilizando cadernos Azure (Python)

O Azure Maps é um portfólio de APIs de serviço geoespacial que está integrado de forma nativa no Azure. Estas APIs permitem que desenvolvedores, empresas e ISVs desenvolvam aplicações conscientes da localização, IoT, mobilidade, logística e soluções de rastreio de ativos. 

As APIs DE REPOUSO Do Azure Maps podem ser chamadas a partir de línguas como Python e R para permitir a análise de dados geoespaciais e cenários de aprendizagem automática. O Azure Maps oferece um conjunto robusto de APIs de [encaminhamento](https://docs.microsoft.com/rest/api/maps/route) que permite aos utilizadores calcular rotas entre vários pontos de dados. Os cálculos baseiam-se em várias condições, tais como o tipo de veículo ou a área alcançável. 

Neste tutorial, você anda ajuda um condutor cuja bateria de veículo elétrico é baixa. O condutor precisa de encontrar o posto de carregamento mais próximo possível da localização do veículo.

Neste tutorial, irá:

> [!div class="checklist"]
> * Crie e execute um caderno Jupyter em [Cadernos Azure](https://docs.microsoft.com/azure/notebooks) na nuvem.
> * Ligue para o Azure Maps REST APIs em Python.
> * Procure uma gama acessível com base no modelo de consumo do veículo elétrico.
> * Procure postos de carregamento de veículos elétricos dentro do alcance acessível, ou isocronona.
> * Torne os postos de limite de alcance e carregamento acessíveis num mapa.
> * Encontre e visualize uma rota para a estação de carregamento de veículos elétricos mais próxima com base no tempo de condução.


## <a name="prerequisites"></a>Pré-requisitos 

Para completar este tutorial, primeiro é necessário criar uma conta Azure Maps e obter a sua chave principal (chave de subscrição). 

Para criar uma subscrição de conta Azure Maps, siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps). Precisa de uma subscrição de conta Azure Maps com o nível de preço S1. 

Para obter a chave de subscrição primária para a sua conta, siga as instruções para obter a [chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebook"></a>Criar um caderno Azure

Para acompanhar este tutorial, você precisa criar um projeto de portátil Azure e baixar e executar o arquivo de portátil Jupyter. O ficheiro do portátil contém código Python, que implementa o cenário neste tutorial. Para criar um projeto de caderno Azure e enviar-lhe o documento do caderno Jupyter, faça os seguintes passos:

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. Para mais informações, consulte [Quickstart: Inicie e detete um ID do utilizador](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
1. No topo da sua página de perfil público, selecione **My Projects**.

    ![O botão My Projects](./media/tutorial-ev-routing/myproject.png)

1. Na página **My Projects,** selecione **New Project**.
 
   ![O botão New Project](./media/tutorial-ev-routing/create-project.png)

1. No painel **Create New Project,** insira um nome de projeto e id do projeto.
 
    ![O painel criar novo projeto](./media/tutorial-ev-routing/create-project-window.png)

1. Selecione **Criar**.

1. Depois de criado o seu projeto, descarregue este ficheiro de documentos do [portátil Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) do [repositório de cadernos Azure Maps Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. Na lista de projetos na página **My Projects,** selecione o seu projeto e, em seguida, selecione **Upload** para carregar o ficheiro de documentos do portátil Jupyter. 

    ![caderno de upload](./media/tutorial-ev-routing/upload-notebook.png)

1. Faça upload do ficheiro a partir do seu computador e, em seguida, selecione **Done**.

1. Depois de o upload ter terminado com sucesso, o seu ficheiro é apresentado na sua página do projeto. Clique duas vezes no ficheiro para o abrir como um caderno Jupyter.

Tente entender a funcionalidade implementada no ficheiro do caderno. Executa o código, no ficheiro do caderno, uma célula de cada vez. Pode executar o código em cada célula selecionando o botão **Executar** na parte superior da aplicação portátil.

  ![O botão Executar](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Instalar pacotes de nível de projeto

Para executar o código no caderno, instale pacotes ao nível do projeto fazendo os seguintes passos:

1. Faça o download do ficheiro [*requisitos.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) do [repositório de cadernos Azure Maps Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)e, em seguida, faça o upload para o seu projeto.
1. No painel de instrumentos do projeto, selecione **Definições de Projeto**. 
1. No painel **definições** do projeto, selecione o separador **Ambiente** e, em seguida, selecione **Adicionar**.
1. Em etapas de **configuração ambiente,** faça o seguinte:   
    a. Na primeira lista de entregas, selecione **Requirements.txt**.  
    b. Na segunda lista de entrega, selecione o ficheiro *requisitos.txt.*  
    c. Na terceira lista de descidas, selecione **Python Version 3.6** como versão.
1. Selecione **Guardar**.

    ![Instalar pacotes](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Carregue os módulos e quadros necessários

Para carregar todos os módulos e quadros necessários, execute o seguinte script.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Solicitar o limite de alcance acessível

Uma empresa de entrega de pacotes tem alguns veículos elétricos na sua frota. Durante o dia, os veículos elétricos precisam de ser recarregados sem terem de regressar ao armazém. Sempre que a carga restante cai para menos de uma hora, procura um conjunto de postos de carregamento que estejam dentro de um alcance acessível. Essencialmente, procure um posto de carregamento quando a bateria estiver com pouca carga. E obtém-se a informação de fronteira para essa gama de postos de carregamento. 

Como a empresa prefere utilizar rotas que exijam um equilíbrio económico e de velocidade, o routeType solicitado é *eco*. O seguinte script chama a [Gama Get Route API](https://docs.microsoft.com/rest/api/maps/route/getrouterange) do serviço de encaminhamento Do Azure Maps. Utiliza parâmetros para o modelo de consumo do veículo. O script analisa então a resposta para criar um objeto de polígono do formato geojson, que representa a gama máxima acessível do carro.

Para determinar os limites para a gama alcançável do veículo elétrico, execute o script na seguinte célula:

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

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Pesquisa de postos de carregamento de veículos elétricos dentro da gama alcançável

Depois de determinar a gama alcançável (isochrone) para o veículo elétrico, pode procurar postos de carregamento dentro dessa gama. 

O seguinte script chama o Azure Maps [Post Search Inside Geometry API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry). Procura postos de carregamento para veículos elétricos, dentro dos limites do alcance máximo de alcance do carro. Em seguida, o script analisa a resposta a uma variedade de locais acessíveis.

Para procurar postos de carregamento de veículos elétricos dentro da gama alcançável, execute o seguinte script:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Faça upload da gama e pontos de carregamento acessíveis para o Serviço de Dados do Azure Maps

Num mapa, você vai querer visualizar os postos de carregamento e o limite para a gama máxima acessível do veículo elétrico. Para tal, faça o upload dos dados de fronteira e dos postos de carregamento como objetos geojson para o Serviço de Dados do Mapa Do Azure. Utilize a [API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)de upload de dados . 

Para fazer o upload dos dados de pontos de fronteira e de carregamento para o Serviço de Dados do Azure Maps, execute as seguintes duas células:

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

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Tornar as estações de carregamento e alcance acessível num mapa

Depois de ter enviado os dados para o serviço de dados, ligue para o [serviço](https://docs.microsoft.com/rest/api/maps/render/getmapimage)De imagem do Mapa do Mapa do Azure Maps . Este serviço é utilizado para tornar os pontos de carregamento e o limite máximo alcançável na imagem do mapa estático executando o seguinte script:

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

![Um mapa que mostra a gama de localização](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Encontre a estação de carregamento ideal

Em primeiro lugar, pretende determinar todas as potenciais estações de carregamento dentro do alcance acessível. Então, quer saber qual deles pode ser alcançado num período mínimo de tempo. 

O seguinte guião chama a [API de Encaminhamento de Matriz](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)de Mapas Azure . Devolve a localização especificada do veículo, o tempo de viagem e a distância a cada estação de carregamento. O script na célula seguinte analisa a resposta para localizar a estação de carregamento mais próxima possível no que diz respeito ao tempo.

Para encontrar a estação de carregamento mais próxima e acessível que pode ser alcançada no menor período de tempo, faça o script na seguinte célula:

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

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Calcular a rota para a estação de carregamento mais próxima

Agora que encontrou a estação de carregamento mais próxima, pode ligar para a API Get [Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) para solicitar a rota detalhada da localização atual do veículo elétrico para a estação de carregamento.

Para obter o percurso até à estação de carregamento e analisar a resposta para criar um objeto geojson que represente a rota, executar o script na seguinte célula:

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

Para ajudar a visualizar a rota, primeiro faça o upload dos dados da rota como um objeto geojson para o Serviço de Dados do Azure Maps. Para tal, utilize a [API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)de upload de dados do Azure Maps . Em seguida, ligue para o serviço de renderização, [Obtenha API de Imagem de Mapa,](https://docs.microsoft.com/rest/api/maps/render/getmapimage)para tornar a rota no mapa, e visualizá-la.

Para obter uma imagem para a rota renderizada no mapa, executar o seguinte script:

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

![Um mapa mostrando a rota](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar diretamente para as APIs do Azure Maps e visualizar os dados do Azure Maps utilizando python.

Para explorar as APIs do Azure Maps que são usadas neste tutorial, consulte:

* [Obtenha alcance de rota](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Pós-Pesquisa Dentro da Geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Upload de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Render - Obter Imagem do Mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Matriz de pós-rota](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)
* [Obtenha direções de rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Para obter uma lista completa de APIs REST DO Azure Maps, consulte [O PDP](https://docs.microsoft.com/azure/azure-maps/consumption-model)REST Do Azure Maps .

Para saber mais sobre os Cadernos Azure, consulte [os Cadernos Azure.](https://docs.microsoft.com/azure/notebooks)
