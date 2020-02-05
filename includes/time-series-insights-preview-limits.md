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
ms.openlocfilehash: 8537db1e5703abd8df92a2950455bee556731d08
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013748"
---
### <a name="general-availability-and-preview-comparison"></a>Disponibilidade geral e comparação de pré-visualização

O quadro seguinte resume várias diferenças fundamentais entre a disponibilidade geral da Série De Tempo Azure Insights (GA) e as instâncias de pré-visualização.

| | GA (Geographic Availability, Disponibilidade Geográfica) | Pré-visualização |
| --- | --- | ---|
| Cidadão de primeira classe | Centrado em eventos | Centrado em séries temporais |
| Raciocínio semântico | Dados de baixo nível (dados de referência) | Modelos de alto nível (modelos) |
| Contextualização de dados | Nível não-dispositivo | Dispositivo e nível não-dispositivo |
| Armazenamento lógico computacional | Não | Armazenado em variáveis de tipo parte do modelo |
| Controlo de armazenamento e acesso | Não | Habilitado via modelo |
| Agregações/Amostragem | Não | Evento ponderado e tempo ponderado |
| Reconstrução de sinal | Não | Interpolação |
| Produção de séries temporais derivadas | Não | Sim, funde-se e junta-se |
| Flexibilidade linguística | Não compável | Compisável |
| Expressão de idioma | Corda predicada | Expressões da série temporal (cordas predicadas, valores, expressões e funções) |

### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade da Time Series Insights aumentaram para 1.000 de um limite máximo de 800 em GA. As propriedades fornecidas do evento têm correspondentes JSON, CSV e colunas de gráficos que você pode ver dentro do explorador de [pré-visualização](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)de Insights da Série De Tempo .

| SKU | Propriedades máximas |
| --- | --- |
| Pré-visualização PAYG | 1\.000 propriedades (colunas) |
| GA S1 | 600 propriedades (colunas) |
| GA S2 | 800 propriedades (colunas) |

### <a name="event-sources"></a>Origens de eventos

É apoiado um máximo de duas fontes de eventopor instância. 

* Saiba como [Adicionar uma origem do hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configure [uma fonte de hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Por padrão, os [ambientes de visualização dão suporte a tarifas de entrada](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress) de até **1 megabyte por segundo (MB/s) por ambiente**. Os clientes podem dimensionar seus ambientes de visualização de até **16 MB/s** de taxa de transferência, se necessário. Também há um limite por partição de **0,5 MB/s**. 

### <a name="api-limits"></a>Limites da API

Os limites de API DE REPOUSO para A Pré-visualização de Insights da Série De Tempo são especificados na documentação de referência rest [API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).
