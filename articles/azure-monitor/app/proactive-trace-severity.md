---
title: Degradação na taxa de gravidade de rastreamento-insights de Aplicativo Azure
description: Monitore rastreamentos de aplicativos com Aplicativo Azure insights para padrões incomuns na telemetria de rastreamento com detecção inteligente.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/27/2017
ms.openlocfilehash: b51cb66b59589c9b58d9115dfa6cefce0a20f3c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432419"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradação na taxa de gravidade de rastreamento (versão prévia)

Os rastreamentos são amplamente usados em aplicativos, pois ajudam a informar a história do que acontece nos bastidores. Quando as coisas dão errado, os rastreamentos fornecem visibilidade crucial da sequência de eventos que levam ao estado indesejado. Embora os rastreamentos sejam geralmente não estruturados, há uma coisa que pode ser Obtida de forma concreta a partir deles – seu nível de severidade. No estado estável de um aplicativo, esperamos que a taxa entre rastreamentos "bons" (*informações* e *detalhadas*) e rastreamentos "incorretos" (*aviso*, *erro*e *crítico*) permaneçam estáveis. A suposição é que os rastreamentos "ruins" podem ocorrer regularmente a uma determinada extensão devido a vários motivos (problemas de rede transitórios, por exemplo). Mas, quando um problema real começa a crescer, ele geralmente se manifesta como um aumento na proporção relativa de rastreamentos "ruins" vs "bons". Application Insights detecção inteligente analisa automaticamente os rastreamentos registrados pelo seu aplicativo e pode avisá-lo sobre padrões incomuns na severidade de sua telemetria de rastreamento.

Esse recurso não requer nenhuma configuração especial, além de configurar o log de rastreamento para seu aplicativo (consulte como configurar um ouvinte de log de rastreamento para [.net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) ou [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Ele está ativo quando seu aplicativo gera telemetria de exceção suficiente.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia esse tipo de notificação de detecção inteligente?
Você poderá obter esse tipo de notificação se a taxa entre rastreamentos "bons" (rastreamentos registrados com um nível de *informações* ou *detalhado*) e rastreamentos "incorretos" (rastreamentos registrados com um nível de *aviso*, *erro*ou *fatal*) estiver degradando em um dia específico, em comparação com uma linha de base calculada nos últimos sete dias.

## <a name="does-my-app-definitely-have-a-problem"></a>Meu aplicativo definitivamente tem um problema?
Não, uma notificação não significa que seu aplicativo definitivamente tem um problema. Embora uma degradação na taxa entre os rastreamentos "bons" e "incorretos" possa indicar um problema de aplicativo, essa alteração na proporção pode ser benigna. Por exemplo, o aumento pode ser devido a um novo fluxo no aplicativo que emite mais rastreamentos "ruins" do que os fluxos existentes).

## <a name="how-do-i-fix-it"></a>Como posso corrigi-lo?
As notificações incluem informações de diagnóstico para dar suporte ao processo de diagnóstico:
1. **Triagem.** A notificação mostra quantas operações são afetadas. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Com.** O problema está afetando todo o tráfego ou apenas alguma operação? Essas informações podem ser obtidas na notificação.
3. **Tect.** Você pode usar os itens relacionados e os relatórios vinculando a informações de suporte para ajudá-lo a diagnosticar o problema.


