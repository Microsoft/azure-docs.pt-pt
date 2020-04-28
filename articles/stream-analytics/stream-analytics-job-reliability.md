---
title: Evite interrupções de serviço em empregos da Azure Stream Analytics
description: Este artigo descreve orientações para tornar os seus trabalhos stream analytics atualizados resilientes.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75425992"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantia Stream Analytics fiabilidade do trabalho durante atualizações de serviço

Parte de ser um serviço totalmente gerido é a capacidade de introduzir novas funcionalidades de serviço e melhorias a um ritmo acelerado. Como resultado, o Stream Analytics pode ter uma atualização de serviço implementada semanalmente (ou mais frequente). Por muito que os testes sejam feitos, existe ainda o risco de um trabalho existente e em execução poder quebrar devido à introdução de um bug. Se está a gerir trabalhos críticos da missão, estes riscos têm de ser evitados. Pode reduzir este risco seguindo o modelo de **[região emparelhado](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** do Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como é que as regiões emparelhadas de Azure abordam esta preocupação?

Stream Analytics garante que os postos de trabalho em regiões emparelhadas são atualizados em lotes separados. Como resultado, existe um espaço de tempo suficiente entre as atualizações para identificar potenciais problemas e corrigi-los.

_Com exceção da Índia Central_ (cuja região emparelhada, a Índia do Sul, não tem presença no Stream Analytics), a implantação de uma atualização para o Stream Analytics não ocorreria ao mesmo tempo num conjunto de regiões emparelhadas. As implantações em várias regiões **do mesmo grupo** podem ocorrer ao mesmo **tempo**.

O artigo sobre **[disponibilidade e regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** tem a informação mais atualizada sobre quais as regiões que estão emparelhadas.

Recomenda-se a implantação de postos de trabalho idênticos em ambas as regiões emparelhadas. Deve [então monitorizar estes trabalhos](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) para ser notificado quando algo inesperado acontece. Se um destes trabalhos acabar num [estado falhado](https://docs.microsoft.com/azure/stream-analytics/job-states) após uma atualização de serviço stream analytics, pode contactar o apoio do cliente para ajudar a identificar a causa principal. Também deve falhar em relação a quaisquer consumidores a jusante para a produção saudável de emprego.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência rest API de gestão stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
