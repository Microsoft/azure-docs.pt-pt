---
title: Aumento anormal no volume de exceção - Insights de aplicação do Azure
description: Monitorize exceções de aplicação com Smart Detection in Azure Application Insights para padrões incomuns em volume de exceção.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: a08fae4774a8afb9959f55ea3196cd1a45c33439
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671771"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Aumento anormal do volume de exceção (pré-visualização)

Application Insights analisa automaticamente as exceções lançadas na sua aplicação, e pode avisá-lo sobre padrões incomuns na sua telemetria de exceção.

Esta funcionalidade não requer configuração especial, a não ser [configurar relatórios](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) de exceção para a sua aplicação. Está ativo quando a sua aplicação gera telemetria de exceção suficiente.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu receberia este tipo de notificação de deteção inteligente?
Poderá receber este tipo de notificação se a sua aplicação apresentar um aumento anormal no número de exceções de um tipo específico durante um dia, em comparação com uma linha de base calculada nos sete dias anteriores.
Os algoritmos de aprendizagem automática estão a ser usados para detetar o aumento da contagem de exceções, tendo em conta um crescimento natural no uso da sua aplicação.

## <a name="does-my-app-definitely-have-a-problem"></a>A minha aplicação tem um problema?
Não, uma notificação não significa que a sua aplicação definitivamente tenha um problema. Embora um número excessivo de exceções indique geralmente um problema de aplicação, estas exceções podem ser benignas e tratadas corretamente pela sua aplicação.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para apoiar no processo de diagnóstico:
1. **A triagem.** A notificação mostra quantos utilizadores ou quantos pedidos são afetados. Isto pode ajudá-lo a atribuir uma prioridade ao problema.
2. **O alcance.** O problema está a afetar todo o tráfego, ou apenas alguma operação? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção contém informações sobre o método a partir do qual a exceção foi lançada, bem como o tipo de exceção. Também pode utilizar os itens e relatórios relacionados que ligam a informações de apoio, para ajudá-lo a diagnosticar o problema.
