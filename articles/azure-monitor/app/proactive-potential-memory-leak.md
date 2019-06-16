---
title: Deteção - potencial fuga de memória detetado pelo Azure Application Insights inteligente | Documentos da Microsoft
description: Monitorizar aplicações com o Azure Application Insights para potenciais fugas de memória.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: e430b1e976ac26f7320b28d50dd39923066cfa41
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306348"
---
# <a name="memory-leak-detection-preview"></a>Deteção de vazamento de memória (pré-visualização)

O Application Insights automaticamente analisa o consumo de memória de cada processo em seu aplicativo e pode avisá-lo sobre potenciais fugas de memória ou consumo de memória maior.

Esta funcionalidade não requer nenhuma configuração especial, que [configurando contadores de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) para a sua aplicação. Ele está ativo quando a sua aplicação gerar a telemetria de contadores de desempenho suficiente memória (por exemplo, Bytes privados).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu teria este tipo de notificação de deteção inteligente?
Uma notificação típica seguirá um aumento consistente no consumo de memória durante um longo período de tempo, num ou mais processos e/ou uma ou mais máquinas, que fazem parte da sua aplicação. Algoritmos de Machine learning são utilizados para a detecção de consumo de memória avançados que corresponda ao padrão de um vazamento de memória.

## <a name="does-my-app-really-have-a-problem"></a>O meu aplicativo tem realmente um problema?
Não, uma notificação não significa que a aplicação tem definitivamente um problema. Embora padrões de vazamento de memória normalmente indicam um problema de aplicativo, estes padrões poderiam ser típicos para o seu processo específico, ou podem ter uma justificativa comercial natural e podem ser ignorados.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para suportar o processo de análise de diagnóstico:
1. **Triagem.** A notificação mostra aumentar a quantidade de memória (em GB) e o intervalo de tempo em que aumentou a memória. Isto pode ajudar a atribuir uma prioridade para o problema.
2. **Âmbito.** O número de máquinas apresentados o padrão de vazamento de memória? O número de exceções foram acionadas durante a potencial fuga de memória? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção contém o padrão de vazamento de memória, que mostra o consumo de memória do processo ao longo do tempo. Também pode utilizar os itens relacionados e relatórios que criar uma ligação para informações, para ajudá-lo ainda mais suporte diagnosticar o problema.
