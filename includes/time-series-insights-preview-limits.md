---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/16/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: dfe16b4e965670d115cfa92f1cb3ca812d6375ad
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990877"
---
### <a name="general-availability-and-preview-comparison"></a>Disponibilidade geral e comparação de visualização

A tabela a seguir resume várias diferenças importantes entre Azure Time Series Insights GA (disponibilidade geral) e instâncias de visualização.

| | GA (Geographic Availability, Disponibilidade Geográfica) | Pré-visualização |
| --- | --- | ---|
| Cidadão de primeira classe | Centrado em evento | Centrado em série temporal |
| Raciocínio semântico | Nível baixo (dados de referência) | Alto nível (modelos) |
| Contexto de dados | Não nível de dispositivo | Dispositivo e não nível de dispositivo |
| Armazenamento lógico de computação | Não | Armazenado na parte de variáveis de tipo do modelo |
| Armazenamento e controle de acesso | Não | Habilitado por meio do modelo |
| Agregações/amostragem | Não | Ponderado do evento e tempo demorado |
| Reconstrução de sinal | Não | Interpola |
| Produção de série temporal derivada | Não | Sim, mesclagens e junções |
| Flexibilidade da linguagem | Não combinável | Combinável |
| Expressão de idioma | Cadeia de predicado | Expressões de série temporal (cadeias de caracteres de predicado, valores, expressões e funções) |

### <a name="property-limits"></a>Limites de propriedade

Time Series Insights os limites de propriedade aumentaram para 1.000 de um limite máximo de 800 no GA. As propriedades de evento fornecidas têm as colunas JSON, CSV e de gráfico correspondentes que você pode exibir dentro do [Time Series insights Gerenciador de visualização](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| Visualizar PAYG | Propriedades de 1.000 (colunas) |
| S1 GA | Propriedades de 600 (colunas) |
| S2 GA | Propriedades de 800 (colunas) |

### <a name="event-sources"></a>Origens de eventos

Há suporte para no máximo duas origens de evento por instância. 

* Saiba como [Adicionar uma origem do hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configurar [uma origem do Hub IOT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limites de API

Os limites da API REST para Time Series Insights versão prévia são especificados na [documentação de referência da API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).