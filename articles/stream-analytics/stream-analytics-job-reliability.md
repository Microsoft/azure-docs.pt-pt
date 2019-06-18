---
title: Evitar interrupções de serviços em tarefas do Azure Stream Analytics
description: Este artigo descreve orientações sobre a realização de suas tarefas do Stream Analytics atualizar resiliente.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479461"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garante a confiabilidade de tarefa do Stream Analytics durante as atualizações de serviço

Parte do que está a ser um serviço totalmente gerido é a capacidade para introduzir novas funcionalidades de serviço e aprimoramentos rapidamente. Como resultado, o Stream Analytics pode ter uma atualização de serviço implementar numa base semanal (ou com mais frequência). Independentemente da quantidade de teste é realizado ainda há um risco de que uma tarefa existente, em execução pode ser interrompidos devido à introdução de um bug. Para os clientes que executam tarefas de processamento de transmissão em fluxo críticos estes riscos tem de ser evitada. Um mecanismo que os clientes podem utilizar para reduzir este risco é o do Azure **[região emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelo. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como é que as regiões emparelhadas do Azure abordar esta questão?

Stream Analytics garante tarefas em regiões emparelhadas são atualizadas em lotes separados. Assim existe um intervalo de tempo suficiente entre as atualizações para identificar potenciais erros de última hora e a resolvê-los.

_Com exceção da Índia Central_ (cujo região emparelhada, Sul da Índia, não tem a presença de Stream Analytics), a implementação de uma atualização para o Stream Analytics não ocorreria ao mesmo tempo num conjunto de regiões emparelhadas. Implementações em várias regiões **no mesmo grupo de** pode ocorrer **ao mesmo tempo**.

O artigo sobre **[disponibilidade e regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** tem as informações mais atualizadas em que regiões são emparelhadas.

Os clientes são aconselhados a implementar tarefas idênticas em ambas as regiões emparelhadas. Além de análise de Stream interno de capacidades de monitorização, os clientes também são aconselhados a monitorizar as tarefas como se **ambos** são tarefas de produção. Se for identificado um intervalo de ser o resultado da atualização de serviço do Stream Analytics, escalar adequadamente e efetuar a ativação pós-falha nenhum consumidor jusante para o resultado da tarefa em bom estado. Escalonamento para o suporte irá impedir que a região emparelhada sejam afetados pela implantação nova e manter a integridade dos trabalhos emparelhados.

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da REST API de gestão de análise de Stream](https://msdn.microsoft.com/library/azure/dn835031.aspx)
