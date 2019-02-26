---
title: O Azure Service Fabric, melhores práticas de monitorização | Documentos da Microsoft
description: Melhores práticas para a monitorização de aplicações e clusters do Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 9783c7f2837619d9b279a645668d06d9e0a144bb
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804786"
---
# <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

[Monitorização e diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) são fundamentais para desenvolver, testar e implementar cargas de trabalho em qualquer ambiente de cloud. Por exemplo, pode controlar como os seus aplicativos são usados, as ações executadas pela plataforma do Service Fabric, a utilização de recursos com contadores de desempenho e o estado de funcionamento global do seu cluster. Pode usar essas informações para diagnosticar e corrigir problemas e impedir que ocorra no futuro.

## <a name="application-monitoring"></a>Monitorização da aplicação

Monitorização de aplicações controla como os recursos e componentes da sua aplicação estão sendo usados. Monitorize as aplicações para se certificar de que os problemas que afetem os seus utilizadores são detectados. Monitorização de aplicações é de responsabilidade desses desenvolver o aplicativo e seus serviços, porque é exclusivo para a lógica de negócios da sua aplicação. Recomenda-se que configure a monitorização de aplicações com [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), ferramenta de monitorização da aplicação do Azure.

## <a name="cluster-monitoring"></a>Monitorização de cluster

Um dos objetivos do Service Fabric é tornar as aplicações resilientes a falhas de hardware. Essa meta é alcançada através de capacidade dos serviços do sistema da plataforma para detetar problemas de infraestrutura e, rapidamente, com cargas de trabalho de ativação pós-falha para outros nós do cluster. Mas e se os serviços do sistema se tem problemas? Ou, em caso de tentar implementar ou mover uma carga de trabalho, são violou regras para o posicionamento de serviços? O Service Fabric fornece diagnósticos para estes e outros problemas, para se certificar de que é informado sobre como a plataforma do Service Fabric interage com seus aplicativos, serviços, contentores e nós.

Para clusters do Windows, recomenda-se que configure a monitorização de clusters com [agente de diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) e [do Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Para clusters do Linux, do Log Analytics também é a ferramenta recomendada para a plataforma do Azure e o monitoramento da infraestrutura. Diagnóstico de plataforma Linux necessita de configuração diferente, segundo observado na [eventos de cluster do Service Fabric do Linux do Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Monitorização das infraestruturas

[Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) é recomendada para a monitorização de eventos de nível de cluster. Depois de configurar o agente Log Analytics com a sua área de trabalho, tal como descrito na ligação anterior, poderá recolher métricas de desempenho, como a utilização de CPU, contadores de desempenho do .NET, como o processo ao nível de utilização da CPU, desempenho do Service Fabric contadores como n. º de exceções de um serviço fiável e métricas de contentor, como a utilização da CPU.  Terá de escrever os registos de contentor para stdout ou stderr para que elas estarão disponíveis no Log Analytics.

## <a name="watchdogs"></a>Watchdogs

Em geral, um watchdog é um serviço separado que observa o estado de funcionamento e a carga nos serviços, faz o ping pontos finais e relatórios de eventos de estado de funcionamento inesperado no cluster. Isso pode ajudar a evitar erros que poderão não ser detetados com base apenas no desempenho de um único serviço. Watchdogs também são um bom lugar para código de anfitrião que executa a ação corretiva que não exigem a interação do usuário, como a limpeza dos ficheiros de registo no armazenamento em determinados intervalos de tempo. Veja uma implementação de serviços de watchdog de exemplo na [eventos de cluster do Service Fabric do Linux do Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Passos Seguintes

* Introdução ao instrumentar seus aplicativos: [Geração de eventos e de registo ao nível no aplicativo](service-fabric-diagnostics-event-generation-app.md).
* Avance para os passos para configurar o Application Insights para a sua aplicação com [monitorizar e diagnosticar uma aplicação ASP.NET Core no Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Saiba mais sobre a monitorização da plataforma e os eventos que o Service Fabric fornece para: [Geração de eventos e de registo ao nível no plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Configure a integração do Log Analytics com o Service Fabric: [Configurar o Log Analytics para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar o Log Analytics para monitorizar contentores: [Monitorização e diagnósticos para contentores do Windows no Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Veja os problemas de diagnóstico de exemplo e soluções com o Service Fabric: [cenários comuns de diagnóstico](service-fabric-diagnostics-common-scenarios.md)
* Saiba mais sobre as recomendações gerais da monitorização para recursos do Azure: [Melhores práticas - monitorização e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).