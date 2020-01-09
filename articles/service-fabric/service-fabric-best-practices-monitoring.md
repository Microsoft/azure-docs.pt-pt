---
title: Práticas recomendadas de monitoramento de Service Fabric do Azure
description: Práticas recomendadas e considerações de design para monitorar clusters e aplicativos usando o Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: af03223e8b007cbd2a00d54c3076056cd110ecc9
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551821"
---
# <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

O [monitoramento e o diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) são essenciais para o desenvolvimento, o teste e a implantação de cargas de trabalho em qualquer ambiente de nuvem. Por exemplo, você pode acompanhar como seus aplicativos são usados, as ações tomadas pela plataforma Service Fabric, a utilização de recursos com contadores de desempenho e a integridade geral do cluster. Você pode usar essas informações para diagnosticar e corrigir problemas e impedir que eles ocorram no futuro.

## <a name="application-monitoring"></a>Monitorização da aplicação

O monitoramento de aplicativos controla como os recursos e componentes do seu aplicativo estão sendo usados. Monitore seus aplicativos para garantir que os problemas que afetam os usuários sejam detectados. O monitoramento de aplicativos é responsabilidade do desenvolvimento do aplicativo e de seus serviços, pois ele é exclusivo para a lógica de negócios do seu aplicativo. É recomendável configurar o monitoramento de aplicativos com o [Application insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), a ferramenta de monitoramento de aplicativos do Azure.

## <a name="cluster-monitoring"></a>Monitorização de cluster

Uma das metas de Service Fabric é tornar os aplicativos resilientes a falhas de hardware. Essa meta é obtida por meio da capacidade dos serviços do sistema da plataforma de detectar problemas de infraestrutura e fazer failover rápido de cargas de trabalho para outros nós no cluster. Mas e se os próprios serviços do sistema tiverem problemas? Ou se estiver tentando implantar ou mover uma carga de trabalho, as regras para o posicionamento dos serviços serão violadas? Service Fabric fornece diagnóstico para esses e outros problemas, para ter certeza de que você está informado sobre como a plataforma de Service Fabric interage com seus aplicativos, serviços, contêineres e nós.

Para clusters do Windows, é recomendável que você configure o monitoramento de cluster com o [agente de diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) e [os logs de Azure monitor](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Para clusters do Linux, os logs de Azure Monitor também são a ferramenta recomendada para o monitoramento de infraestrutura e plataforma do Azure. O diagnóstico da plataforma Linux requer uma configuração diferente, conforme observado em [Service Fabric eventos de cluster do Linux no syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Monitorização das infraestruturas

[Azure monitor logs](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) é recomendado para monitorar eventos no nível do cluster. Depois de configurar o agente de Log Analytics com seu espaço de trabalho conforme descrito no link anterior, você poderá coletar métricas de desempenho, como utilização da CPU, contadores de desempenho do .NET, como utilização de CPU de nível de processo, Service Fabric contadores de desempenho, como n º de exceções de um serviço confiável e métricas de contêiner, como a utilização da CPU.  Você precisará Gravar logs de contêiner para stdout ou stderr para que eles estejam disponíveis nos logs de Azure Monitor.

## <a name="watchdogs"></a>Watchdogs

Geralmente, um Watchdog é um serviço separado que observa a integridade e a carga entre os serviços, executa ping nos pontos de extremidade e relata eventos de integridade inesperados no cluster. Isso pode ajudar a evitar erros que podem não ser detectados com base apenas no desempenho de um único serviço. Os Watchdogs também são um bom local para hospedar códigos que executam ações corretivas que não exigem interação do usuário, como a limpeza de arquivos de log no armazenamento em determinados intervalos de tempo. Consulte uma implementação de serviço WatchDog de exemplo em [Service Fabric eventos de cluster do Linux no syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Passos seguintes

* Comece a instrumentar seus aplicativos: [geração de log e evento de nível de aplicativo](service-fabric-diagnostics-event-generation-app.md).
* Percorra as etapas para configurar Application Insights para seu aplicativo com [monitorar e diagnosticar um aplicativo de ASP.NET Core no Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Saiba mais sobre como monitorar a plataforma e os eventos Service Fabric fornece a você: [geração de log e evento de nível de plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Configurar a integração de logs de Azure Monitor com o Service Fabric: [Configurar Logs de Azure monitor para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar logs de Azure Monitor para monitorar contêineres: [monitoramento e diagnóstico para contêineres do Windows no Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Consulte exemplos de problemas de diagnóstico e soluções com Service Fabric: [diagnosticando cenários comuns](service-fabric-diagnostics-common-scenarios.md)
* Saiba mais sobre as recomendações gerais de monitoramento para recursos do Azure: [práticas recomendadas-monitoramento e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).