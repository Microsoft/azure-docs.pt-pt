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
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263353"
---
O seguinte resume os principais limites na Disponibilidade Geral.

### <a name="sku-ingress-rates-and-capacities"></a>Taxas e capacidades de ingresso SKU

As taxas e capacidades de ingresso S1 e S2 SKU proporcionam flexibilidade ao configurar um novo ambiente time series Insights. A sua capacidade SKU indica a sua diária taxa de ingresso com base no número de eventos ou bytes armazenados, o que vier em primeiro lugar. Note que a entrada é medida *por minuto*, e o **estrangulamento** é aplicado usando o algoritmo do balde simbólico. Ingress é medido em blocos de 1 KB. Por exemplo, um evento real de 0,8 KB seria medido como um evento, e um evento de 2,6 KB é contado como três eventos.

| Capacidade S1 SKU | Taxa de ingresso | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 1 GB (1 milhão de eventos) por dia | 30 GB (30 milhões de eventos) por mês |
| 10 | 10 GB (10 milhões de eventos) por dia | 300 GB (300 milhões de eventos) por mês |

| Capacidade S2 SKU | Taxa de ingresso | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 10 GB (10 milhões de eventos) por dia | 300 GB (300 milhões de eventos) por mês |
| 10 | 100 GB (100 milhões de eventos) por dia | 3 TB (3 mil milhões de eventos) por mês |

> [!NOTE]
> As capacidades escalam linearmente, por isso um S1 SKU com capacidade 2 suporta 2 GB (2 milhões) de eventos por dia e 60 GB (60 milhões de eventos) por mês.

Os ambientes S2 SKU suportam substancialmente mais eventos por mês e têm uma capacidade de ingresso significativamente maior.

| SKU  | Contagem de eventos por mês  | Contagem de eventos por minuto | Tamanho do evento por minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milhões   |  720    |  720 KB   |
 |S2     |   300 milhões   | 7,200   | 7.200 KB  |

### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade ga dependem do ambiente SKU que é selecionado. As propriedades fornecidas do evento têm correspondentes JSON, CSV e colunas de gráficos que podem ser vistas dentro do [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| S1 | 600 propriedades (colunas) |
| S2 | 800 propriedades (colunas) |

### <a name="event-sources"></a>Origens de eventos

É apoiado um máximo de duas fontes de eventopor instância. 

* Saiba como [adicionar uma fonte](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)de hub de eventos.
* Configure [uma fonte de hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limites da API

Os limites da API REST para a Time Series Insights Disponibilidade Geral são especificados na documentação de referência rest [API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).