---
title: Aumento anormal no volume de exceção – insights de Aplicativo Azure
description: Monitore as exceções de aplicativo com detecção inteligente no Aplicativo Azure insights de padrões incomuns no volume de exceção.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/08/2017
ms.openlocfilehash: b524000f78741b2ff8cf442d2daaef0c7945799a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432426"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Aumento anormal no volume de exceção (versão prévia)

Application Insights analisa automaticamente as exceções geradas em seu aplicativo e pode avisá-lo sobre padrões incomuns em sua telemetria de exceção.

Este recurso não requer nenhuma configuração especial, além de [Configurar o relatório de exceções](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) para seu aplicativo. Ele está ativo quando seu aplicativo gera telemetria de exceção suficiente.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia esse tipo de notificação de detecção inteligente?
Você poderá obter esse tipo de notificação se seu aplicativo estiver exibindo um aumento anormal no número de exceções de um tipo específico durante um dia, em comparação com uma linha de base calculada nos sete dias anteriores.
Os algoritmos de aprendizado de máquina estão sendo usados para detectar o aumento na contagem de exceção, levando em conta um crescimento natural no uso do aplicativo.

## <a name="does-my-app-definitely-have-a-problem"></a>Meu aplicativo definitivamente tem um problema?
Não, uma notificação não significa que seu aplicativo definitivamente tem um problema. Embora um número excessivo de exceções geralmente indique um problema de aplicativo, essas exceções podem ser benignas e manipuladas corretamente pelo seu aplicativo.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para dar suporte ao processo de diagnóstico:
1. **Triagem.** A notificação mostra quantos usuários ou quantas solicitações foram afetadas. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Com.** O problema está afetando todo o tráfego ou apenas alguma operação? Essas informações podem ser obtidas na notificação.
3. **Tect.** A detecção contém informações sobre o método do qual a exceção foi lançada, bem como o tipo de exceção. Você também pode usar os itens relacionados e os relatórios vinculando a informações de suporte, para ajudá-lo a diagnosticar o problema.
