---
title: Deteção - aumento anormal no volume de exceção, no Azure Application Insights inteligente | Documentos da Microsoft
description: Monitorizar exceções de aplicativos com o Azure Application Insights para padrões de invulgares no volume de exceção.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: a6e7e8e01ccb623a3ff340c318c9c238c919cb38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61298589"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Aumento anormal no volume de exceção (pré-visualização)

O Application Insights automaticamente analisa as exceções geradas na sua aplicação e possam avisá-lo sobre os padrões invulgares na sua telemetria de exceção.

Esta funcionalidade não requer nenhuma configuração especial, que [configurar relatórios de exceção](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) para a sua aplicação. Ele está ativo quando a sua aplicação gerar telemetria suficiente exceção.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu teria este tipo de notificação de deteção inteligente?
Poderá receber este tipo de notificação se a sua aplicação é que apresentam um aumento anormal no número de exceções de um tipo específico durante um dia, em comparação com uma linha de base calculada ao longo de sete dias anteriores.
Algoritmos de Machine learning, estão a ser utilizados para detectar o aumento na contagem de exceções, ao mesmo tempo, tendo em conta um crescimento natural a utilização da aplicação.

## <a name="does-my-app-definitely-have-a-problem"></a>O meu aplicativo tem definitivamente um problema?
Não, uma notificação não significa que a aplicação tem definitivamente um problema. Embora um número excessivo de exceções normalmente indica um problema de aplicativo, essas exceções podem ser benignas e processados corretamente pelo seu aplicativo.

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para suportar o processo de diagnóstico:
1. **Triagem.** A notificação mostra o número de utilizadores ou o número de pedidos que é afetado. Isto pode ajudar a atribuir uma prioridade para o problema.
2. **Âmbito.** O problema é afetar todo o tráfego, ou apenas alguns operação? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção contém informações sobre o método a partir do qual a exceção foi acionada, bem como o tipo de exceção. Também pode utilizar os itens relacionados e relatórios que criar uma ligação para informações, para ajudá-lo ainda mais suporte diagnosticar o problema.