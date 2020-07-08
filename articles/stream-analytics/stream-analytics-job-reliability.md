---
title: Evite interrupções de serviço em trabalhos Azure Stream Analytics
description: Este artigo descreve orientações para tornar os seus trabalhos stream Analytics resilientes.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 8d62f87a6bf33bbbd85b68c9d2e0c6f4a93adf16
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044435"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantia Stream Analytics fiabilidade do trabalho durante atualizações de serviço

Parte de ser um serviço totalmente gerido é a capacidade de introduzir novas funcionalidades de serviço e melhorias a um ritmo rápido. Como resultado, o Stream Analytics pode ter uma atualização de serviço implementada semanalmente (ou mais frequente). Não importa quantos testes sejam feitos, existe ainda o risco de um trabalho existente e em execução poder quebrar devido à introdução de um inseto. Se estiver a executar trabalhos críticos da missão, estes riscos têm de ser evitados. Pode reduzir este risco seguindo o modelo de **[região emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** da Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como é que as regiões emparelhadas de Azure abordam esta preocupação?

Stream Analytics garante que os empregos em regiões emparelhadas são atualizados em lotes separados. Como resultado, existe um intervalo de tempo suficiente entre as atualizações para identificar potenciais problemas e remediar as questões.

_Com exceção da Índia Central_ (cuja região emparelhada, no Sul da Índia, não tem presença stream analytics), a implantação de uma atualização para stream Analytics não ocorreria ao mesmo tempo num conjunto de regiões emparelhadas. Podem ocorrer implantações em várias regiões **do mesmo grupo** ao mesmo **tempo**.

O artigo sobre **[disponibilidade e regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** tem a informação mais atualizada sobre as regiões emparelhadas.

Recomenda-se a colocação de empregos idênticos em ambas as regiões emparelhadas. Em seguida, deve [monitorizar estes trabalhos](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) para ser notificado quando algo inesperado acontece. Se um destes trabalhos acabar num [estado falhado](https://docs.microsoft.com/azure/stream-analytics/job-states) após uma atualização do serviço Stream Analytics, pode contactar o apoio ao cliente para ajudar a identificar a causa principal. Deve também falhar em relação a qualquer consumidor a jusante à produção de emprego saudável.

## <a name="next-steps"></a>Próximos passos

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência linguística de consulta de stream analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API de gestão de stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
