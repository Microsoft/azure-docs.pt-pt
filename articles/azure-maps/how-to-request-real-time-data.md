---
title: Como solicitar dados em tempo real no Azure Maps | Microsoft Docs
description: Solicite dados em tempo real usando o serviço de mobilidade do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4c53d1c1ffbc80e694a9a7b423b2aaf9c6d38b48
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914386"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Solicitar dados em tempo real usando o serviço de mobilidade do Azure Maps

Este artigo mostra como usar o [serviço de mobilidade](https://aka.ms/AzureMapsMobilityService) do Azure Maps para solicitar dados de trânsito em tempo real.

Neste artigo, você aprenderá a:


 * Solicitar as próximas entradas em tempo real para todas as linhas que chegam na parada determinada
 * Solicitar informações em tempo real para uma determinada estação de encaixe de bicicletas.


## <a name="prerequisites"></a>Pré-requisitos

Para fazer chamadas para as APIs de trânsito público do Azure Maps, você precisa de uma conta e chave de mapas. Para obter informações sobre como criar uma conta, siga as instruções em [gerenciar conta](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) e siga as etapas em [obter chave primária](./tutorial-search-location.md#getkey) para recuperar uma chave de assinatura primária para sua conta.

Este artigo usa o [aplicativo de postmaster](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que preferir.


## <a name="request-real-time-arrivals-for-a-stop"></a>Solicitar entradas em tempo real para uma parada

Para solicitar dados de chegada em tempo real para uma parada de trânsito público em particular, você precisará fazer uma solicitação para a [API de entradas em tempo real](https://aka.ms/AzureMapsMobilityRealTimeArrivals) do [serviço de mobilidade](https://aka.ms/AzureMapsMobilityService)do Azure Maps. Você precisará de **metroID** e **StopId** para concluir a solicitação. Para saber mais sobre como solicitar esses parâmetros, consulte nosso guia de instruções para [solicitar rotas de trânsito públicas](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Vamos usar "522" como nossa ID metro, que é a ID de metro para a área "Seattle – Tacoma – Bellevue, WA", e usar a ID de parada "522---2060603", que é uma parada de barramento em "ne 24 St & 162nd ave ne, Bellevue WA". Para solicitar os próximos cinco dados de chegada em tempo real para todas as próximas entradas ao vivo nesta interrupção, conclua as seguintes etapas:

1. Crie uma coleção na qual armazenar as solicitações. No aplicativo de postmaster, selecione **novo**. Na janela **criar nova** , selecione **coleção**. Nomeie a coleção e selecione o botão **criar** .

2. Para criar a solicitação, selecione **novo** novamente. Na janela **criar nova** , selecione **solicitação**. Insira um **nome de solicitação** para a solicitação, selecione a coleção que você criou na etapa anterior como o local no qual salvar a solicitação e, em seguida, selecione **salvar**.

    ![Criar uma solicitação no postmaster](./media/how-to-request-transit-data/postman-new.png)

3. Selecione o método HTTP GET na guia Construtor e insira a URL a seguir para criar uma solicitação GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Após uma solicitação bem-sucedida, você receberá a resposta a seguir.  Observe que o parâmetro ' ScheduleType ' define se o tempo de chegada estimado é baseado em dados em tempo real ou estáticos.

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


## <a name="real-time-data-for-bike-docking-station"></a>Dados em tempo real para estação de encaixe de bicicletas

A [API obter informações de encaixe de trânsito](https://aka.ms/AzureMapsMobilityTransitDock) do serviço de mobilidade do Azure Maps permite solicitar informações estáticas e em tempo real, como informações de disponibilidade e vagas para uma estação de encaixe determinada ou motocicleta. Vamos fazer uma solicitação para obter dados em tempo real para uma estação de encaixe para bicicletas.

Para fazer uma solicitação para a API obter informações de encaixe de trânsito, você precisará do **dockid** para essa estação. Você pode obter a ID de encaixe fazendo uma solicitação de pesquisa para a [API obter tráfego próximo](https://aka.ms/AzureMapsMobilityNearbyTransit) e definindo o parâmetro **objecttype** como "bikeDock". Siga as etapas abaixo para obter dados em tempo real de uma estação de encaixe para bicicletas.


### <a name="get-dock-id"></a>Obter ID do Dock

Para obter **dockid**, siga as etapas abaixo para fazer uma solicitação para a API Get de trânsito próximo:

1. No postmaster, clique em **nova solicitação** | **obter solicitação** e nomeie-a como **obter ID de encaixe**.

2.  Na guia Construtor, selecione o método http **Get** , insira a URL de solicitação a seguir e clique em **Enviar**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Após uma solicitação bem-sucedida, você receberá a resposta a seguir. Observe que agora temos a **ID** na resposta, que pode ser usada posteriormente como um parâmetro de consulta na solicitação para a API obter informações de encaixe de trânsito.

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


### <a name="get-real-time-bike-dock-status"></a>Obter status de encaixe de bicicletas em tempo real

Siga as etapas abaixo para fazer uma solicitação para a API obter informações de encaixe de trânsito para obter dados em tempo real para o Dock selecionado.

1. No postmaster, clique em **nova solicitação** | **obter solicitação** e nomeie-a como **obter dados de encaixe em tempo real**.

2.  Na guia Construtor, selecione o método http **Get** , insira a URL de solicitação a seguir e clique em **Enviar**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Após uma solicitação bem-sucedida, você receberá uma resposta da seguinte estrutura:

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


## <a name="next-steps"></a>Passos Seguintes

Saiba como solicitar dados de trânsito usando o serviço de mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Explore a documentação da API do serviço de mobilidade do Azure Maps:

> [!div class="nextstepaction"]
> [Documentação da API do serviço de mobilidade](https://aka.ms/AzureMapsMobilityService)
