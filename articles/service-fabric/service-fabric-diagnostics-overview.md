---
title: Visão geral de monitoramento e diagnóstico de Service Fabric do Azure
description: Saiba mais sobre monitoramento e diagnóstico para clusters, aplicativos e serviços do Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: ef77810adfab213845c7824740effc3416d85407
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75349702"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoramento e diagnóstico para o Azure Service Fabric

Este artigo fornece uma visão geral do monitoramento e diagnóstico para o Azure Service Fabric. O monitoramento e o diagnóstico são essenciais para o desenvolvimento, o teste e a implantação de cargas de trabalho em qualquer ambiente de nuvem. Por exemplo, você pode acompanhar como seus aplicativos são usados, as ações tomadas pela plataforma Service Fabric, a utilização de recursos com contadores de desempenho e a integridade geral do cluster. Você pode usar essas informações para diagnosticar e corrigir problemas e impedir que eles ocorram no futuro. As próximas seções explicarão brevemente cada área do monitoramento de Service Fabric para considerar as cargas de trabalho de produção. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Monitorização da aplicação
O monitoramento de aplicativos controla como os recursos e componentes do seu aplicativo estão sendo usados. Você deseja monitorar seus aplicativos para verificar se os problemas que afetam os usuários são detectados. A responsabilidade do monitoramento de aplicativos é que os usuários que desenvolvem um aplicativo e seus serviços, pois são exclusivos da lógica de negócios do seu aplicativo. O monitoramento de seus aplicativos pode ser útil nos seguintes cenários:
* Quanto tráfego meu aplicativo está experimentando? -Você precisa dimensionar seus serviços para atender às demandas dos usuários ou resolver um afunilamento potencial em seu aplicativo?
* Minhas chamadas de serviço para serviço são bem-sucedidas e acompanhadas?
* Quais ações são tomadas pelos usuários do meu aplicativo? -A coleta de telemetria pode orientar o desenvolvimento futuro de recursos e melhor diagnóstico para erros de aplicativo
* Meu aplicativo está lançando exceções sem tratamento? 
* O que está acontecendo nos serviços em execução dentro de meus contêineres?

A grande vantagem do monitoramento de aplicativos é que os desenvolvedores podem usar qualquer ferramenta e estrutura que desejarem desde que ele resida dentro do contexto do seu aplicativo! Você pode saber mais sobre a solução do Azure para monitoramento de aplicativos com Azure Monitor Application Insights na [análise de eventos com o Application insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Também temos um tutorial sobre como configurá- [lo para aplicativos .net](service-fabric-tutorial-monitoring-aspnet.md). Este tutorial vai sobre como instalar as ferramentas corretas, um exemplo para escrever telemetria personalizada em seu aplicativo e exibir o Application Diagnostics e a telemetria no portal do Azure. 


## <a name="platform-cluster-monitoring"></a>Monitoramento de plataforma (cluster)
Um usuário está no controle sobre o que a telemetria vem de seu aplicativo, uma vez que um usuário escreve o código em si, mas e quanto ao diagnóstico da plataforma Service Fabric? Uma das metas de Service Fabric é manter os aplicativos resistentes a falhas de hardware. Essa meta é obtida por meio da capacidade dos serviços do sistema da plataforma de detectar problemas de infraestrutura e fazer failover rápido de cargas de trabalho para outros nós no cluster. Mas neste caso em particular, e se os próprios serviços do sistema tiverem problemas? Ou se estiver tentando implantar ou mover uma carga de trabalho, as regras para o posicionamento dos serviços serão violadas? Service Fabric fornece diagnósticos para esses e mais para garantir que você seja informado sobre a atividade em andamento no cluster. Alguns cenários de exemplo para o monitoramento de cluster incluem:

Service Fabric fornece um conjunto abrangente de eventos prontos para uso. Esses [Service Fabric eventos](service-fabric-diagnostics-events.md) podem ser acessados por meio do EventStore ou do canal operacional (canal de evento exposto pela plataforma). 

* Service Fabric canais de eventos – no Windows, os eventos de Service Fabric estão disponíveis em um único provedor de ETW com um conjunto de `logLevelKeywordFilters` relevantes usado para escolher entre os canais operacionais e de dados & de mensagens – essa é a maneira na qual separamos os eventos de Service Fabric de saída a serem filtrados conforme necessário. No Linux, Service Fabric eventos são enviados por meio de LTTng e colocados em uma tabela de armazenamento, de onde eles podem ser filtrados conforme necessário. Esses canais contêm eventos estruturados, que podem ser usados para entender melhor o estado do cluster. Os diagnósticos são habilitados por padrão no momento da criação do cluster, que criam uma tabela de armazenamento do Azure em que os eventos desses canais são enviados para consulta no futuro. 

* EventStore-o EventStore é um recurso oferecido pela plataforma que fornece Service Fabric eventos de plataforma disponíveis no Service Fabric Explorer e por meio da API REST. Você pode ver uma exibição de instantâneo do que está acontecendo no cluster para cada entidade, por exemplo, nó, serviço, aplicativo e consulta com base na hora do evento. Você também pode ler mais sobre o EventStore na [visão geral do EventStore](service-fabric-diagnostics-eventstore.md).    

![EventStore](media/service-fabric-diagnostics-overview/eventstore.png)

Os diagnósticos fornecidos estão na forma de um conjunto abrangente de eventos prontos para uso. Esses [Service Fabric eventos](service-fabric-diagnostics-events.md) ilustram ações executadas pela plataforma em entidades diferentes, como nós, aplicativos, serviços, partições, etc. No último cenário acima, se um nó fosse desativado, a plataforma emitiria um evento de `NodeDown` e você poderá ser notificado imediatamente pela ferramenta de monitoramento de sua escolha. Outros exemplos comuns incluem `ApplicationUpgradeRollbackStarted` ou `PartitionReconfigured` durante um failover. **Os mesmos eventos estão disponíveis em clusters do Windows e do Linux.**

Os eventos são enviados por meio de canais padrão no Windows e no Linux e podem ser lidos por qualquer ferramenta de monitoramento que ofereça suporte a eles. A solução de Azure Monitor é Azure Monitor logs. Fique à vontade para ler mais sobre nossa [integração de logs de Azure monitor](service-fabric-diagnostics-event-analysis-oms.md) , que inclui um painel operacional personalizado para seu cluster e algumas consultas de exemplo das quais você pode criar alertas. Mais conceitos de monitoramento de cluster estão disponíveis em [geração de log e eventos de nível de plataforma](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Monitorização do estado de funcionamento
A plataforma Service Fabric inclui um modelo de integridade, que fornece relatórios de integridade extensível para o status de entidades em um cluster. Cada nó, aplicativo, serviço, partição, réplica ou instância tem um status de integridade continuamente atualizável. O status de integridade pode ser "OK", "aviso" ou "erro". Imagine Service Fabric eventos como verbos feitos pelo cluster para várias entidades e integridade como um adjetivo para cada entidade. Cada vez que a integridade de uma entidade específica é transferida, um evento também será emitido. Dessa forma, você pode configurar consultas e alertas para eventos de integridade na ferramenta de monitoramento de sua escolha, assim como qualquer outro evento. 

Além disso, até mesmo permitimos que os usuários substituam a integridade de entidades. Se seu aplicativo estiver passando por uma atualização e você tiver testes de validação com falha, você poderá gravar no Service Fabric Health usando a API de integridade para indicar que seu aplicativo não está mais íntegro e Service Fabric irá reverter automaticamente a atualização! Para obter mais informações sobre o modelo de integridade, confira a [introdução ao monitoramento de integridade Service Fabric](service-fabric-health-introduction.md)

![Painel de integridade SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Watchdogs
Geralmente, um Watchdog é um serviço separado que pode observar a integridade e a carga entre os serviços, os pontos de extremidade de ping e a integridade do relatório para qualquer coisa no cluster. Isso pode ajudar a evitar erros que não seriam detectados com base na exibição de um único serviço. Os Watchdogs também são um bom local para hospedar código que executa ações corretivas sem interação do usuário (por exemplo, limpar arquivos de log no armazenamento em determinados intervalos de tempo). Você pode encontrar uma implementação de serviço WatchDog de exemplo [aqui](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitoramento de infraestrutura (desempenho)
Agora que abordamos o diagnóstico em seu aplicativo e a plataforma, como sabemos que o hardware está funcionando conforme o esperado? Monitorar sua infraestrutura subjacente é uma parte fundamental da compreensão do estado do seu cluster e da utilização de recursos. Medir o desempenho do sistema depende de muitos fatores que podem ser subjetivos dependendo de suas cargas de trabalho. Esses fatores normalmente são medidos por meio de contadores de desempenho. Esses contadores de desempenho podem vir de uma variedade de fontes, incluindo o sistema operacional, o .NET Framework ou a própria plataforma de Service Fabric. Alguns cenários nos quais eles seriam úteis são

* Estou utilizando meu hardware com eficiência? Você deseja usar seu hardware em 90% de CPU ou 10% da CPU. Isso é útil ao dimensionar o cluster ou otimizar os processos do aplicativo.
* Posso prever problemas de infraestrutura de forma proativa? -muitos problemas são precedidos por alterações repentinas (quedas) no desempenho, para que você possa usar contadores de desempenho, como e/s de rede e utilização de CPU, para prever e diagnosticar os problemas de forma proativa.

Uma lista de contadores de desempenho que devem ser coletados no nível de infraestrutura pode ser encontrada em [métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric também fornece um conjunto de contadores de desempenho para os modelos de programação Reliable Services e atores. Se você estiver usando qualquer um desses modelos, esses contadores de desempenho poderão obter informações para garantir que seus atores estejam girando ou reduzindo corretamente ou que suas solicitações de serviço confiáveis estejam sendo tratadas com rapidez suficiente. Para obter mais informações, consulte [monitorando a comunicação remota do serviço confiável](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) e [monitoramento de desempenho para Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

A solução Azure Monitor para coletar esses é Azure Monitor logs, assim como o monitoramento em nível de plataforma. Você deve usar o [agente de log Analytics](service-fabric-diagnostics-oms-agent.md) para coletar os contadores de desempenho apropriados e exibi-los em logs de Azure monitor.

## <a name="recommended-setup"></a>Configuração recomendada
Agora que passamos por cada área de cenários de monitoramento e de exemplo, aqui está um resumo das ferramentas de monitoramento do Azure e a configuração necessária para monitorar todas as áreas acima. 

* Monitoramento de aplicativos com o [Application insights](service-fabric-tutorial-monitoring-aspnet.md)
* Monitoramento de cluster com o [agente de diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md) e [logs de Azure monitor](service-fabric-diagnostics-oms-setup.md)
* Monitoramento de infraestrutura com [logs de Azure monitor](service-fabric-diagnostics-oms-agent.md)

Você também pode usar e modificar o modelo ARM de exemplo localizado [aqui](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) para automatizar a implantação de todos os recursos e agentes necessários. 

## <a name="other-logging-solutions"></a>Outras soluções de registro em log

Embora as duas soluções que recomendamos, [Azure monitor logs](service-fabric-diagnostics-event-analysis-oms.md) e [Application insights](service-fabric-diagnostics-event-analysis-appinsights.md) tenham integração interna com o Service Fabric, muitos eventos são criados por meio de provedores de ETW e são extensíveis com outras soluções de registro em log. Você também deve examinar a [pilha elástica](https://www.elastic.co/products) (especialmente se estiver considerando a execução de um cluster em um ambiente offline), [dynaTrace](https://www.dynatrace.com/)ou qualquer outra plataforma de sua preferência. Temos uma lista de parceiros integrados disponíveis [aqui](service-fabric-diagnostics-partners.md).

Os pontos principais para qualquer plataforma que você escolher devem incluir o quão confortável você está com a interface do usuário, os recursos de consulta, as visualizações e os painéis personalizados disponíveis e as ferramentas adicionais que eles fornecem para aprimorar sua experiência de monitoramento. 

## <a name="next-steps"></a>Passos seguintes

* Para começar a instrumentar seus aplicativos, consulte [geração de log e evento de nível de aplicativo](service-fabric-diagnostics-event-generation-app.md).
* Percorra as etapas para configurar Application Insights para seu aplicativo com [monitorar e diagnosticar um aplicativo de ASP.NET Core no Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Saiba mais sobre como monitorar a plataforma e os eventos Service Fabric fornece para você em [geração de log e evento de nível de plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Configurar a integração de logs de Azure Monitor com o Service Fabric em [Configurar Logs de Azure monitor para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar logs de Azure Monitor para monitoramento de contêineres- [monitoramento e diagnóstico para contêineres do Windows no Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Veja problemas de diagnóstico de exemplo e soluções com Service Fabric em [diagnosticar cenários comuns](service-fabric-diagnostics-common-scenarios.md)
* Confira outros produtos de diagnóstico que se integram com Service Fabric em [parceiros de diagnóstico Service Fabric](service-fabric-diagnostics-partners.md)
* Saiba mais sobre as recomendações gerais de monitoramento para recursos do Azure – [práticas recomendadas-monitoramento e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 