---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 48080bb4d1e24f7f98d3dfe1fd63b65ba46df35e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289905"
---
### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade da Azure Time Series Insights aumentaram para 1.000 de um limite máximo de 800 na Gen1. As propriedades fornecidas do evento têm correspondentes colunas JSON, CSV e gráficos que pode ver dentro do [Azure Time Series Insights Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| Gen2 (L1) | 1.000 propriedades (colunas) |
| Gen1 (S1) | 600 propriedades (colunas) |
| Gen1 (S2) | 800 propriedades (colunas) |

### <a name="event-sources"></a>Origens de eventos

É suportado um máximo de duas fontes de evento por instância.

* Saiba como [adicionar uma fonte de centro de eventos.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)
* Configure [uma fonte de hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Por padrão, [os ambientes da Gen2 suportam taxas](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingress-throughput-limits) de entrada até **1 megabyte por segundo (MB/s) por ambiente**. Se necessário, os clientes podem escalar os seus ambientes até **16 MB/s.** Existe também um limite por partição de **0,5 MB/s**.

### <a name="api-limits"></a>Limites da API

Os limites de API para Azure Time Series Insights Gen2 são especificados na [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)da API REST .
