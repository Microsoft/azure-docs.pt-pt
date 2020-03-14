---
title: Visão geral de monitorização e diagnóstico de tecido de serviço Azure
description: Conheça a monitorização e diagnóstico sinuoso para clusters, aplicações e serviços azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: ef77810adfab213845c7824740effc3416d85407
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282487"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorização e diagnóstico sintetizado sintetizar tecido de serviço Azure

Este artigo fornece uma visão geral da monitorização e diagnóstico sintetizado para o Tecido de Serviço Azure. A monitorização e diagnóstico são fundamentais para o desenvolvimento, teste e implantação de cargas de trabalho em qualquer ambiente de nuvem. Por exemplo, pode acompanhar a forma como as suas aplicações são utilizadas, as ações tomadas pela plataforma Service Fabric, a utilização de recursos com contadores de desempenho e a saúde geral do seu cluster. Pode utilizar esta informação para diagnosticar e corrigir problemas e evitar que ocorram no futuro. As próximas secções explicarão brevemente cada área de monitorização do Tecido de Serviço a considerar para cargas de trabalho de produção. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Monitorização da aplicação
A monitorização da aplicação rastreia a forma como estão a ser utilizados recursos e componentes da sua aplicação. Pretende monitorizar as suas aplicações para garantir que os utilizadores de impacto são apanhados. A responsabilidade do acompanhamento da aplicação é dos utilizadores que desenvolvem uma aplicação e dos seus serviços, uma vez que é única na lógica empresarial da sua aplicação. Monitorizar as suas aplicações pode ser útil nos seguintes cenários:
* Quanto tráfego está a passar a minha aplicação? - Precisa de escalar os seus serviços para satisfazer as exigências dos utilizadores ou abordar um potencial estrangulamento na sua aplicação?
* O meu serviço às chamadas de serviço são bem sucedidos e rastreados?
* Que medidas são tomadas pelos utilizadores da minha aplicação? - Recolher telemetria pode orientar o desenvolvimento futuro de funcionalidades e melhores diagnósticos para erros de aplicação
* A minha candidatura está a lançar exceções não manipuladas? 
* O que se passa dentro dos serviços que estão a funcionar dentro dos meus contentores?

O melhor da monitorização de aplicações é que os desenvolvedores podem usar todas as ferramentas e enquadramento que quiserem, uma vez que vive no contexto da sua aplicação! Pode saber mais sobre a solução Azure para monitorização de aplicações com o Azure Monitor - Application Insights em análise de [eventos com Insights](service-fabric-diagnostics-event-analysis-appinsights.md)de Aplicação .
Temos também um tutorial sobre como [configurar isto para .NET Applications](service-fabric-tutorial-monitoring-aspnet.md). Este tutorial passa por cima de como instalar as ferramentas certas, um exemplo para escrever telemetria personalizada na sua aplicação, e visualizar os diagnósticos de aplicação e telemetria no portal Azure. 


## <a name="platform-cluster-monitoring"></a>Monitorização da plataforma (Cluster)
Um utilizador controla o que a telemetria vem da sua aplicação uma vez que um utilizador escreve o código em si, mas e os diagnósticos da plataforma Service Fabric? Um dos objetivos da Service Fabric é manter as aplicações resilientes a falhas de hardware. Este objetivo é alcançado através da capacidade dos serviços de sistema da plataforma de detetar problemas de infraestrutura e de falhar rapidamente as cargas de trabalho para outros nós do cluster. Mas, neste caso em particular, e se os próprios serviços do sistema tiverem problemas? Ou se na tentativa de implantar ou mover uma carga de trabalho, as regras para a colocação de serviços são violadas? O Service Fabric fornece diagnósticos para estes e muito mais para se certificar de que é informado sobre a atividade que ocorre no seu cluster. Alguns cenários de amostra para monitorização de clusters incluem:

O Service Fabric fornece um conjunto abrangente de eventos fora da caixa. Estes [eventos de Tecido de Serviço](service-fabric-diagnostics-events.md) podem ser acedidos através da EventStore ou do canal operacional (canal de evento exposto pela plataforma). 

* Canais de eventos Service Fabric - No Windows, os eventos de Tecido de Serviço estão disponíveis a partir de um único fornecedor de ETW com um conjunto de `logLevelKeywordFilters` relevantes usados para escolher entre os canais Operacionais e Dados e Mensagens - é assim que separamos os eventos de fabricação de serviço de saída para serem filtrados conforme necessário. No Linux, os eventos de Tecido de Serviço passam por LTTng e são colocados numa tabela de armazenamento, de onde podem ser filtrados conforme necessário. Estes canais contêm eventos curados e estruturados que podem ser usados para entender melhor o estado do seu cluster. Os diagnósticos são ativados por padrão no tempo de criação do cluster, que criam uma tabela de Armazenamento Azure onde os eventos destes canais são enviados para que você faça consulta no futuro. 

* EventStore - A EventStore é uma funcionalidade oferecida pela plataforma que fornece eventos da plataforma Service Fabric disponíveis no Service Fabric Explorer e através da REST API. Pode ver uma visão instantânea do que se passa no seu cluster para cada entidade, por exemplo, nó, serviço, aplicação e consulta com base no tempo do evento. Também pode ler mais sobre a EventStore na visão geral da [EventStore](service-fabric-diagnostics-eventstore.md).    

![Loja de Eventos](media/service-fabric-diagnostics-overview/eventstore.png)

Os diagnósticos fornecidos são sob a forma de um conjunto abrangente de eventos fora da caixa. Estes [eventos de Tecido de Serviço](service-fabric-diagnostics-events.md) ilustram ações feitas pela plataforma em diferentes entidades como Nós, Aplicações, Serviços, Divisórias, etc. No último cenário acima, se um nó cair, a plataforma emitiria um evento `NodeDown` e poderia ser notificado imediatamente pela sua ferramenta de monitorização de eleição. Outros exemplos comuns incluem `ApplicationUpgradeRollbackStarted` ou `PartitionReconfigured` durante uma falha. **Os mesmos eventos estão disponíveis tanto nos clusters Windows como Linux.**

Os eventos são enviados através de canais padrão tanto no Windows como no Linux e podem ser lidos por qualquer ferramenta de monitorização que os suporte. A solução Azure Monitor é registos Do Monitor Azure. Sinta-se livre para ler mais sobre a nossa integração de [logs Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) que inclui um dashboard operacional personalizado para o seu cluster e algumas consultas de amostra a partir das quais você pode criar alertas. Mais conceitos de monitorização de clusterestão disponíveis em evento de [nível de plataforma e geração de registos.](service-fabric-diagnostics-event-generation-infra.md)

### <a name="health-monitoring"></a>Monitorização da integridade
A plataforma Service Fabric inclui um modelo de saúde, que fornece relatórios de saúde extensíveis para o estado das entidades num cluster. Cada nó, aplicação, serviço, partição, réplica ou instância, tem um estado de saúde continuamente elevado. O estado de saúde pode ser "OK", "Aviso" ou "Erro". Pense nos eventos de Tecido de Serviço como verbos feitos pelo cluster a várias entidades e saúde como um adjetivo para cada entidade. De cada vez que a saúde de uma determinada entidade transita, será também emitido um evento. Desta forma, pode configurar consultas e alertas para eventos de saúde na sua ferramenta de monitorização de eleição, como qualquer outro evento. 

Além disso, até deixamos que os utilizadores sobressaem a saúde para as entidades. Se a sua aplicação estiver a passar por uma atualização e tiver testes de validação a falhar, pode escrever para a Service Fabric Health usando a API de saúde para indicar que a sua aplicação já não é saudável, e o Tecido de Serviço irá automaticamente reverter a atualização! Para mais informações sobre o modelo de saúde, consulte a introdução à monitorização da [saúde do Tecido de Serviço](service-fabric-health-introduction.md)

![Painel de saúde SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Cães de guarda
Geralmente, um cão de guarda é um serviço separado que pode observar a saúde e carregar através de serviços, pontos finais ping, e reportar saúde para qualquer coisa no cluster. Isto pode ajudar a prevenir erros que não seriam detetados com base na visão de um único serviço. Os watchdogs também são um bom local para hospedar código que executa ações corretivas sem interação do utilizador (por exemplo, limpar ficheiros de registo no armazenamento em determinados intervalos de tempo). Você pode encontrar uma implementação de serviço de cão de guarda de amostra [aqui](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitorização das infraestruturas (desempenho)
Agora que cobrimos os diagnósticos na sua aplicação e na plataforma, como sabemos que o hardware está funcionando como esperado? Monitorizar a sua infraestrutura subjacente é uma parte fundamental da compreensão do estado do seu cluster e da utilização dos seus recursos. Medir o desempenho do sistema depende de muitos fatores que podem ser subjetivos dependendo das suas cargas de trabalho. Estes fatores são tipicamente medidos através de contadores de desempenho. Estes contadores de desempenho podem vir de uma variedade de fontes, incluindo o sistema operativo, a estrutura .NET ou a própria plataforma Service Fabric. Alguns cenários em que seriam úteis são

* Estou a usar o meu hardware de forma eficiente? Deseja utilizar o seu hardware a 90% de CPU ou 10% de CPU. Isto vem a calhar ao escalonar o seu cluster, ou otimizar os processos da sua aplicação.
* Posso prever proactivamente as questões das infraestruturas? - muitas questões são precedidas por alterações repentinas (quedas) no desempenho, para que possa utilizar contadores de desempenho como a utilização da rede I/O e cpu para prever e diagnosticar proactivamente as questões.

Uma lista de contadores de desempenho que devem ser recolhidos ao nível da infraestrutura pode ser encontrada nas [métricas](service-fabric-diagnostics-event-generation-perf.md)de Desempenho . 

O Service Fabric também fornece um conjunto de contadores de desempenho para os modelos de programação De Serviços Fiáveis e Atores. Se estiver a utilizar qualquer um destes modelos, estes contadores de desempenho podem obter informações para garantir que os seus atores estão a girar corretamente para cima e para baixo, ou que os seus pedidos de serviço fiáveis estão a ser tratados com rapidez suficiente. Para mais informações, consulte [monitorização para monitorização](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) fiável do serviço e monitorização do [desempenho para atores fiáveis](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

A solução Azure Monitor para recolher estes é o Azure Monitor logs tal como a monitorização ao nível da plataforma. Deve utilizar o [agente Log Analytics](service-fabric-diagnostics-oms-agent.md) para recolher os contadores de desempenho apropriados e vê-los nos registos do Monitor Azure.

## <a name="recommended-setup"></a>Configuração recomendada
Agora que revimos cada área de monitorização e cenários de exemplo, aqui está um resumo das ferramentas de monitorização do Azure e configurado necessário para monitorizar todas as áreas acima. 

* Monitorização de aplicações com [Insights de Aplicação](service-fabric-tutorial-monitoring-aspnet.md)
* Monitorização do cluster com [registos](service-fabric-diagnostics-event-aggregation-wad.md) do Agente de Diagnóstico e [do Monitor Azure](service-fabric-diagnostics-oms-setup.md)
* Monitorização de infraestruturas com [registos do Monitor Azure](service-fabric-diagnostics-oms-agent.md)

Também pode utilizar e modificar o modelo ARM da amostra localizado [aqui](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) para automatizar a implantação de todos os recursos e agentes necessários. 

## <a name="other-logging-solutions"></a>Outras soluções de exploração madeireira

Embora as duas soluções que recomendamos, [os registos](service-fabric-diagnostics-event-analysis-oms.md) do Monitor Azure e [os Insights de Aplicação](service-fabric-diagnostics-event-analysis-appinsights.md) tenham construído em integração com o Service Fabric, muitos eventos são escritos através de fornecedores de ETW e são extensíveis com outras soluções de exploração madeireira. Você também deve olhar para a [Stack Elástica](https://www.elastic.co/products) (especialmente se você estiver considerando executar um cluster em um ambiente offline), [Dynatrace](https://www.dynatrace.com/), ou qualquer outra plataforma da sua preferência. Temos aqui uma lista de [](service-fabric-diagnostics-partners.md)parceiros integrados.

Os pontos-chave para qualquer plataforma que escolha devem incluir o quão confortável está com a interface do utilizador, as capacidades de consulta, as visualizações personalizadas e os dashboards disponíveis, e as ferramentas adicionais que eles fornecem para melhorar a sua experiência de monitorização. 

## <a name="next-steps"></a>Passos Seguintes

* Para começar a instrumentar as suas aplicações, consulte evento de [nível de aplicação e geração de registos.](service-fabric-diagnostics-event-generation-app.md)
* Percorra os passos para configurar os Insights de Aplicação para a sua aplicação com [o Monitor e diagnostice uma aplicação ASP.NET Core no Tecido de Serviço](service-fabric-tutorial-monitoring-aspnet.md).
* Saiba mais sobre a monitorização da plataforma e os eventos que o Service Fabric lhe fornece no [evento de nível da Plataforma e na geração de registos.](service-fabric-diagnostics-event-generation-infra.md)
* Configure a integração de logs do Monitor Azure com o Tecido de Serviço na Configuração de [registos do Monitor Azure para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar registos do Monitor Azure para monitorização de contentores - [Monitorização e Diagnóstico para recipientes Windows em tecido de serviço Azure](service-fabric-tutorial-monitoring-wincontainers.md).
* Consulte problemas e soluções de diagnóstico de diagnóstico com tecido de serviço no [diagnóstico de cenários comuns](service-fabric-diagnostics-common-scenarios.md)
* Confira outros produtos de diagnóstico que se integram com o Fabric de Serviço em [Parceiros](service-fabric-diagnostics-partners.md) de Diagnóstico de Tecido de Serviço
* Conheça as recomendações gerais de monitorização dos recursos Azure - [Boas Práticas - Monitorização e Diagnósticos.](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) 