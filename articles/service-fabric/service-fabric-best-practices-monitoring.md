---
title: Azure Service Fabric monitorizando as melhores práticas
description: Boas práticas e considerações de design para monitorização de clusters e aplicações usando tecido de serviço Azure.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: af03223e8b007cbd2a00d54c3076056cd110ecc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551821"
---
# <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

[A monitorização e diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) são fundamentais para o desenvolvimento, teste e implantação de cargas de trabalho em qualquer ambiente de nuvem. Por exemplo, pode acompanhar a forma como as suas aplicações são utilizadas, as ações tomadas pela plataforma Service Fabric, a utilização de recursos com contadores de desempenho e a saúde geral do seu cluster. Pode utilizar esta informação para diagnosticar e corrigir problemas e evitar que ocorram no futuro.

## <a name="application-monitoring"></a>Monitorização da aplicação

A monitorização da aplicação rastreia a forma como estão a ser utilizados recursos e componentes da sua aplicação. Monitorize as suas aplicações para garantir que os problemas que impactam os seus utilizadores sejam apanhados. O acompanhamento da aplicação é da responsabilidade de quem desenvolve a aplicação e os seus serviços porque é exclusivo da lógica empresarial da sua aplicação. Recomenda-se que instale a monitorização da aplicação com o [Application Insights,](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet)a ferramenta de monitorização de aplicações do Azure.

## <a name="cluster-monitoring"></a>Monitorização de cluster

Um dos objetivos da Service Fabric é tornar as aplicações resilientes a falhas de hardware. Este objetivo é alcançado através da capacidade dos serviços de sistema da plataforma de detetar problemas de infraestrutura e de falhar rapidamente as cargas de trabalho para outros nós do cluster. Mas e se os próprios serviços do sistema tiverem problemas? Ou se na tentativa de implantar ou mover uma carga de trabalho, as regras para a colocação de serviços são violadas? O Service Fabric fornece diagnósticos para estas e outras questões, para garantir que é informado sobre como a plataforma Service Fabric interage com as suas aplicações, serviços, contentores e nós.

Para os clusters Windows, recomenda-se que instale a monitorização do cluster com [registos](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) do Agente de Diagnóstico e [do Monitor Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Para os clusters Linux, os registos do Monitor Azure são também a ferramenta recomendada para a monitorização da plataforma Azure e da infraestrutura. Os diagnósticos da plataforma Linux requerem uma configuração diferente, como se nota em eventos de cluster de tecido de [serviço linux em Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Monitorização das infraestruturas

Os [registos do Monitor Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) são recomendados para monitorizar os eventos de nível de cluster. Assim que configurar o agente Log Analytics com o seu espaço de trabalho descrito no link anterior, poderá recolher métricas de desempenho como a Utilização do CPU, contadores de desempenho .NET, tais como a utilização do CPU nível de processo, contadores de desempenho do Tecido de Serviço, tais como # de exceções de um serviço fiável, e métricas de contentores como a Utilização do CPU.  Terá de escrever registos de contentores para stdout ou stderr para que estejam disponíveis em registos do Monitor Azure.

## <a name="watchdogs"></a>Cães de guarda

Geralmente, um cão de guarda é um serviço separado que observa a saúde e a carga através de serviços, pontos finais de pings e reporta eventos de saúde inesperados no cluster. Isto pode ajudar a prevenir erros que podem não ser detetados apenas com base no desempenho de um único serviço. Os watchdogs também são um bom local para hospedar código que executa ação corretiva que não requer interação do utilizador, como limpar ficheiros de registo no armazenamento em determinados intervalos de tempo. Consulte uma implementação do serviço de vigilância de amostras em eventos de [cluster Service Fabric Linux em Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Passos seguintes

* Começar a instrumentar as suas aplicações: [evento de nível de aplicação e geração de registos.](service-fabric-diagnostics-event-generation-app.md)
* Percorra os passos para configurar os Insights de Aplicação para a sua aplicação com [o Monitor e diagnostice uma aplicação ASP.NET Core no Tecido de Serviço](service-fabric-tutorial-monitoring-aspnet.md).
* Saiba mais sobre a monitorização da plataforma e dos eventos que o Service Fabric lhe fornece: evento de [nível de plataforma e geração de registos.](service-fabric-diagnostics-event-generation-infra.md)
* Configure Azure Monitor regista integração com tecido de serviço: [Configurar registos do Monitor Azure para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar registos do Monitor Azure para monitorizar os recipientes: [Monitorização e Diagnóstico para recipientes Windows em tecido](service-fabric-tutorial-monitoring-wincontainers.md)de serviço Azure .
* Veja os problemas e soluções de diagnóstico de diagnóstico com o Tecido de Serviço: diagnóstico de [cenários comuns](service-fabric-diagnostics-common-scenarios.md)
* Conheça as recomendações gerais de monitorização dos recursos Azure: [Boas Práticas - Monitorização e Diagnósticos.](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)