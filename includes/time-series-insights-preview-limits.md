---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 016ad0e11f3378dba887e0a235f235fa91e3aa03
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109543"
---
### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade da Azure Time Series Insights aumentaram para 1.000 para armazenamento quente e nenhum limite de propriedade para armazenamento frio. As propriedades fornecidas do evento têm correspondentes colunas JSON, CSV e gráficos que pode ver dentro do [Azure Time Series Insights Gen2 Explorer](../articles/time-series-insights/quickstart-explore-tsi.md).

| SKU | Propriedades máximas |
| --- | --- |
| Gen2 (L1) | 1.000 propriedades (colunas) para armazenamento quente e ilimitadas para armazenamento frio|
| Gen1 (S1) | 600 propriedades (colunas) |
| Gen1 (S2) | 800 propriedades (colunas) |

### <a name="streaming-ingestion"></a>Ingestão de transmissão em fluxo

* Há um máximo de [duas fontes](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) de evento por ambiente.

* As melhores práticas e orientação geral para fontes de eventos podem ser encontradas [aqui](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Por padrão, a Azure Time Series Insights Gen2 pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (MBps) por Azure Time Series Insights Gen2 ambiente**. Existem limitações adicionais [por partição do hub.](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits) As tarifas até 2 MBps podem ser fornecidas através da submissão de um bilhete de apoio através do portal Azure. Para saber mais, leia [os Limites de Produção de Ingestão de Streaming.](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)

### <a name="api-limits"></a>Limites da API

Os limites de API para Azure Time Series Insights Gen2 são especificados na [documentação de referência](/rest/api/time-series-insights/preview#limits-1)da API REST .