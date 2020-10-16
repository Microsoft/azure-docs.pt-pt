---
title: Visão geral de monitorização e diagnóstico de tecidos de serviço Azure
description: Saiba mais sobre monitorização e diagnóstico de clusters, aplicações e serviços de tecido de serviço Azure Service.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: 25a50a2841a03929804be45be8012f9b5d0457ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91357136"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorização e diagnósticos para O Tecido de Serviço Azure

Este artigo fornece uma visão geral da monitorização e diagnóstico do Azure Service Fabric. A monitorização e os diagnósticos são fundamentais para o desenvolvimento, teste e implantação de cargas de trabalho em qualquer ambiente em nuvem. Por exemplo, pode acompanhar a forma como as suas aplicações são utilizadas, as ações tomadas pela plataforma Service Fabric, a sua utilização de recursos com contadores de desempenho e a saúde geral do seu cluster. Pode utilizar esta informação para diagnosticar e corrigir problemas, e evitar que ocorram no futuro. As próximas secções explicarão brevemente cada área de monitorização do Tecido de Serviço a ter em conta para as cargas de trabalho de produção. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Monitorização da aplicação
A monitorização da aplicação rastreia a forma como as funcionalidades e componentes da sua aplicação estão a ser utilizados. Pretende monitorizar as suas aplicações para garantir que os problemas que os utilizadores de impacto têm impacto são apanhados. A responsabilidade da monitorização de aplicações é dos utilizadores que desenvolvem uma aplicação e seus serviços, uma vez que é exclusiva da lógica de negócio da sua aplicação. A monitorização das suas aplicações pode ser útil nos seguintes cenários:
* Quanto tráfego está a passar pela minha aplicação? - Precisa de escalar os seus serviços para satisfazer as exigências dos utilizadores ou resolver um potencial estrangulamento na sua aplicação?
* O meu serviço às chamadas de serviço é bem sucedido e rastreado?
* Que ações são tomadas pelos utilizadores da minha aplicação? - A recolha de telemetria pode orientar o desenvolvimento de funcionalidades futuras e melhores diagnósticos para erros de aplicação
* A minha candidatura está a abrir exceções? 
* O que se passa dentro dos serviços que estão dentro dos meus contentores?

O melhor da monitorização de aplicações é que os desenvolvedores podem usar todas as ferramentas e enquadramentos que gostariam, uma vez que vive no contexto da sua aplicação! Pode saber mais sobre a solução Azure para monitorização de aplicações com o Azure Monitor - Application Insights em [análise de Eventos com Insights de Aplicação](service-fabric-diagnostics-event-analysis-appinsights.md).
Temos também um tutorial com forma de [configurar isto para aplicações .NET.](service-fabric-tutorial-monitoring-aspnet.md) Este tutorial passa por como instalar as ferramentas certas, um exemplo para escrever telemetria personalizada na sua aplicação, e visualizar os diagnósticos de aplicação e telemetria no portal Azure. 


## <a name="platform-cluster-monitoring"></a>Monitorização da plataforma (Cluster)
Um utilizador está no controlo sobre que telemetria vem da sua aplicação uma vez que um utilizador escreve o código em si, mas e os diagnósticos da plataforma Service Fabric? Um dos objetivos da Service Fabric é manter as aplicações resilientes a falhas de hardware. Este objetivo é alcançado através da capacidade dos serviços de sistema da plataforma de detetar problemas de infraestrutura e rapidamente falhar cargas de trabalho para outros nós no cluster. Mas, neste caso em particular, e se os próprios serviços do sistema tiverem problemas? Ou se na tentativa de implantar ou mover uma carga de trabalho, as regras para a colocação de serviços são violadas? O Service Fabric fornece diagnósticos para estes e muito mais para se certificar de que está informado sobre a atividade que está a ocorrer no seu cluster. Alguns cenários de amostra para monitorização do cluster incluem:

A Service Fabric fornece um conjunto abrangente de eventos fora da caixa. Estes [eventos de Tecido de Serviço](service-fabric-diagnostics-events.md) podem ser acedidos através do EventStore ou do canal operacional (canal de eventos exposto pela plataforma). 

* Canais de eventos de Tecido de Serviço - No Windows, os eventos de Tecido de Serviço estão disponíveis a partir de um único fornecedor de ETW com um conjunto de `logLevelKeywordFilters` relevantes usados para escolher entre canais operacionais e de dados & de mensagens - é assim que separamos os eventos de Tecido de Serviço de saída para serem filtrados conforme necessário. No Linux, os eventos de Service Fabric vêm através do LTTng e são colocados numa mesa de armazenamento, de onde podem ser filtrados conforme necessário. Estes canais contêm eventos curados e estruturados que podem ser usados para entender melhor o estado do seu aglomerado. Os diagnósticos são ativados por padrão no tempo de criação do cluster, que criam uma tabela de Armazenamento Azure onde os eventos destes canais são enviados para que você possa consultar no futuro. 

* EventStore - O EventStore é uma funcionalidade oferecida pela plataforma que fornece eventos da plataforma Service Fabric disponíveis no Service Fabric Explorer e através da REST API. Pode ver uma visão instantânea do que se passa no seu cluster para cada entidade, por exemplo, nó, serviço, aplicação e consulta com base na hora do evento. Também pode ler mais sobre a Loja de Eventos na Visão Geral da Loja de [Eventos.](service-fabric-diagnostics-eventstore.md)    

![O screenshot mostra o separador EVENTOS do Painel De Nodes de vários eventos, incluindo um evento NodeDown.](media/service-fabric-diagnostics-overview/eventstore.png)

Os diagnósticos fornecidos são na forma de um conjunto abrangente de eventos fora da caixa. Estes [eventos de Tecido de Serviço](service-fabric-diagnostics-events.md) ilustram ações feitas pela plataforma em diferentes entidades como Nós, Aplicações, Serviços, Partições, etc. No último cenário acima, se um nó fosse para baixo, a plataforma emitiria um `NodeDown` evento e você poderia ser notificado imediatamente pela sua ferramenta de monitorização de escolha. Outros exemplos comuns incluem `ApplicationUpgradeRollbackStarted` ou `PartitionReconfigured` durante uma falha. **Os mesmos eventos estão disponíveis em clusters Windows e Linux.**

Os eventos são enviados através de canais padrão tanto no Windows como no Linux e podem ser lidos por qualquer ferramenta de monitorização que os suporte. A solução Azure Monitor são registos do Monitor Azure. Sinta-se livre para ler mais sobre a nossa [integração de registos Azure Monitor,](service-fabric-diagnostics-event-analysis-oms.md) que inclui um dashboard operacional personalizado para o seu cluster e algumas consultas de amostras a partir das quais você pode criar alertas. Mais conceitos de monitorização de clusters estão disponíveis no [evento de nível de plataforma e geração de log.](service-fabric-diagnostics-event-generation-infra.md)

### <a name="health-monitoring"></a>Monitorização do estado de funcionamento
A plataforma Service Fabric inclui um modelo de saúde, que fornece relatórios de saúde extensíveis para o estado das entidades num cluster. Cada nó, aplicação, serviço, partição, réplica ou instância, tem um estado de saúde continuamente elevado. O estado de saúde pode ser "OK", "Aviso", ou "Erro". Pense nos eventos do Service Fabric como verbos feitos pelo cluster a várias entidades e saúde como um adjetivo para cada entidade. Cada vez que a saúde de uma determinada entidade transite, um evento também será emitido. Desta forma, pode configurar consultas e alertas para eventos de saúde na sua ferramenta de monitorização de eleição, como qualquer outro evento. 

Além disso, permitimos mesmo que os utilizadores sobreponham a saúde às entidades. Se a sua aplicação estiver a passar por uma atualização e tiver testes de validação a falhar, pode escrever para a Service Fabric Health utilizando a API de Saúde para indicar que a sua aplicação já não é saudável e o Service Fabric irá automaticamente reverter a atualização! Para mais informações sobre o modelo de saúde, confira a [introdução à monitorização de saúde do Service Fabric](service-fabric-health-introduction.md)

![Painel de saúde SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Cães de guarda
Geralmente, um cão de guarda é um serviço separado que pode observar a saúde e carregar através de serviços, ping pontos finais, e reportar saúde para qualquer coisa no cluster. Isto pode ajudar a prevenir erros que não seriam detetados com base na visão de um único serviço. Os watchdogs também são um bom local para hospedar código que executa ações corretivas sem interação do utilizador (por exemplo, limpar ficheiros de registo no armazenamento em determinados intervalos de tempo). Você pode encontrar uma implementação de serviço de cão de guarda de amostra [aqui](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitorização da infraestrutura (desempenho)
Agora que cobrimos os diagnósticos na sua aplicação e na plataforma, como sabemos que o hardware está funcionando como esperado? Monitorizar a sua infraestrutura subjacente é uma parte fundamental da compreensão do estado do seu cluster e da sua utilização de recursos. Medir o desempenho do sistema depende de muitos fatores que podem ser subjetivos dependendo das suas cargas de trabalho. Estes fatores são tipicamente medidos através de contadores de desempenho. Estes contadores de desempenho podem vir de uma variedade de fontes, incluindo o sistema operativo, a estrutura .NET ou a própria plataforma Service Fabric. Alguns cenários em que seriam úteis são

* Estou a usar o meu hardware de forma eficiente? Deseja utilizar o seu hardware a 90% cpU ou 10% CPU. Isto vem a calhar ao escalar o seu cluster, ou otimizar os processos da sua aplicação.
* Posso prever proactivamente as questões das infraestruturas? - muitas questões são precedidas por alterações súbitas (quedas) no desempenho, para que possa utilizar contadores de desempenho como a utilização da rede I/O e da CPU para prever e diagnosticar os problemas de forma proactiva.

Uma lista de contadores de desempenho que devem ser recolhidos ao nível da infraestrutura pode ser encontrada nas [métricas de Desempenho](service-fabric-diagnostics-event-generation-perf.md). 

A Service Fabric também fornece um conjunto de contadores de desempenho para os modelos de programação Reliable Services and Actors. Se estiver a utilizar um destes modelos, estes contadores de desempenho podem obter informações para garantir que os seus atores estão a girar corretamente para cima e para baixo, ou que os seus pedidos de serviço fiáveis estão a ser manuseados com rapidez suficiente. Para obter mais informações, consulte [monitoramento para remoagem de serviços fiáveis](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) e [monitorização de desempenho para atores fiáveis](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

A solução Azure Monitor para recolher estes são registos do Azure Monitor tal como a monitorização do nível da plataforma. Deve utilizar o [agente Log Analytics](service-fabric-diagnostics-oms-agent.md) para recolher os contadores de desempenho apropriados e vê-los nos registos do Azure Monitor.

## <a name="recommended-setup"></a>Configuração recomendada
Agora que passamos por cada área de monitorização e cenários de exemplo, aqui está um resumo das ferramentas de monitorização do Azure e criados necessários para monitorizar todas as áreas acima. 

* Monitorização de aplicações com [Insights de Aplicações](service-fabric-tutorial-monitoring-aspnet.md)
* Monitorização do cluster com [registos de Agente de Diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md) e Monitor [Azure](service-fabric-diagnostics-oms-setup.md)
* Monitorização de infraestruturas com [registos do Monitor Azure](service-fabric-diagnostics-oms-agent.md)

Também pode utilizar e modificar o modelo ARM de amostra localizado [aqui](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) para automatizar a implementação de todos os recursos e agentes necessários. 

## <a name="other-logging-solutions"></a>Outras soluções de registo

Embora as duas soluções que recomendamos, os registos do [Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) e os Application [Insights](service-fabric-diagnostics-event-analysis-appinsights.md) construíram em integração com o Service Fabric, muitos eventos são escritos através de fornecedores da ETW e são extensíveis com outras soluções de registo. Você também deve olhar para a [Pilha Elástica](https://www.elastic.co/products) (especialmente se você estiver pensando executar um cluster em um ambiente offline), [Dynatrace](https://www.dynatrace.com/), ou qualquer outra plataforma da sua preferência. Temos uma lista de parceiros integrados disponíveis [aqui.](service-fabric-diagnostics-partners.md)

Os pontos-chave para qualquer plataforma que escolher devem incluir o quão confortável você está com a interface do utilizador, as capacidades de consulta, as visualizações personalizadas e dashboards disponíveis, e as ferramentas adicionais que eles fornecem para melhorar a sua experiência de monitorização. 

## <a name="next-steps"></a>Passos seguintes

* Para começar a instrumentar as suas aplicações, consulte [o evento de nível de aplicação e a geração de registos.](service-fabric-diagnostics-event-generation-app.md)
* Percora as etapas para configurar o Application Insights para a sua aplicação com [o Monitor e diagnosticar uma aplicação core ASP.NET no Tecido de Serviço.](service-fabric-tutorial-monitoring-aspnet.md)
* Saiba mais sobre a monitorização da plataforma e dos eventos que o Service Fabric fornece para si no [evento ao nível da Plataforma e geração de log.](service-fabric-diagnostics-event-generation-infra.md)
* Configure a integração de registos do Monitor Azure com o Tecido de Serviço na [Configuração de registos do Monitor Azure para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar registos do Azure Monitor para monitorizar contentores - [Monitorização e Diagnóstico de Recipientes windows em tecido de serviço Azure](service-fabric-tutorial-monitoring-wincontainers.md).
* Veja exemplo de problemas e soluções de diagnóstico com Tecido de Serviço no [diagnóstico de cenários comuns](service-fabric-diagnostics-common-scenarios.md)
* Confira outros produtos de diagnóstico que se integram com o Service Fabric in [Service Fabric parceiros de diagnóstico](service-fabric-diagnostics-partners.md)
* Conheça as recomendações gerais de monitorização dos recursos Azure - [Boas Práticas - Monitorização e diagnósticos.](/azure/architecture/best-practices/monitoring) 
