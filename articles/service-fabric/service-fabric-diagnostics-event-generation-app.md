---
title: Monitoramento de nível de aplicativo Service Fabric do Azure
description: Saiba mais sobre eventos e logs de nível de serviço e aplicativo usados para monitorar e diagnosticar clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 97c3be391dfbee7301ea47bf7234a9549d373370
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464718"
---
# <a name="application-logging"></a>Registo de aplicação

Instrumentar seu código não é apenas uma maneira de obter informações sobre seus usuários, mas também a única maneira de saber se algo está errado em seu aplicativo e diagnosticar o que precisa ser corrigido. Embora tecnicamente seja possível conectar um depurador a um serviço de produção, não é uma prática comum. Portanto, ter dados de instrumentação detalhados é importante.

Alguns produtos instrumentam seu código automaticamente. Embora essas soluções possam funcionar bem, a instrumentação manual quase sempre precisa ser específica para sua lógica de negócios. No final, você deve ter informações suficientes para depurar o aplicativo de forma forense. Service Fabric aplicativos podem ser instrumentados com qualquer estrutura de registro em log. Este documento descreve algumas abordagens diferentes para instrumentar seu código e quando escolher uma abordagem sobre outra. 

Para obter exemplos de como usar essas sugestões, consulte [adicionar registro em log ao seu aplicativo Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>SDK do Application Insights

Application Insights tem uma integração rica com Service Fabric pronta para uso. Os usuários podem adicionar os pacotes do AI Service Fabric NuGet e receber dados e logs criados e coletados no portal do Azure. Além disso, os usuários são incentivados a adicionar sua própria telemetria para diagnosticar e depurar seus aplicativos e controlar quais serviços e partes de seus aplicativos são mais usados. A classe [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) no SDK fornece várias maneiras de rastrear a telemetria em seus aplicativos. Confira um exemplo de como instrumentar e adicionar o Application insights ao seu aplicativo em nosso tutorial para [monitorar e diagnosticar um aplicativo .net](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

Quando você cria uma solução de Service Fabric de um modelo no Visual Studio, uma classe derivada de **EventSource**( **ActorEventSource**ou**messageeventname** ) é gerada. Um modelo é criado, no qual você pode adicionar eventos para seu aplicativo ou serviço. O nome EventSource **deve** ser exclusivo e deve ser renomeado da cadeia de caracteres do modelo padrão MyCompany-&lt;solução&gt;-&lt;projeto&gt;. Ter várias definições de **EventSource** que usam o mesmo nome causa um problema no tempo de execução. Cada evento definido deve ter um identificador exclusivo. Se um identificador não for exclusivo, ocorrerá uma falha de tempo de execução. Algumas organizações preatribuem intervalos de valores para identificadores para evitar conflitos entre equipes de desenvolvimento separadas. Para obter mais informações, consulte o [blog do Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou a [documentação do MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>Log de ASP.NET Core

É importante planejar cuidadosamente como você instrumenta seu código. O plano de instrumentação certo pode ajudá-lo a evitar a desestabilização de sua base de código e, em seguida, precisar reinstrumentar o código. Para reduzir o risco, você pode escolher uma biblioteca de instrumentação como [Microsoft. Extensions. Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), que faz parte do Microsoft ASP.NET Core. ASP.NET Core tem uma interface [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) que você pode usar com o provedor de sua escolha, minimizando o efeito sobre o código existente. Você pode usar o código em ASP.NET Core no Windows e Linux e, na .NET Framework completa, o código de instrumentação será padronizado.

## <a name="next-steps"></a>Passos seguintes

Depois de escolher seu provedor de log para instrumentar seus aplicativos e serviços, seus logs e eventos precisam ser agregados antes que possam ser enviados a qualquer plataforma de análise. Leia sobre [Application insights](service-fabric-diagnostics-event-analysis-appinsights.md) e [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) para entender melhor algumas das opções Azure monitor recomendadas.
