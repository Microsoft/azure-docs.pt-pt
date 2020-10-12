---
title: Visão geral da ingestão - Azure Time Series Insights Gen2 / Microsoft Docs
description: Saiba mais sobre a ingestão de dados na Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.custom: seodec18
ms.openlocfilehash: 57c23ba4acdbde1a5dfac39d89a09dfcef6b25a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460905"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Azure Time Series Insights Gen2 data ingestion visão geral

O seu ambiente Azure Time Series Insights Gen2 contém um *motor de ingestão* para recolher, processar e armazenar dados da série de tempo de streaming. À medida que os dados chegam à sua fonte de eventos, a Azure Time Series Insights Gen2 consumirá e armazenará os seus dados em tempo real.

[![Descrição geral da ingestão](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Tópicos de ingestão

Os seguintes artigos abrangem o tratamento de dados em profundidade, incluindo as melhores práticas a seguir:

* Leia sobre [fontes de eventos](./concepts-streaming-ingestion-event-sources.md) e orientações sobre a seleção de um timetamp de fonte de evento.

* Reveja os tipos de [dados suportados](./concepts-supported-data-types.md)

* Compreenda como o motor de ingestão aplicará um conjunto de [regras](./concepts-json-flattening-escaping-rules.md) às suas propriedades JSON para criar as colunas da sua conta de armazenamento.

* Reveja as [limitações de produção](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente para planear as suas necessidades de escala.

## <a name="next-steps"></a>Passos seguintes

* Continue a conhecer mais sobre [fontes de eventos](./concepts-streaming-ingestion-event-sources.md) para o seu ambiente Azure Time Series Insights Gen2.
