---
title: Estruturas de dados do Serviço de Mobilidade no Azure Maps Microsoft Azure Maps
description: Entenda como os dados são organizados em áreas de metro nos serviços de Mobilidade Azure Maps. Veja quais os campos que armazenam informações sobre paragens e linhas de trânsito público.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 02d89226bd2df4bfe5d11897199c50c702e7bc1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88033228"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Estruturas de dados no Azure Maps Mobility Service

Este artigo introduz o conceito de Área metropolitana no Serviço de [Mobilidade Azure Maps.](https://aka.ms/AzureMapsMobilityService) Discutimos alguns dos campos comuns que são devolvidos quando este serviço é questionado para paragens e linhas de trânsito público. Recomendamos a leitura deste artigo antes de desenvolver com as APIs do Serviço de Mobilidade.

## <a name="metro-area"></a>Área metropolitana

Os dados do Serviço de Mobilidade são agrupados por áreas de metro apoiadas. As áreas de metro não seguem os limites da cidade. Uma área metropolitana pode conter várias cidades, cidade densamente povoada e cidades circundantes. Na verdade, um país/região pode ser uma área de metro. 

O `metroID` iD de uma área de metrô que pode ser usado para chamar a [API de Informação da Área do Metro.](https://aka.ms/AzureMapsMobilityMetroAreaInfo) Utilize a API "Get Metro" da Azure Maps para solicitar tipos de trânsito, agências de trânsito, alertas ativos e detalhes adicionais para o metrô escolhido. Você também pode solicitar as áreas de metrô e metroIDs apoiados. As identificações da área do metro estão sujeitas a alterações.

**metroID:** 522   **Nome:** Seattle-Tacoma-Bellevue

![Seattle-metro-área](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Parar iDs

As paragens de trânsito podem ser referidas por dois tipos de IDs, o ID [da Especificação Geral de Alimentação de Trânsito (GFTS)](http://gtfs.org/) e o ID de paragem do Azure Maps. O ID GFTS é referido como o stopKey e o ID de paragem do Azure Maps é referido como stopID. Quando se refere frequentemente às paragens de trânsito, é encorajado a usar o ID de paragem do Azure Maps. o stopID é mais estável e é provável que permaneça o mesmo enquanto a paragem física existir. O ID de paragem GTFS é atualizado com mais frequência. Por exemplo, o ID de paragem GTFS pode ser atualizado de acordo com o pedido do fornecedor GTFS ou quando uma nova versão GTFS é lançada. Embora a paragem física não tenha mudado, o ID de paragem GTFS pode mudar.

Para começar, pode solicitar paragens de trânsito nas proximidades usando [a API de Trânsito Próximo.](https://aka.ms/AzureMapsMobilityNearbyTransit)

## <a name="line-groups-and-lines"></a>Grupos de linha e linhas

O Serviço de Mobilidade utiliza um modelo de dados paralelo para linhas e grupos de linha. Este modelo é utilizado para lidar melhor com as mudanças herdadas das rotas [GTFS](http://gtfs.org/) e os dados das viagens.


### <a name="line-groups"></a>Grupos de Linha

O Grupo A Line é uma entidade, que agrupará todas as linhas que logicamente fazem parte do mesmo grupo. Normalmente, um grupo de linha contém duas linhas, uma do ponto A para o B, e a outra volta do ponto B para A. Ambas as linhas pertencem à mesma agência de transportes públicos e têm o mesmo número de linha. No entanto, pode haver casos em que um grupo de linhas tenha mais de duas linhas ou apenas uma única linha dentro dele.


### <a name="lines"></a>Linhas

Como discutido acima, cada grupo de linha é composto por um conjunto de linhas. Cada grupo de linha é composto por duas linhas, e cada linha descreve uma direção.  No entanto, há casos em que mais linhas compõem um grupo de linha. Por exemplo, há uma linha que às vezes desvia-se por um certo bairro e às vezes não. Em ambos os casos, opera com o mesmo número de linha. Também um grupo de linha pode ser composto por uma única linha. Uma linha circular com uma única direção é um grupo ling com uma linha.

Para começar, pode solicitar grupos de linha utilizando a [API da Linha de Trânsito Get.](https://aka.ms/AzureMapsMobilityTransitLine)


## <a name="next-steps"></a>Passos seguintes

Saiba como solicitar dados de trânsito utilizando o Serviço de Mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Saiba como solicitar dados em tempo real utilizando o Serviço de Mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados em tempo real](how-to-request-real-time-data.md)

Explore a documentação da APC do Serviço de Mobilidade Azure Maps

> [!div class="nextstepaction"]
> [Documentação da API do Serviço de Mobilidade](https://aka.ms/AzureMapsMobilityService)
