---
title: Estruturas de dados do serviço de mobilidade no Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os campos comuns e estruturas de dados retornados por meio dos serviços de mobilidade de mapas de Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ceecdcc508e5b43c8775b6a88f9b4e4f0eb23c77
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989013"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Estruturas de dados no serviço de mobilidade do Azure Maps

Este artigo introduz o conceito de Área metropolitana no Serviço de [Mobilidade Do Azure Maps.](https://aka.ms/AzureMapsMobilityService) Discutimos alguns dos campos comuns que são devolvidos quando este serviço é questionado sobre paragens e linhas de trânsito público. Recomendamos a leitura deste artigo antes de desenvolver com as APIs do Serviço de Mobilidade.

## <a name="metro-area"></a>Área de metrô

Os dados do Serviço de Mobilidade são agrupados por áreas de metro apoiadas. As áreas metropolitanas não seguem os limites da cidade. Uma área metropolitana pode conter várias cidades, cidades densamente povoadas, e cidades circundantes. Na verdade, um país/região pode ser uma área metropolitana. 

O `metroID` é uma identificação de uma área metropolitana que pode ser usada para chamar a API de Informação da [Área metropolitana.](https://aka.ms/AzureMapsMobilityMetroAreaInfo) Utilize a API "Get Metro" da Azure Maps para solicitar tipos de trânsito, agências de trânsito, alertas ativos e detalhes adicionais para o metrô escolhido. Você também pode solicitar as áreas de metrô e metrô suportados. As IDs de área de metrô estão sujeitas a alterações.

**metroID:** 522 **nome:** Seattle-Tacoma-Bellevue

![Seattle – área de metrô](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Parar IDs

As paragens de trânsito podem ser referidas por dois tipos de IDs, o ID da Especificação Geral de Alimentação de [Trânsito (GFTS)](https://gtfs.org/) e o ID de paragem do Azure Maps. O ID GFTS é referido como o stopKey e o ID de paragem do Azure Maps é referido como stopID. Quando se refere frequentemente às paragens de trânsito, é encorajado a usar o ID de paragem do Azure Maps. stopID é mais estável e provável de permanecer o mesmo enquanto a paragem física existir. O ID de paragem GTFS é atualizado com mais frequência. Por exemplo, o ID de paragem GTFS pode ser atualizado de acordo com o pedido do fornecedor GTFS ou quando uma nova versão GTFS é lançada. Embora a paragem física não tenha mudado, o ID de paragem GTFS pode mudar.

Para começar, pode solicitar paragens de trânsito nas proximidades utilizando a [API](https://aka.ms/AzureMapsMobilityNearbyTransit)de trânsito próximo .

## <a name="line-groups-and-lines"></a>Linhas e grupos de linha

O Serviço de Mobilidade utiliza um modelo paralelo de dados para Linhas e Grupos de Linhas. Este modelo é utilizado para lidar melhor com as alterações herdadas das rotas [GTFS](https://gtfs.org/) e os dados das viagens.


### <a name="line-groups"></a>Grupos de linhas

Um grupo de linhas é uma entidade, que agrupa todas as linhas que são logicamente parte do mesmo grupo. Normalmente, um grupo de linha contém duas linhas, uma do ponto A para B, e a outra volta do ponto B para A. Ambas as linhas pertenceriam à mesma agência de transportes públicos e teriam o mesmo número de linha. No entanto, pode haver casos em que um grupo de linhas tem mais de duas linhas ou apenas uma única linha dentro dela.


### <a name="lines"></a>Linhas

Conforme discutido acima, cada grupo de linhas é composto por um conjunto de linhas. Cada grupo de linha é composto por duas linhas, e cada linha descreve uma direção.  No entanto, há casos em que mais linhas compõem um grupo de linhas. Por exemplo, há uma linha que às vezes desfila por um certo bairro e às vezes não. Em ambos os casos, funciona com o mesmo número de linha. Também um grupo de linha pode ser composto por uma única linha. Uma linha circular com uma única direção é um grupo ling com uma linha.

Para começar, pode solicitar grupos de linha utilizando a [API da Linha de Trânsito Get](https://aka.ms/AzureMapsMobilityTransitLine).


## <a name="next-steps"></a>Passos seguintes

Saiba como solicitar dados de trânsito usando o serviço de mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Saiba como solicitar dados em tempo real usando o serviço de mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados em tempo real](how-to-request-real-time-data.md)

Explore a documentação da API do serviço de mobilidade do Azure Maps

> [!div class="nextstepaction"]
> [Documentação da API do serviço de mobilidade](https://aka.ms/AzureMapsMobilityService)
