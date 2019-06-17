---
title: Estruturas de dados do serviço de mobilidade do Azure Maps | Documentos da Microsoft
description: Estruturas de dados no serviço de mobilidade do Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 29e8a9d7555ca836b6266879f3b3c1e32ffd3980
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735560"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Estruturas de dados no serviço de mobilidade do Azure Maps

Este artigo apresenta o conceito de área Metro [serviço de mobilidade do Azure Maps](https://aka.ms/AzureMapsMobilityService) e alguns dos campos comuns retornados por serviços, quando forem solicitada para trânsito público interrompe e linhas. Recomendamos que passar por este artigo antes de começar a utilizar as APIs de serviço de mobilidade. Discutimos esses campos comuns abaixo.

## <a name="metro-area"></a>Área metro

Dados do serviço de mobilidade são divididos em áreas de metrô suportadas. Áreas de metrô não siga os limites de cidade, uma área metro pode conter várias cidades, por exemplo, cidade densely preenchida e suas cidades ao redor; e um país/região pode ser uma área metro. 

O `metroID` é o ID de uma área metro que pode ser utilizado para chamar o [obter API de informações de área de Metro](https://aka.ms/AzureMapsMobilityMetroAreaInfo) para tipos de trânsito de pedido de suporte e detalhes adicionais para a área metro como agências de trânsito e alertas ativos. Pode utilizar a API do Azure Maps, obtenha e Metro para pedir a áreas de metrô suportadas e metroIDs. IDs de área metro estão sujeitos a alterações.

**metroID:** 522 **nome:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Parar IDs

Paragens de trânsito podem ser referenciadas por dois tipos de IDs, o [especificação geral de Feed de trânsito (GFTS)](https://gtfs.org/) ID (referido como stopKey) e o Azure Maps parar ID (referido como stopId). Quando nos Referimos a paradas ao longo do tempo, recomenda-se para utilizar o ID de paragem de mapas do Azure, como este ID é mais estável e não vai provavelmente mudar, desde que a parada física existe. O ID de paragem GTFS é atualizado com mais freqüência, por exemplo, no caso do fornecedor GTFS tem de alterá-lo ou a nova versão GTFS for lançada, embora a parada física não tivesse nenhuma alteração.

Para começar, pode pedir deixa de trânsito próximos usando [obter próximos API de trânsito](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Grupos de linhas e linhas

O serviço de mobilidade utiliza um modelo de dados paralelos para linhas e herdada de grupos de linha para lidar melhor com as alterações do [GTFS](https://gtfs.org/) rotas e viagens ao modelo de dados.


### <a name="line-groups"></a>Grupos de linha

Um grupo de linha é uma entidade, o que agrupa todas as linhas que são, logicamente, parte do mesmo grupo. Normalmente, um grupo de linha irá conter duas linhas, um vai do ponto A para B e a outros retornando do ponto de B para A, ambos os pertencentes à Agência de transporte público ao mesmo e ter o mesmo número de linha. No entanto, poderá haver casos em que um grupo de linha tem mais de duas linhas ou apenas uma única linha dentro da mesma.


### <a name="lines"></a>linhas

Conforme mencionado acima, cada grupo de linha é composta por um conjunto de linhas. Muitas vezes, cada linha descreve uma direção e cada linha de grupo é composto por duas linhas. No entanto, existem casos em que mais linhas compõem um grupo de linha, por exemplo aqui é uma linha que às vezes, desvios por meio de um determinado ambiente e, às vezes, não e é operado em ambos os casos em que o mesmo número de linha, e existem outros casos em que uma linha g rupo é composta por uma única linha, por exemplo, uma linha circular com uma única direção.

Para começar, pode pedir a grupos de linha utilizando o [obter API de linha de trânsito](https://aka.ms/AzureMapsMobilityTransitLine) e Desagregue posterior para linhas.


## <a name="next-steps"></a>Passos Seguintes

Saiba como os dados de trânsito através do serviço de mobilidade do pedido:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Saiba como os dados em tempo real com o serviço de mobilidade do pedido:

> [!div class="nextstepaction"]
> [Como solicitar dados em tempo real](how-to-request-real-time-data.md)

Explore a documentação da API de serviço de mobilidade do Azure Maps

> [!div class="nextstepaction"]
> [Documentação da API de serviço de mobilidade](https://aka.ms/AzureMapsMobilityService)
