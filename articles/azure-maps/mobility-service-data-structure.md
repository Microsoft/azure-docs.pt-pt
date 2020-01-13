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
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910728"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Estruturas de dados no serviço de mobilidade do Azure Maps

Este artigo apresenta o conceito de área de metrô no [serviço de mobilidade do Azure Maps](https://aka.ms/AzureMapsMobilityService) e alguns dos campos comuns retornados por meio dos serviços, quando ele é consultado para interrupções e linhas de trânsito públicos. É recomendável passar por este artigo antes de começar com as APIs do serviço de mobilidade. Discutimos esses campos comuns abaixo.

## <a name="metro-area"></a>Área de metrô

Os dados do serviço de mobilidade são divididos em áreas metropolitanas com suporte. As áreas de metrô não seguem os limites de cidade, uma área de metrô pode conter várias cidades, por exemplo, uma cidade com alta densidade e suas cidades ao seu redor; e um país/região pode ser uma área de metrô. 

O `metroID` é uma ID da área de metrô que pode ser usada para chamar a [API obter informações da área de metrô](https://aka.ms/AzureMapsMobilityMetroAreaInfo) para solicitar tipos de trânsito com suporte e detalhes adicionais para a área de metrô, como agências de trânsito e alertas ativos. Você pode usar a API Get metro do Azure Maps para solicitar as áreas de metrô e metroIDs com suporte. As IDs de área de metrô estão sujeitas a alterações.

**metroID:** 522 **nome:** Seattle-Tacoma-Bellevue

![Seattle – área de metrô](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Parar IDs

As interrupções de trânsito podem ser referenciadas por dois tipos de IDs, a ID de [GFTS (especificação de feed de trânsito geral)](https://gtfs.org/) (conhecida como stopKey) e a ID de parada do Azure Maps (conhecida como StopId). Quando se refere a interrupções ao longo do tempo, é recomendável usar a ID de parada do Azure Maps, pois essa ID é mais estável e provavelmente não será alterada desde que a parada física exista. A ID de parada GTFS é atualizada com mais frequência, por exemplo, caso o provedor GTFS precise alterá-la ou a nova versão do GTFS seja liberada, embora a parada física não tenha nenhuma alteração.

Para começar, você pode solicitar interrupções de trânsito próximas usando [obter API de trânsito próximo](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Linhas e grupos de linha

O serviço de mobilidade usa um modelo de dados paralelo para linhas e grupos de linhas para lidar melhor com as alterações herdadas do modelo de dados de rotas e viagens de [GTFS](https://gtfs.org/) .


### <a name="line-groups"></a>Grupos de linhas

Um grupo de linhas é uma entidade, que agrupa todas as linhas que são logicamente parte do mesmo grupo. Normalmente, um grupo de linhas conterá duas linhas, uma indo de ponto a a B e a outra retornando do ponto B para a, ambas pertencentes à mesma agência de transporte pública e que têm o mesmo número de linha. No entanto, pode haver casos em que um grupo de linhas tem mais de duas linhas ou apenas uma única linha dentro dela.


### <a name="lines"></a>Linhas

Conforme discutido acima, cada grupo de linhas é composto por um conjunto de linhas. Geralmente, cada linha descreve uma direção e cada grupo de linhas é composto de duas linhas. No entanto, há casos em que mais linhas compõem um grupo de linhas, por exemplo, há uma linha que às vezes faz um desvio por uma determinada vizinhança e, às vezes, não é operada em ambos os casos sob o mesmo número de linha e há outros casos em que uma linha g rupar é composto de uma única linha, por exemplo, uma linha circular com uma única direção.

Para começar, você pode solicitar grupos de linhas usando a [API obter linha de trânsito](https://aka.ms/AzureMapsMobilityTransitLine) e, posteriormente, fazer uma busca detalhada em linhas.


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
