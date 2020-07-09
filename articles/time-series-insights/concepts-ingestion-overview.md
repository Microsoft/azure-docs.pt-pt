---
title: Visão geral da ingestão - Azure Time Series Insights / Microsoft Docs
description: Saiba mais sobre a ingestão de dados no Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050114"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Azure Time Series Insights visão geral da ingestão de dados

O seu ambiente Azure Time Series Insights contém um *motor de ingestão* para recolher, processar e armazenar dados de séries de tempo de streaming. À medida que os dados chegam à sua fonte de eventos, o Azure Time Series Insights consumirá e armazenará os seus dados em tempo real.

[![Visão geral da ingestão](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Tópicos de ingestão

Os seguintes artigos abrangem o tratamento de dados em profundidade, incluindo as melhores práticas a seguir:

* Leia sobre [fontes de eventos](concepts-streaming-ingestion-event-sources.md) e orientações sobre a seleção de um timetamp de fonte de evento.

* Reveja os tipos de [dados suportados](concepts-supported-data-types.md)

* Compreenda como o motor de ingestão aplicará um conjunto de [regras](./concepts-json-flattening-escaping-rules.md) às suas propriedades JSON para criar as colunas da sua conta de armazenamento.

* Reveja as [limitações de produção](concepts-streaming-throughput-limitations.md) do seu ambiente para planear as suas necessidades de escala.

## <a name="next-steps"></a>Próximos passos

* Continue a saber mais sobre [fontes de eventos](concepts-streaming-ingestion-event-sources.md) para o seu ambiente Azure Time Series Insights. 
