---
title: Nível de aplicativo de recursos de infraestrutura do serviço do Azure monitorização | Documentos da Microsoft
description: Saiba mais sobre os registos de aplicações e os eventos de nível de serviço e utilizados para monitorizar e diagnosticar os clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 613faf5bbc9498b82bc04460d30b2e94c30340db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393100"
---
# <a name="application-logging"></a>Registo de aplicação

Instrumentar o seu código é não apenas uma forma de obter informações sobre os seus utilizadores, mas também a única maneira que pode saber se algo está errado em seu aplicativo e para diagnosticar o que precisa ser corrigido. Embora seja tecnicamente possível conectar um depurador a um serviço de produção, não é uma prática comum. Então, ter detalhadas dados de instrumentação é importante.

Alguns produtos automaticamente instrumentem o seu código. Embora essas soluções podem funcionar bem, instrumentação manual quase sempre é necessário para ser específicas para sua lógica de negócio. No final, tem de ter informações suficientes para forensicamente depurar o aplicativo. Aplicações do Service Fabric podem ser equipadas com qualquer arquitetura de registo. Este documento descreve algumas abordagens diferentes para instrumentar o seu código e quando escolher uma abordagem em vez de outro. 

Para obter exemplos sobre como utilizar estas sugestões, consulte [adicionar registos à sua aplicação do Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>SDK do Application Insights

O Application Insights tem uma integração avançada com o Service Fabric prontos a utilizar. Os utilizadores podem adicionar os pacotes de nuget do Service Fabric de IA e receber dados e logs criados e recolhidos podem ser visualizados no portal do Azure. Além disso, os utilizadores são encorajados a adicionar a sua própria telemetria para diagnosticar e depurar seus aplicativos e a controlar que serviços e partes de seu aplicativo são utilizados mais. O [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) classe no SDK fornece várias formas de controlar a telemetria em seus aplicativos. Veja um exemplo de como instrumentar e adicionar o application insights ao seu aplicativo no nosso tutorial para [monitorizar e diagnosticar uma aplicação .NET](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

Quando cria uma solução de Service Fabric a partir de um modelo no Visual Studio, um **EventSource**-classe derivada (**ServiceEventSource** ou **ActorEventSource**) é gerado . Um modelo é criado, na qual pode adicionar eventos para a sua aplicação ou serviço. O **EventSource** name **tem** de ser exclusivo e deve ser alterado de cadeia de modelo padrão MyCompany -&lt;solução&gt;-&lt;projeto &gt;. Ter vários **EventSource** definições que utilizam o mesmo nome faz com que um problema no tempo de execução. Cada evento definido tem de ter um identificador exclusivo. Se um identificador não for exclusivo, ocorre uma falha de tempo de execução. Algumas organizações preassign intervalos de valores para os identificadores evitar conflitos entre as equipes de desenvolvimento separados. Para obter mais informações, consulte [blogue de artigo de Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou o [documentação do MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>O registo do ASP.NET Core

É importante planejar cuidadosamente a forma como irá instrumentar o seu código. O plano certo instrumentação pode ajudar a evitar desestabilizar potencialmente a sua base de código e, em seguida, a necessidade de reinstrument o código. Para reduzir o risco, pode escolher uma biblioteca de instrumentação, como [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), que faz parte do Microsoft ASP.NET Core. ASP.NET Core tem um [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) interface que pode utilizar com o fornecedor da sua preferência, enquanto minimiza o efeito no código existente. Pode usar o código no ASP.NET Core em Windows e Linux e no .NET Framework completo, então, seu código de instrumentação é padronizado.

## <a name="next-steps"></a>Passos Seguintes

Depois de escolher o fornecedor de registo para instrumentar seus aplicativos e serviços, os registos e eventos será necessário agregar antes que eles podem ser enviados para qualquer plataforma de análise. Leia sobre [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) e [o EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) para melhor compreender alguns do Azure Monitor opções recomendadas.
