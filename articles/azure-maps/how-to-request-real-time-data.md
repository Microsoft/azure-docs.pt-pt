---
title: Solicitar dados de trânsito em tempo real  Microsoft Azure Maps
description: Solicite dados em tempo real utilizando o Serviço de Mobilidade Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 053e6c84f69e8b3d3fed0a90a8b632aa4eb311cb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198161"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Solicite dados em tempo real utilizando o Serviço de Mobilidade Do Azure Maps

Este artigo mostra-lhe como utilizar o Serviço de [Mobilidade](https://aka.ms/AzureMapsMobilityService) Do Azure Maps para solicitar dados de trânsito em tempo real.

Neste artigo aprenderá a:


 * Solicite próximas chegadas em tempo real para todas as linhas que chegam a uma determinada paragem
 * Solicite informações em tempo real para uma determinada estação de ancoragem de bicicletas.


## <a name="prerequisites"></a>Pré-requisitos

Primeiro é necessário ter uma conta Azure Maps e uma chave de subscrição para fazer chamadas para as APIs de trânsito público do Azure Maps. Para obter informações, siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma conta Azure Maps. Siga os passos na [chave principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps](./how-to-manage-authentication.md).


Este artigo usa a [app Postman](https://www.getpostman.com/apps) para construir chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que você preferir.


## <a name="request-real-time-arrivals-for-a-stop"></a>Solicite chegadas em tempo real para uma paragem

Para solicitar dados de chegadas em tempo real de uma determinada paragem de trânsito público, terá de fazer um pedido à API de [Chegadas](https://aka.ms/AzureMapsMobilityRealTimeArrivals) em tempo real do Serviço de [Mobilidade](https://aka.ms/AzureMapsMobilityService)do Azure Maps. Você precisará do **metroID** e **stopID** para completar o pedido. Para saber mais sobre como solicitar estes parâmetros, consulte o nosso guia sobre como solicitar rotas de [trânsito público.](https://aka.ms/AMapsHowToGuidePublicTransitRouting) 

Vamos usar o "522" como identificação do metro, que é o ID do metrô para a área "Seattle-Tacoma-Bellevue, WA". Utilize o "522---2060603" como id de paragem, esta paragem de autocarro é em "Ne 24th St & 162nd Ave Ne, Bellevue WA". Para solicitar os próximos cinco dados de chegadas em tempo real, para todas as próximas chegadas ao vivo nesta paragem, complete os seguintes passos:

1. Abra a aplicação Postman e vamos criar uma coleção para armazenar os pedidos. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.**

2. Para criar o pedido, selecione **Nova** novamente. Na janela **Criar Nova,** selecione **Pedido**. Insira um **nome de Pedido** para o pedido. Selecione a coleção que criou no passo anterior, como a localização para guardar o pedido. Em seguida, selecione **Guardar**.

    ![Criar um pedido no Carteiro](./media/how-to-request-transit-data/postman-new.png)

3. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL para criar um pedido GET. Substitua `{subscription-key}`, com a chave primária do Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Após um pedido bem sucedido, receberá a seguinte resposta.  Note que o parâmetro 'scheduleType' define se a hora estimada de chegada é baseada em dados em tempo real ou estáticos.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
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
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
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
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Dados em tempo real para estação de ancoragem de bicicleta

A [API](https://aka.ms/AzureMapsMobilityTransitDock) Get Transit Dock Info permite que os utilizadores solicitem informações estáticas e em tempo real. Por exemplo, os utilizadores podem solicitar informações de disponibilidade e vaga para uma bicicleta, ou uma estação de ancoragem de scooters. A [API](https://aka.ms/AzureMapsMobilityTransitDock) Get Transit Dock Info também faz parte do Serviço de [Mobilidade](https://aka.ms/AzureMapsMobilityService)Do Azure Maps.

Para fazer um pedido para a API da Doca de [Trânsito,](https://aka.ms/AzureMapsMobilityTransitDock)você precisará do **dockId** para aquela estação. Pode obter o ID da doca fazendo um pedido de pesquisa para a [API](https://aka.ms/AzureMapsMobilityNearbyTransit) de trânsito próximo com o parâmetro **objectType** atribuído a "bikeDock". Siga os passos abaixo para obter dados em tempo real de uma estação de ancoragem para bicicletas.


### <a name="get-dock-id"></a>Obter ID da doca

Para obter **dockID,** siga os passos abaixo para fazer um pedido para a API de trânsito próximo:

1. No Carteiro, clique em **New Request** | **GET request** e nomeie-o Obter ID **da doca**.

2.  No separador Construtor, selecione o método **GET** HTTP, introduza o URL de pedido seguinte e clique em **Enviar**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Após um pedido bem sucedido, receberá a seguinte resposta. Note que agora temos o **id** na resposta, que pode ser usado mais tarde como um parâmetro de consulta no pedido para a API Get Transit Dock Info.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
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


### <a name="get-real-time-bike-dock-status"></a>Obtenha o estatuto de doca de bicicleta em tempo real

Siga os passos abaixo para fazer um pedido à API Get Transit Dock Info para obter dados em tempo real para a doca selecionada.

1. No Carteiro, clique em **New Request** | **GET request** e nomeie-o Obtenha dados **da doca em tempo real**.

2.  No separador Construtor, selecione o método **GET** HTTP, introduza o URL de pedido seguinte e clique em **Enviar**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Após um pedido bem sucedido, receberá uma resposta da seguinte estrutura:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Passos seguintes

Saiba como solicitar dados de trânsito através do Serviço de Mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Explore a documentação da API do Serviço de Mobilidade Do Azure Maps:

> [!div class="nextstepaction"]
> [Documentação da API do Serviço de Mobilidade](https://aka.ms/AzureMapsMobilityService)
