---
title: Monitorização do nível de aplicação do tecido do serviço Azure
description: Conheça os eventos e registos de nível de aplicação e serviço utilizados para monitorizar e diagnosticar clusters de tecido de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 97c3be391dfbee7301ea47bf7234a9549d373370
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75464718"
---
# <a name="application-logging"></a>Registo de aplicação

A instrumentação do seu código não é apenas uma forma de obter informações sobre os seus utilizadores, mas também a única forma de saber se algo está errado na sua aplicação e diagnosticar o que precisa de ser corrigido. Embora tecnicamente seja possível ligar um depurador a um serviço de produção, não é uma prática comum. Portanto, ter dados de instrumentação detalhados é importante.

Alguns produtos instrumentam automaticamente o seu código. Embora estas soluções possam funcionar bem, a instrumentação manual é quase sempre necessária para ser específica da sua lógica de negócio. No final, deve ter informações suficientes para desinver o pedido forense. As aplicações de tecido de serviço podem ser instrumentadas com qualquer estrutura de exploração madeireira. Este documento descreve algumas abordagens diferentes para a instrumentalização do seu código, e quando escolher uma abordagem em vez de outra. 

Por exemplo, sobre como usar estas sugestões, consulte [Adicionar login na sua aplicação Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Insights de aplicação SDK

Application Insights tem uma rica integração com o Service Fabric fora da caixa. Os utilizadores podem adicionar os pacotes de nuget de tecido de serviço de IA e receber dados e registos criados e recolhidos visível no portal Azure. Além disso, os utilizadores são encorajados a adicionar a sua própria telemetria para diagnosticar e depurar as suas aplicações e rastrear quais os serviços e partes da sua aplicação que são mais utilizados. A classe [TelemettryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) no SDK fornece muitas formas de rastrear a telemetria nas suas aplicações. Confira um exemplo de como instrumentar e adicionar informações sobre aplicações à sua aplicação no nosso tutorial para [monitorização e diagnóstico de uma aplicação .NET](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

Quando cria uma solução de Tecido de Serviço a partir de um modelo no Estúdio Visual, é gerada uma classe derivada de **EventSource****(ServiceEventSource** ou **ActorEventSource).** É criado um modelo, no qual pode adicionar eventos para a sua aplicação ou serviço. O nome **EventSource** **deve** ser único e deve ser renomeado&lt;&gt;-&lt;a&gt;partir do projeto de solução myCompany- modelo padrão . Ter várias definições **de EventSource** que usam o mesmo nome causa um problema no tempo de execução. Cada evento definido deve ter um identificador único. Se um identificador não for único, ocorre uma falha no tempo de execução. Algumas organizações pretribuem gamas de valores para identificadores para evitar conflitos entre equipas de desenvolvimento separadas. Para mais informações, consulte o [blog do Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou a documentação da [MSDN.](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)

## <a name="aspnet-core-logging"></a>ASP.NET loglogging Core

É importante planear cuidadosamente como vai saquear o seu código. O plano de instrumentação certo pode ajudá-lo a evitar potencialmente desestabilizar a sua base de código e, em seguida, precisar de reinstrumentar o código. Para reduzir o risco, pode escolher uma biblioteca de instrumentação como [a Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), que faz parte do Microsoft ASP.NET Core. ASP.NET Core tem uma interface [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) que pode utilizar com o fornecedor à sua escolha, minimizando o efeito no código existente. Pode utilizar o código em ASP.NET Core no Windows e linux, e na estrutura .NET completa, para que o seu código de instrumentação seja normalizado.

## <a name="next-steps"></a>Passos seguintes

Depois de ter escolhido o seu fornecedor de registos para instrumentar as suas aplicações e serviços, os seus registos e eventos precisam de ser agregados antes de poderem ser enviados para qualquer plataforma de análise. Leia sobre os Insights de [Aplicação](service-fabric-diagnostics-event-analysis-appinsights.md) e [o EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) para entender melhor algumas das opções recomendadas pelo Monitor Azure.
