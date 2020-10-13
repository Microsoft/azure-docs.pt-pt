---
title: Detetar fuga de memória - Azure Application Insights Smart Detection
description: Monitorize aplicações com Azure Application Insights para possíveis fugas de memória.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 3fe58cd7d61246c5565cd89fa782c8a977f09499
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539895"
---
# <a name="memory-leak-detection-preview"></a>Deteção de fugas de memória (pré-visualização)

O Application Insights analisa automaticamente o consumo de memória de cada processo na sua aplicação e pode alertá-lo sobre possíveis fugas de memória ou aumento do consumo de memória.

Esta funcionalidade não requer nenhuma configuração especial, além [de configurar contadores de desempenho](./performance-counters.md) para a sua aplicação. Está ativo quando a sua aplicação gera contadores de desempenho de memória suficientes (por exemplo, Private Bytes).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu receberia este tipo de notificação de deteção inteligente?
Uma notificação típica seguirá um aumento consistente do consumo de memória durante um longo período de tempo, em um ou mais processos e/ou uma ou mais máquinas, que fazem parte da sua aplicação. Os algoritmos de aprendizagem automática são usados para detetar um aumento do consumo de memória que corresponde ao padrão de uma fuga de memória.

## <a name="does-my-app-really-have-a-problem"></a>A minha aplicação tem mesmo algum problema?
Não, uma notificação não significa que a sua aplicação definitivamente tem um problema. Embora os padrões de fuga de memória geralmente indiquem um problema de aplicação, estes padrões podem ser típicos do seu processo específico, ou podem ter uma justificação natural do negócio, e podem ser ignorados.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para suporte no processo de análise de diagnóstico:
1. **A triagem.** A notificação mostra-lhe a quantidade de aumento de memória (em GB) e o intervalo de tempo em que a memória aumentou. Isto pode ajudá-lo a atribuir uma prioridade ao problema.
2. **O alcance.** Quantas máquinas exibiram o padrão de fuga de memória? Quantas exceções foram desencadeadas durante a possível fuga de memória? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção contém o padrão de fuga de memória, mostrando o consumo de memória do processo ao longo do tempo. Também pode utilizar os itens e relatórios relacionados que ligam a informações de suporte, para ajudá-lo a diagnosticar ainda mais o problema.
