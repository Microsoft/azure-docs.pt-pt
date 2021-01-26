---
title: Monitorização do nível de aplicação do tecido de serviço Azure
description: Saiba mais sobre eventos de aplicação e nível de serviço e registos utilizados para monitorizar e diagnosticar clusters de tecido de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: a1df5d033701195f4fe5f6b7174f3883b84393ab
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791278"
---
# <a name="application-logging"></a>Registo de aplicação

Instrumentar o seu código não é apenas uma forma de obter informações sobre os seus utilizadores, mas também a única forma de saber se algo está errado na sua aplicação e diagnosticar o que precisa de ser corrigido. Embora tecnicamente seja possível ligar um depurar a um serviço de produção, não é uma prática comum. Portanto, ter dados de instrumentação detalhados é importante.

Alguns produtos instrumentam automaticamente o seu código. Embora estas soluções possam funcionar bem, a instrumentação manual é quase sempre necessária para ser específica da sua lógica de negócio. No final, deve ter informações suficientes para depurar o pedido forense. As aplicações de tecido de serviço podem ser instrumentadas com qualquer enquadramento de registo. Este documento descreve algumas abordagens diferentes para instrumentar o seu código, e quando escolher uma abordagem em vez de outra. 

Por exemplo, sobre como utilizar estas sugestões, consulte [adicionar registo na sua aplicação De Tecido de Serviço.](service-fabric-how-to-diagnostics-log.md)

## <a name="application-insights-sdk"></a>Insights de Aplicação SDK

A Application Insights tem uma rica integração com o Tecido de Serviço fora da caixa. Os utilizadores podem adicionar os pacotes nuget do Tecido de Serviço da IA e receber dados e registos criados e recolhidos visualizados no portal Azure. Além disso, os utilizadores são encorajados a adicionar a sua própria telemetria para diagnosticar e depurar as suas aplicações e rastrear quais os serviços e partes da sua aplicação que são mais utilizados. A aula [de TelemetriaClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) no SDK fornece muitas formas de rastrear a telemetria nas suas aplicações. Confira um exemplo de como instrumentar e adicionar insights de aplicação à sua aplicação no nosso tutorial para [monitorização e diagnóstico de uma aplicação .NET](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>Fonte de Eventos

Quando cria uma solução de Tecido de Serviço a partir de um modelo no Visual Studio, é gerada uma classe derivada do **EventSource****(ServiceEventSource** ou **ActorEventSource).** É criado um modelo, no qual pode adicionar eventos para a sua aplicação ou serviço. O nome **EventSource** **deve** ser único e deve ser renomeado a partir do projeto de solução mycompany- solução de corda padrão &lt; &gt; - &lt; &gt; . Ter várias definições **de EventSource** que usam o mesmo nome causa um problema no tempo de execução. Cada evento definido deve ter um identificador único. Se um identificador não for único, ocorre uma falha de tempo de execução. Algumas organizações pré-assinaram gamas de valores para identificadores para evitar conflitos entre equipas de desenvolvimento separadas. Para mais informações, consulte o [blog do Vance](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource) ou a documentação da [MSDN.](/previous-versions/msp-n-p/dn774985(v=pandp.20))

## <a name="aspnet-core-logging"></a>Registo do núcleo de ASP.NET

É importante planear cuidadosamente como vai instrumentar o seu código. O plano de instrumentação certo pode ajudá-lo a evitar potencialmente desestabilizar a sua base de código e, em seguida, precisar de reinstruir o código. Para reduzir o risco, pode escolher uma biblioteca de instrumentação como [microsoft.extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), que faz parte do Microsoft ASP.NET Core. ASP.NET Core tem uma interface [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) que pode utilizar com o fornecedor à sua escolha, ao mesmo tempo que minimiza o efeito no código existente. Pode utilizar o código em ASP.NET Core no Windows e Linux, e em quadros .NET completos, para que o seu código de instrumentação seja normalizado.

## <a name="next-steps"></a>Próximos passos

Uma vez que tenha escolhido o seu fornecedor de registo para instrumentar as suas aplicações e serviços, os seus registos e eventos precisam de ser agregados antes de poderem ser enviados para qualquer plataforma de análise. Leia sobre [Insights de Aplicações](service-fabric-diagnostics-event-analysis-appinsights.md) e [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) para entender melhor algumas das opções recomendadas pelo Azure Monitor.
