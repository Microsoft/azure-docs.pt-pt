---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: f097479b67a7b48eca4a2710db3bd7eed6ddc982
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013919"
---
O seguinte resume os principais limites na Disponibilidade Geral.

### <a name="sku-ingress-rates-and-capacities"></a>Taxas e capacidades de ingresso SKU

As taxas e capacidades de ingresso S1 e S2 SKU proporcionam flexibilidade ao configurar um novo ambiente time series Insights.

| Capacidade S1 SKU | Taxa de ingresso | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 1 GB (1 milhão de eventos) | 30 GB (30 milhões de eventos) por mês |
| 10 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |

| Capacidade S2 SKU | Taxa de ingresso | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |
| 10 | 100 GB (100 milhões de eventos) | 3 TB (3 mil milhões de eventos) por mês |

> [!NOTE]
> As capacidades escalam linearmente, por isso um S1 SKU com capacidade 2 suporta 2 GB (2 milhões) de eventos por dia e 60 GB (60 milhões de eventos) por mês.

Os ambientes S2 SKU suportam substancialmente mais eventos por mês e têm uma capacidade de ingresso significativamente maior.

| SKU  | Contagem de eventos por mês  | Tamanho do evento por mês  | Contagem de eventos por minuto | Tamanho do evento por minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milhões     |  30 GB     |  720    |  720 KB   |
 |S2     |   300 milhões    |   300 GB   | 7,200   | 7\.200 KB  |

### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade ga dependem do ambiente SKU que é selecionado. As propriedades fornecidas do evento têm correspondentes JSON, CSV e colunas de gráficos que podem ser vistas dentro do [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| S1 | 600 propriedades (colunas) |
| S2 | 800 propriedades (colunas) |

### <a name="event-sources"></a>Origens de eventos

É apoiado um máximo de duas fontes de eventopor instância. 

* Saiba como [Adicionar uma origem do hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configure [uma fonte de hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limites da API

Os limites da API REST para a Time Series Insights Disponibilidade Geral são especificados na documentação de referência rest [API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).