---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 1171c07a754b056fb9df2cee73ddf224427037cc
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86277854"
---
### <a name="general-availability-and-preview-comparison"></a>Disponibilidade geral e comparação de pré-visualização

A tabela seguinte resume várias diferenças-chave entre a disponibilidade geral do Azure Time Series Insights (GA) e as instâncias de pré-visualização.

| | GA | Pré-visualizar |
| --- | --- | ---|
| **Cidadão de primeira classe** | Centrado em eventos | Centrada em séries de tempo |
| **Raciocínio semântico** | Baixo nível (dados de referência) | Modelos de alto nível |
| **Contextualização de dados** | Nível não-dispositivo | Nível de dispositivo e não dispositivo |
| **Armazenamento de lógica computacional** | Não | Armazenadas em variáveis tipo parte do modelo |
| **Controlo de armazenamento e acessos** | Não | Habilidoso via modelo |
| **Agregações/Amostragem** | Não | Evento ponderado e ponderado pelo tempo |
| **Reconstrução do sinal** | Não | Interpolação |
| **Produção de séries de tempo derivadas** | Não | Sim, funde-se e junta-se |
| **Flexibilidade linguística** | Não compor | Composível |
| **Linguagem de expressão** | Cadeia predicado | Expressões de séries crono prazos (cadeias, valores, expressões e funções predicados) |

### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade da Time Series Insights aumentaram para 1.000 de um limite máximo de 800 em GA. As propriedades fornecidas do evento têm correspondentes colunas JSON, CSV e gráficos que pode ver dentro do explorador de [pré-visualização de Insights de Séries temporiais](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| Pré-visualização PAYG | 1.000 propriedades (colunas) |
| GA S1 | 600 propriedades (colunas) |
| GA S2 | 800 propriedades (colunas) |

### <a name="event-sources"></a>Origens de eventos

É suportado um máximo de duas fontes de evento por instância. 

* Saiba como [adicionar uma fonte de centro de eventos.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)
* Configure [uma fonte de hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Por padrão, [os ambientes de pré-visualização suportam taxas](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) de entrada até **1 megabyte por segundo (MB/s) por ambiente**. Se necessário, os clientes podem escalar os seus ambientes de pré-visualização até **16 MB/s.** Existe também um limite por partição de **0,5 MB/s**. 

### <a name="api-limits"></a>Limites da API

Rest Os limites da API para a visualização de insights de séries de tempo são especificados na [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)da API REST .
