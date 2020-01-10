---
title: Detectar vazamento de memória-detecção inteligente de insights de Aplicativo Azure
description: Monitore aplicativos com informações Aplicativo Azure para possíveis vazamentos de memória.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/12/2017
ms.openlocfilehash: cc6074732a86be02a8d8c596c46e154db4e8ad7f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406467"
---
# <a name="memory-leak-detection-preview"></a>Detecção de vazamento de memória (visualização)

Application Insights analisa automaticamente o consumo de memória de cada processo em seu aplicativo e pode avisá-lo sobre possíveis vazamentos de memória ou maior consumo de memória.

Esse recurso não requer nenhuma configuração especial, além da [configuração de contadores de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) para seu aplicativo. Ele está ativo quando seu aplicativo gera telemetria de contadores de desempenho de memória suficiente (por exemplo, bytes privados).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia esse tipo de notificação de detecção inteligente?
Uma notificação típica seguirá um aumento consistente no consumo de memória em um longo período de tempo, em um ou mais processos e/ou em uma ou mais máquinas, que fazem parte do seu aplicativo. Os algoritmos de aprendizado de máquina são usados para detectar maior consumo de memória que corresponde ao padrão de um vazamento de memória.

## <a name="does-my-app-really-have-a-problem"></a>Meu aplicativo realmente tem um problema?
Não, uma notificação não significa que seu aplicativo definitivamente tem um problema. Embora os padrões de perda de memória geralmente indiquem um problema de aplicativo, esses padrões podem ser típicos para seu processo específico, ou podem ter uma justificativa de negócios natural e podem ser ignorados.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para dar suporte ao processo de análise de diagnóstico:
1. **Triagem.** A notificação mostra a quantidade de aumento de memória (em GB) e o intervalo de tempo no qual a memória aumentou. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Com.** Quantos computadores exibiram o padrão de perda de memória? Quantas exceções foram disparadas durante o possível vazamento de memória? Essas informações podem ser obtidas na notificação.
3. **Tect.** A detecção contém o padrão de perda de memória, mostrando o consumo de memória do processo ao longo do tempo. Você também pode usar os itens relacionados e os relatórios vinculando a informações de suporte, para ajudá-lo a diagnosticar o problema.
