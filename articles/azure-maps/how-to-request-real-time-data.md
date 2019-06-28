---
title: Como solicitar dados em tempo real no Azure Maps | Documentos da Microsoft
description: Solicite dados em tempo real com o serviço de mobilidade do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: aaab5ef4d8fc3d60a12f9e9f85f2846695fd1ab4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329659"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Pedido de dados em tempo real com o serviço de mobilidade do Azure Maps

Este artigo mostra-lhe como utilizar o Azure Maps [serviço de mobilidade](https://aka.ms/AzureMapsMobilityService) para dados de pedidos de trânsito em tempo real.

Neste artigo, ficará a saber como:


 * Pedido seguintes entradas em tempo real para todas as linhas que chegam à determinado parada
 * Solicitar informações em tempo real para uma estação de ancoragem de bicicletas determinado.


## <a name="prerequisites"></a>Pré-requisitos

Para fazer todas as chamadas para o trânsito de público do Azure Maps APIs, terá de uma conta de mapas e a chave. Para obter informações sobre como criar uma conta e obter uma chave, consulte [como gerir a sua conta do Azure Maps e as chaves](how-to-manage-account-keys.md).

Este artigo utiliza a [aplicação Postman](https://www.getpostman.com/apps) para criar as chamadas REST. Pode usar qualquer ambiente de desenvolvimento de API que preferir.


## <a name="request-real-time-arrivals-for-a-stop"></a>Em tempo real chegadas de solicitações para uma parada

Para solicitar dados de entradas em tempo real de uma parada de trânsito pública específica, terá de fazer um pedido para o [API de entradas em tempo real](https://aka.ms/AzureMapsMobilityRealTimeArrivals) do Azure Maps [serviço de mobilidade](https://aka.ms/AzureMapsMobilityService). Terá do **metroID** e **stopID** para concluir o pedido. Para saber mais sobre como pedir esses parâmetros, consulte o nosso guia de procedimentos [rotas de trânsito pública do pedido](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Vamos usar "522" como nosso metro ID, que é o metro parar o ID de área "Seattle – Tacoma – Bellevue, WA" e utilize o ID de stop "2060603", que é um barramento em "Ne 24th St & 162nd Ave Ne, Bellevue WA". Para solicitar próximos cinco de entradas em tempo real de dados para todas as entradas em direto seguintes, neste parar, conclua os seguintes passos:

1. Crie uma coleção para armazenar os pedidos. Na aplicação do Postman, selecione **New**. Na **criar novo** janela, selecione **coleção**. Nome da coleção e selecione o **criar** botão.

2. Para criar o pedido, selecione **New** novamente. Na **criar novo** janela, selecione **pedir**. Introduza um **nome do pedido** para o pedido, selecione a coleção que criou no passo anterior, como a localização na qual pretende guardar o pedido e, em seguida, selecione **guardar**.

    ![Criar um pedido no Postman](./media/how-to-request-transit-data/postman-new.png)

3. Selecione o método GET HTTP na guia builder e introduza o URL seguinte para criar um pedido GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. Após um pedido com êxito, receberá a seguinte resposta.  Observe que esse parâmetro 'scheduleType' define se o tempo de chegada estimado baseia-se nos dados em tempo real ou estáticos.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Dados em tempo real para a estação de ancoragem de bicicletas

O [obter API de informações de estação de ancoragem de trânsito](https://aka.ms/AzureMapsMobilityTransitDock) do serviço de mobilidade do Azure Maps, permite para pedir informações estáticas e em tempo real, como a disponibilidade e informações de vago para uma determinada bicicletas ou scooter estação de ancoragem. Faremos um pedido para obter dados em tempo real para uma estação de ancoragem para bicicletas.

Para fazer um pedido para a API informações de introdução de estação de ancoragem de trânsito, é necessário o **dockId** dessa estação. Pode obter o ID de estação de ancoragem fazendo uma solicitação de pesquisa para o [obter próximos API de trânsito](https://aka.ms/AzureMapsMobilityNearbyTransit) e ao definir o **objectType** parâmetro para "bikeDock". Siga os passos abaixo para obter dados em tempo real de uma estação de ancoragem para bicicletas.


### <a name="get-dock-id"></a>Obter ID da estação de ancoragem

Para obter **dockID**, siga os passos abaixo para fazer um pedido para a API para obter próximos trânsito:

1. No Postman, clique em **nova solicitação** | **pedido GET** e nomeie- **Get ID da estação de ancoragem**.

2.  No separador Builder, selecione o **Obtenha** método HTTP, introduza o seguinte URL de pedido e clique em **enviar**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Após um pedido com êxito, receberá a seguinte resposta. Tenha em atenção que agora temos o **id** na resposta, que pode ser utilizado mais tarde como um parâmetro de consulta no pedido para a API informações de introdução de estação de ancoragem de trânsito.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Obter o estado da estação de ancoragem de bicicletas em tempo real

Siga os passos abaixo para fazer um pedido para a obter trânsito ancoragem de informações de API para obter dados em tempo real para a estação de ancoragem selecionada.

1. No Postman, clique em **nova solicitação** | **pedido GET** e nomeie- **obter dados em tempo real de estação de ancoragem**.

2.  No separador Builder, selecione o **Obtenha** método HTTP, introduza o seguinte URL de pedido e clique em **enviar**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Após um pedido com êxito, receberá uma resposta da seguinte estrutura:

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Passos Seguintes

Saiba como os dados de trânsito através do serviço de mobilidade do pedido:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Explore a documentação da API de serviço de mobilidade do Azure Maps:

> [!div class="nextstepaction"]
> [Documentação da API de serviço de mobilidade](https://aka.ms/AzureMapsMobilityService)
