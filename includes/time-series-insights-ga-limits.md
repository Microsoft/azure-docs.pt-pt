---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81263353"
---
O seguinte resume os limites-chave na Disponibilidade Geral.

### <a name="sku-ingress-rates-and-capacities"></a>Taxas e capacidades de entrada de SKU

As taxas e capacidades de entrada S1 e S2 SKU proporcionam flexibilidade ao configurar um novo ambiente de Insights de Séries Temporinhas. A sua capacidade SKU indica a sua taxa diária de entrada com base no número de eventos ou bytes armazenados, o que vier primeiro. Note que a entrada é medida *por minuto,* e **o estrangulamento** é aplicado usando o algoritmo do balde simbólico. A entrada é medida em blocos de 1-KB. Por exemplo, um evento real de 0,8 KB seria medido como um evento, e um evento de 2,6 KB é contado como três eventos.

| Capacidade SKU S1 | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 1 GB (1 milhão de eventos) por dia | 30 GB (30 milhões de eventos) por mês |
| 10 | 10 GB (10 milhões de eventos) por dia | 300 GB (300 milhões de eventos) por mês |

| Capacidade S2 SKU | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 10 GB (10 milhões de eventos) por dia | 300 GB (300 milhões de eventos) por mês |
| 10 | 100 GB (100 milhões de eventos) por dia | 3 TB (3 mil milhões de eventos) por mês |

> [!NOTE]
> As capacidades escalam linearmente, pelo que um SKU S1 com capacidade 2 suporta 2 GB (2 milhões) de taxa de entrada por dia e 60 GB (60 milhões de eventos) por mês.

Os ambientes S2 SKU suportam substancialmente mais eventos por mês e têm uma capacidade de entrada significativamente maior.

| SKU  | Contagem de eventos por mês  | Contagem de eventos por minuto | Tamanho do evento por minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milhões   |  720    |  720 KB   |
 |S2     |   300 milhões   | 7,200   | 7.200 KB  |

### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade da GA dependem do ambiente SKU que é selecionado. As propriedades fornecidas do evento têm correspondentes colunas JSON, CSV e gráficos que podem ser visualizadas dentro do [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| S1 | 600 propriedades (colunas) |
| S2 | 800 propriedades (colunas) |

### <a name="event-sources"></a>Origens de eventos

É suportado um máximo de duas fontes de evento por instância. 

* Saiba como [adicionar uma fonte de centro de eventos.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)
* Configure [uma fonte de hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limites da API

Rest API limits para Time Series Insights Disponibilidade geral são especificados na [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)da API REST .