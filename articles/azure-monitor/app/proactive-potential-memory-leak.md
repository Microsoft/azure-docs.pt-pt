---
title: Detetar fuga de memória - Deteção inteligente de Insights de Aplicação Azure
description: Monitorize aplicações com Insights de Aplicação Azure para potenciais fugas de memória.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 85d138518dfb1313a810657016e9fe3143887b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671703"
---
# <a name="memory-leak-detection-preview"></a>Deteção de fugas de memória (pré-visualização)

Aplicação Insights analisa automaticamente o consumo de memória de cada processo na sua aplicação, e pode alertá-lo sobre potenciais fugas de memória ou aumento do consumo de memória.

Esta funcionalidade não requer configuração especial, a não ser [configurar contadores](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) de desempenho para a sua aplicação. Está ativo quando a sua aplicação gera telemetria de contadores de desempenho de memória suficientes (por exemplo, Private Bytes).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu receberia este tipo de notificação de deteção inteligente?
Uma notificação típica seguirá um aumento consistente do consumo de memória durante um longo período de tempo, em um ou mais processos e/ou uma ou mais máquinas, que fazem parte da sua aplicação. Os algoritmos de aprendizagem automática são usados para detetar um aumento do consumo de memória que corresponde ao padrão de uma fuga de memória.

## <a name="does-my-app-really-have-a-problem"></a>A minha aplicação tem mesmo algum problema?
Não, uma notificação não significa que a sua aplicação definitivamente tenha um problema. Embora os padrões de fuga de memória geralmente indiquem um problema de aplicação, estes padrões podem ser típicos do seu processo específico, ou podem ter uma justificação de negócio natural, e podem ser ignorados.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para apoiar no processo de análise de diagnóstico:
1. **A triagem.** A notificação mostra-lhe a quantidade de aumento de memória (em GB) e o intervalo de tempo em que a memória aumentou. Isto pode ajudá-lo a atribuir uma prioridade ao problema.
2. **O alcance.** Quantas máquinas exibiram o padrão de fuga de memória? Quantas exceções foram desencadeadas durante a fuga de memória? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção contém o padrão de fuga de memória, mostrando o consumo de memória do processo ao longo do tempo. Também pode utilizar os itens e relatórios relacionados que ligam a informações de apoio, para ajudá-lo a diagnosticar o problema.
