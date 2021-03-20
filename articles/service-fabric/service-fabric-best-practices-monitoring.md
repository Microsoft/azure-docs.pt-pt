---
title: Azure Service Fabric monitorizando as melhores práticas
description: Boas práticas e considerações de design para monitorizar clusters e aplicações usando o Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a7b1c1b3fc3196557b862c488ee01af8b8e1f04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86529255"
---
# <a name="monitoring-and-diagnostic-best-practices-for-azure-service-fabric"></a>Monitorização e diagnóstico das melhores práticas para o Tecido de Serviço Azure

[A monitorização e os diagnósticos](./service-fabric-diagnostics-overview.md) são fundamentais para o desenvolvimento, teste e implantação de cargas de trabalho em qualquer ambiente em nuvem. Por exemplo, pode acompanhar a forma como as suas aplicações são utilizadas, as ações tomadas pela plataforma Service Fabric, a sua utilização de recursos com contadores de desempenho e a saúde geral do seu cluster. Pode utilizar esta informação para diagnosticar e corrigir problemas, e evitar que ocorram no futuro.

## <a name="application-monitoring"></a>Monitorização da aplicação

A monitorização da aplicação rastreia a forma como as funcionalidades e componentes da sua aplicação estão a ser utilizados. Monitorize as suas aplicações para garantir que os problemas que impactam os seus utilizadores são apanhados. A monitorização de aplicações é da responsabilidade de quem desenvolve a aplicação e os seus serviços porque é única na lógica de negócio da sua aplicação. Recomenda-se que crie monitorização de aplicações com o [Application Insights](./service-fabric-tutorial-monitoring-aspnet.md), a ferramenta de monitorização de aplicações da Azure.

## <a name="cluster-monitoring"></a>Monitorização de cluster

Um dos objetivos da Service Fabric é tornar as aplicações resilientes a falhas de hardware. Este objetivo é alcançado através da capacidade dos serviços de sistema da plataforma de detetar problemas de infraestrutura e rapidamente falhar cargas de trabalho para outros nós no cluster. Mas e se os próprios serviços do sistema tiverem problemas? Ou se na tentativa de implantar ou mover uma carga de trabalho, as regras para a colocação de serviços são violadas? A Service Fabric fornece diagnósticos para estes, e outros problemas, para se certificar de que está informado sobre como a plataforma Service Fabric interage com as suas aplicações, serviços, contentores e nós.

Para os clusters windows, recomenda-se que crie monitorização de clusters com [registos de Agente de Diagnóstico](./service-fabric-diagnostics-event-aggregation-wad.md) e Monitor [Azure](./service-fabric-diagnostics-oms-setup.md).

Para os clusters Linux, os registos do Azure Monitor são também a ferramenta recomendada para a plataforma Azure e monitorização de infraestruturas. Os diagnósticos da plataforma Linux requerem uma configuração diferente, como indicado em [eventos de cluster de tecido de serviço Linux no Syslog](./service-fabric-diagnostics-oms-syslog.md).

## <a name="infrastructure-monitoring"></a>Monitorização das infraestruturas

[Os registos do Monitor Azure](./service-fabric-diagnostics-oms-agent.md) são recomendados para monitorizar eventos de nível de cluster. Assim que configurar o agente Log Analytics com o seu espaço de trabalho, tal como descrito no link anterior, poderá recolher métricas de desempenho como cpu Utilization, contadores de desempenho .NET, tais como utilização de CPU de nível de processo, contadores de desempenho do Service Fabric, tais como # de exceções de um serviço fiável, e métricas de contentores como a CPU Utilization.  Terá de escrever registos de contentores para stdout ou stderr para que estejam disponíveis nos registos do Azure Monitor.

## <a name="watchdogs"></a>Cães de guarda

Geralmente, um cão de guarda é um serviço separado que observa a saúde e a carga através de serviços, pontos finais de pings, e relata eventos de saúde inesperados no cluster. Isto pode ajudar a prevenir erros que podem não ser detetados apenas com base no desempenho de um único serviço. Os watchdogs também são um bom local para hospedar código que executa ação corretiva que não requer interação do utilizador, como limpar ficheiros de registo no armazenamento em determinados intervalos de tempo. Consulte uma implementação do serviço de cão de guarda de amostras em  [eventos de cluster De Tecido de Serviço Linux em Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Passos seguintes

* Começa a instrumentar as tuas aplicações: [Evento de nível de aplicação e geração de registos](service-fabric-diagnostics-event-generation-app.md).
* Percora as etapas para configurar o Application Insights para a sua aplicação com [o Monitor e diagnosticar uma aplicação core ASP.NET no Tecido de Serviço.](service-fabric-tutorial-monitoring-aspnet.md)
* Saiba mais sobre a monitorização da plataforma e dos eventos que o Service Fabric fornece para si: [Evento ao nível da plataforma e geração de log.](service-fabric-diagnostics-event-generation-infra.md)
* Configurar a integração de registos do Monitor Azure com o Tecido de Serviço: [Configurar registos do Monitor Azure para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar registos do Azure Monitor para monitorizar recipientes: [Monitorização e Diagnóstico de Recipientes windows em tecido de serviço Azure](service-fabric-tutorial-monitoring-wincontainers.md).
* Veja exemplo de problemas e soluções de diagnóstico com Tecido de Serviço: [diagnóstico de cenários comuns](service-fabric-diagnostics-common-scenarios.md)
* Conheça as recomendações gerais de monitorização dos recursos Azure: [Melhores Práticas - Monitorização e diagnósticos.](/azure/architecture/best-practices/monitoring)
