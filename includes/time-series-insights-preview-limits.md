---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91287423"
---
### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade da Azure Time Series Insights aumentaram para 1.000 de um limite máximo de 800 na Gen1. As propriedades fornecidas do evento têm correspondentes colunas JSON, CSV e gráficos que pode ver dentro do [Azure Time Series Insights Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| Gen2 (L1) | 1.000 propriedades (colunas) |
| Gen1 (S1) | 600 propriedades (colunas) |
| Gen1 (S2) | 800 propriedades (colunas) |

### <a name="streaming-ingestion"></a>Ingestão de transmissão em fluxo

* Há um máximo de [duas fontes](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) de evento por ambiente.

* As melhores práticas e orientação geral para fontes de eventos podem ser encontradas [aqui](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Por padrão, a Azure Time Series Insights Gen2 pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (MBps) por Azure Time Series Insights Gen2 ambiente**. Existem limitações adicionais [por partição do hub.](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits) As tarifas até 8 MBps podem ser fornecidas através da submissão de um bilhete de apoio através do portal Azure. Para saber mais, leia [os Limites de Produção de Ingestão de Streaming.](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md)

### <a name="api-limits"></a>Limites da API

Os limites de API para Azure Time Series Insights Gen2 são especificados na [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)da API REST .
