---
title: Solicite dados de trânsito público em tempo real com os serviços de mobilidade do Microsoft Azure Maps (Pré-visualização)
description: Saiba como solicitar dados de trânsito público em tempo real, como chegadas a uma paragem de trânsito. Veja como utilizar os serviços de Mobilidade Azure Maps (Pré-visualização) para este fim.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3e3dc4b0e3bc64a38856da8344583b744ea62b6
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906051"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>Solicite dados de trânsito público em tempo real utilizando os serviços de Mobilidade Azure Maps (Pré-visualização) 

> [!IMPORTANT]
> Os serviços de mobilidade Azure Maps estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este artigo mostra-lhe como utilizar [os serviços de Mobilidade](/rest/api/maps/mobility) Azure Maps para solicitar dados de trânsito público em tempo real.

Neste artigo, você vai aprender a solicitar as próximas chegadas em tempo real para todas as linhas que chegam a uma determinada paragem

## <a name="prerequisites"></a>Pré-requisitos

Primeiro precisa de ter uma conta Azure Maps e uma chave de subscrição para fazer quaisquer chamadas para as APIs de trânsito público do Azure Maps. Para obter informações, siga as instruções na [Criar uma conta](quick-demo-map-app.md#create-an-azure-maps-account) para criar uma conta Azure Maps. Siga os passos na [chave principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave principal para a sua conta. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](./how-to-manage-authentication.md)

Este artigo utiliza a [aplicação Do Carteiro](https://www.getpostman.com/apps) para construir chamadas REST. Você pode usar qualquer ambiente de desenvolvimento da API que você preferir.

## <a name="request-real-time-arrivals-for-a-stop"></a>Solicite chegadas em tempo real para uma paragem

Para solicitar dados de chegadas em tempo real de uma determinada paragem de trânsito público, terá de fazer um pedido à API de [Chegadas em Tempo Real](/rest/api/maps/mobility/getrealtimearrivalspreview) do Serviço de Mobilidade Azure Maps [(Pré-visualização)](/rest/api/maps/mobility). Você precisará do **metroID** e **stopID** para completar o pedido. Para saber mais sobre como solicitar estes parâmetros, consulte o nosso guia sobre como solicitar rotas de [trânsito público.](./how-to-request-transit-data.md)

Vamos usar o "522" como o nosso ID do metro, que é a identificação do metrô para a área "Seattle-Tacoma-Bellevue, WA". Use "522---2060603" como o ID de paragem, esta paragem de autocarro fica na "Ne 24th St & 162nd Ave Ne, Bellevue WA". Para solicitar os próximos cinco dados de chegadas em tempo real, para todas as próximas chegadas ao vivo nesta paragem, complete os seguintes passos:

1. Abra a aplicação Do Carteiro e vamos criar uma coleção para armazenar os pedidos. Perto do topo da aplicação Postman, selecione **New**. Na janela **Criar Nova,** selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar.**

2. Para criar o pedido, selecione **New** novamente. Na janela **Criar Novo,** selecione **Request**. Insira um **nome de Pedido** para o pedido. Selecione a coleção que criou no passo anterior, como o local para guardar o pedido. Em seguida, **selecione Save**.

    ![Criar um pedido no Carteiro](./media/how-to-request-transit-data/postman-new.png)

3. Selecione o método **GET** HTTP no separador construtor e introduza o seguinte URL para criar um pedido GET. `{subscription-key}`Substitua- se por a sua tecla primária Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Após um pedido bem sucedido, receberá a seguinte resposta.  Note que o parâmetro 'scheduleType' define se a hora estimada de chegada se baseia em dados em tempo real ou estáticos.

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

## <a name="next-steps"></a>Passos seguintes

Saiba como solicitar dados de trânsito utilizando serviços de mobilidade (Pré-visualização):

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Explore a documentação da API dos serviços de mobilidade Azure Maps (Pré-visualização):

> [!div class="nextstepaction"]
> [Documentação da API dos serviços de mobilidade](/rest/api/maps/mobility)