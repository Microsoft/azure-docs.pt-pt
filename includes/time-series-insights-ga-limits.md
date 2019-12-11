---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 12/06/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 54796f0c0810afd5559af836c566bf66b430223f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981401"
---
O a seguir resume os principais limites em disponibilidade geral.

### <a name="sku-ingress-rates-and-capacities"></a>Taxas e capacidades de entrada do SKU

As taxas e as capacidades de entrada de SKU S1 e S2 fornecem flexibilidade ao configurar um novo ambiente de Time Series Insights.

| Capacidade de SKU S1 | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 1 GB (1 milhão eventos) | 30 GB (30 milhões eventos) por mês |
| 10 | 10 GB (10 milhões eventos) | 300 GB (300 milhões eventos) por mês |

| Capacidade de SKU S2 | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 10 GB (10 milhões eventos) | 300 GB (300 milhões eventos) por mês |
| 10 | 100 GB (100 milhões eventos) | 3 TB (3.000.000.000 eventos) por mês |

> [!NOTE]
> As capacidades são dimensionadas linearmente, de modo que uma SKU S1 com capacidade 2 dá suporte a 2 GB (2 milhões) de eventos por dia e 60 GB (eventos 60 milhões) por mês.

Os ambientes de SKU S2 dão suporte a substancialmente mais eventos por mês e têm uma capacidade de entrada significativamente mais alta.

| SKU  | Contagem de eventos por mês  | Tamanho do evento por mês  | Contagem de eventos por minuto | Tamanho do evento por minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milhões     |  30 GB     |  720    |  720 KB   |
 |S2     |   300 milhões    |   300 GB   | 7\.200   | 7\.200 KB  |

### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade de GA dependem do ambiente de SKU selecionado. As propriedades de evento fornecidas têm as colunas JSON, CSV e de gráfico correspondentes que podem ser exibidas no [Gerenciador de time Series insights](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| S1 | Propriedades de 600 (colunas) |
| S2 | Propriedades de 800 (colunas) |

### <a name="event-sources"></a>Origens de eventos

Há suporte para no máximo duas origens de evento por instância. 

* Saiba como [Adicionar uma origem do hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configurar [uma origem do Hub IOT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limites de API

Os limites da API REST para Time Series Insights disponibilidade geral são especificados na [documentação de referência da API REST](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).