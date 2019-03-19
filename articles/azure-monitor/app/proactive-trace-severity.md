---
title: Deteção - degradação na proporção de gravidade de rastreio, no Azure Application Insights inteligente | Documentos da Microsoft
description: Monitorizar rastreios de aplicações com o Azure Application Insights para padrões invulgares na telemetria de rastreio.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: 10b909fd5239546047aa4696a1f6a68a703778c0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58001224"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradação na proporção de gravidade de rastreio (pré-visualização)

Rastreios são amplamente utilizados nas aplicações, para contar a história do que acontece em segundo plano. Quando as coisas correm mal, rastreios fornecem visibilidade crucial para a sequência de eventos que leva para o estado indesejado. Embora os rastreios são geralmente não estruturados, há uma coisa que concretamente pode ser adquirida a partir deles – o nível de gravidade. No estado estável de um aplicativo, poderia esperar a proporção entre rastreios "boas" (*Info* e *verboso*) e rastreios "bad" (*aviso*, *erro*, e *crítico*) permaneça estável. A pressuposição é de que os rastreios "bad" podem acontecer em intervalos regulares até certo ponto devido a qualquer número de motivos (problemas de rede transitórios por exemplo). Mas, quando um problema real começa a crescer, ele normalmente se manifesta como um aumento da proporção relativo de rastreios de "bom" rastreios "bad" vs. Deteção inteligente do Application Insights automaticamente analisa os rastreios registados pelo seu aplicativo e pode avisá-lo sobre os padrões invulgares na gravidade da sua telemetria de rastreio.

Esta funcionalidade não requer nenhuma configuração especial, além de configurar o registo de rastreio para a sua aplicação (veja como configurar um serviço de escuta de registo de rastreio para [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) ou [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Ele está ativo quando a sua aplicação gerar telemetria suficiente exceção.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu teria este tipo de notificação de deteção inteligente?
Poderá receber este tipo de notificação se a proporção entre rastreios "boas" (rastreios com sessão iniciados com um nível de *informações* ou *verboso*) e rastreios "bad" (rastreios com sessão iniciados com um nível de *aviso*, *Erro*, ou *Fatal*) estar prejudicando num dia específico, em comparação comparado uma linha de base calculada ao longo de sete dias anteriores.

## <a name="does-my-app-definitely-have-a-problem"></a>O meu aplicativo tem definitivamente um problema?
Não, uma notificação não significa que a aplicação tem definitivamente um problema. Embora uma degradação na razão entre "bom" e "bad" rastreios pode indicar um problema de aplicação, esta alteração na proporção poderá ser benigno. Por exemplo, o aumento pode ser devido a um novo fluxo no aplicativo emitindo rastreios "bad" mais do que os fluxos existentes).

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para suportar o processo de diagnóstico:
1. **Triagem.** A notificação mostra quantas operações são afetadas. Isto pode ajudar a atribuir uma prioridade para o problema.
2. **Âmbito.** O problema é afetar todo o tráfego, ou apenas alguns operação? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** Pode utilizar os itens relacionados e relatórios que criar uma ligação para informações, para ajudá-lo ainda mais suporte diagnosticar o problema.


