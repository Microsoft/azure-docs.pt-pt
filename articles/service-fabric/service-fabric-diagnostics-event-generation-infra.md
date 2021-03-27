---
title: Monitorização do nível da plataforma de tecido de serviço Azure
description: Saiba mais sobre eventos e registos de nível de plataforma usados para monitorizar e diagnosticar clusters de tecido de serviço Azure.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: f9104c390ab4115c626beb4759c6b6952d691ca9
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626525"
---
# <a name="monitoring-the-cluster"></a>Monitorização do cluster

É importante monitorizar ao nível do cluster para determinar se o seu hardware e cluster estão ou não a comportar-se como esperado. Embora o Service Fabric possa manter as aplicações em funcionamento durante uma falha de hardware, mas ainda precisa diagnosticar se está a ocorrer um erro numa aplicação ou na infraestrutura subjacente. Também deve monitorizar os seus clusters para planear melhor a capacidade, ajudando nas decisões sobre a adição ou remoção de hardware.

A Service Fabric expõe vários eventos estruturados da plataforma, como [eventos de Service Fabric,](service-fabric-diagnostics-events.md)através da EventStore e de vários canais de log fora da caixa. 

No Windows, os eventos de Service Fabric estão disponíveis a partir de um único fornecedor de ETW com um conjunto de `logLevelKeywordFilters` relevantes usados para escolher entre canais operacionais e de dados & mensagens - esta é a forma como separamos os eventos de Tecido de Serviço de saída para serem filtrados conforme necessário.

* **Operacional** Operações de alto nível realizadas pela Service Fabric e pelo cluster, incluindo eventos para um nó que está a chegar, uma nova aplicação a ser implementada, ou um rollback de upgrade, etc. Consulte aqui a [](service-fabric-diagnostics-event-generation-operational.md)lista completa dos eventos.  

* **Operacional - detalhado**  
Relatórios de saúde e decisões de equilíbrio de carga.

O canal de operação pode ser acedido através de uma variedade de formas, incluindo Registos de Eventos ETW/Windows, o [EventStore](service-fabric-diagnostics-eventstore.md) (disponível no Windows nas versões 6.2 e posteriormente para clusters Windows). O EventStore dá-lhe acesso aos eventos do seu cluster por entidade (entidades incluindo cluster, nós, aplicações, serviços, divisórias, réplicas e contentores) e expõe-nos através de APIs REST e da biblioteca de clientes service Fabric. Utilize a Loja de Eventos para monitorizar os seus clusters dev/teste e para obter uma compreensão pontual do estado dos seus clusters de produção.

* **Mensagens & dados**  
Registos críticos e eventos gerados nas mensagens (atualmente apenas o ReverseProxy) e na via de dados (modelos de serviços fiáveis).

* **Data & Mensagens - detalhada**  
Canal verboso que contém todos os registos não críticos de dados e mensagens no cluster (este canal tem um volume muito elevado de eventos).

Além destes, existem dois canais de EventSource estruturados fornecidos, bem como registos que recolhemos para fins de suporte.

* [Eventos do Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Modelo de programação eventos específicos.

* [Eventos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Programação modelo eventos específicos e contadores de desempenho.

* Registos de suporte  
Registos de sistema gerados por Service Fabric apenas para serem utilizados por nós quando fornecem suporte.

Estes vários canais cobrem a maior parte do registo de nível de plataforma que é recomendado. Para melhorar a exploração a nível da plataforma, considere investir na melhor compreensão do modelo de saúde e adicionar relatórios de saúde **personalizados,** e adicionar contadores de desempenho personalizados para construir uma compreensão em tempo real do impacto dos seus serviços e aplicações no cluster.

Para tirar partido destes registos, é altamente recomendado deixar "Diagnósticos" ativados durante a criação do cluster no Portal Azure. Ao ligar os diagnósticos, quando o cluster é implementado, o Windows Azure Diagnostics é capaz de reconhecer os canais operacionais, fiáveis e de atores fiáveis, e armazenar os dados como explicado mais em [eventos agregados com Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric health and load reporting

A Service Fabric tem o seu próprio modelo de saúde, que é descrito em detalhe nestes artigos:

- [Introdução à monitorização do estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)
- [Comunicar e verificar o estado de funcionamento dos serviços](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Adicionar relatórios de saúde personalizados do Service Fabric](service-fabric-report-health.md)
- [Ver relatórios de saúde do Service Fabric](service-fabric-view-entities-aggregated-health.md)

A monitorização da saúde é fundamental para vários aspetos da exploração de um serviço, especialmente durante uma atualização de aplicação. Após a atualização de cada domínio de atualização do serviço, o domínio de atualização deve passar verificações de saúde antes que a implementação se desaperda para o domínio de atualização seguinte. Se o estado de saúde OK não puder ser alcançado, a implantação é revoada, de modo que a aplicação permanece num estado ok conhecido. Embora alguns clientes possam ser afetados antes de os serviços serem revirados, a maioria dos clientes não vai sentir um problema. Além disso, uma resolução ocorre relativamente rapidamente sem ter que esperar pela ação de um operador humano. Quanto mais controlos de saúde forem incorporados no seu código, mais resiliente é o seu serviço para problemas de implantação.

Outro aspeto da saúde do serviço é reportar métricas do serviço. As métricas são importantes no Tecido de Serviço porque são usadas para equilibrar o uso de recursos. As métricas também podem ser um indicador da saúde do sistema. Por exemplo, você pode ter uma aplicação que tem muitos serviços, e cada instância reporta um pedido por segundo (RPS) métrica. Se um serviço estiver a utilizar mais recursos do que outro serviço, o Service Fabric move instâncias de serviço em torno do cluster, para tentar manter a utilização uniforme dos recursos. Para obter uma explicação mais detalhada de como funciona a utilização dos recursos, consulte [Gerir o consumo de recursos e carregar em Tecido de Serviço com métricas.](service-fabric-cluster-resource-manager-metrics.md)

As métricas também podem ajudar a dar-lhe uma visão de como o seu serviço está a funcionar. Com o tempo, pode utilizar métricas para verificar se o serviço está a funcionar dentro dos parâmetros esperados. Por exemplo, se as tendências mostrarem que às 9:00 da manhã de segunda-feira o RPS médio é de 1.000, então você pode configurar um relatório de saúde que o alerta se o RPS estiver abaixo de 500 ou acima de 1.500. Tudo pode estar perfeitamente bem, mas pode valer a pena ter certeza de que os seus clientes estão tendo uma grande experiência. O seu serviço pode definir um conjunto de métricas que podem ser reportadas para efeitos de verificação de saúde, mas que não afetam o equilíbrio de recursos do cluster. Para isso, coloque o peso métrico em zero. Recomendamos que inicie todas as métricas com um peso de zero e não aumente o peso até ter a certeza de que compreende como o peso das métricas afeta o equilíbrio de recursos para o seu cluster.

> [!TIP]
> Não use muitas métricas ponderadas. Pode ser difícil compreender por que razão as instâncias de serviço estão a ser movidas para equilibrar. Algumas métricas podem ir longe!

Qualquer informação que possa indicar a saúde e o desempenho da sua candidatura é candidata a métricas e relatórios de saúde. **Um contador de desempenho do CPU pode dizer-lhe como o seu nó é utilizado, mas não lhe diz se um determinado serviço é saudável, porque vários serviços podem estar funcionando em um único nó.** Mas, métricas como RPS, itens processados, e solicitar latência tudo pode indicar a saúde de um serviço específico.

## <a name="service-fabric-support-logs"></a>Registos de suporte de tecido de serviço

Se precisar de contactar o suporte da Microsoft para obter ajuda com o seu cluster Azure Service Fabric, os registos de suporte são quase sempre necessários. Se o seu cluster estiver hospedado no Azure, os registos de suporte são configurados e recolhidos automaticamente como parte da criação de um cluster. Os registos são armazenados numa conta de armazenamento dedicada no grupo de recursos do seu cluster. A conta de armazenamento não tem um nome fixo, mas na conta, você vê recipientes blob e tabelas com nomes que começam com *tecido*. Para obter informações sobre a configuração de coleções de registo para um cluster autónomo, consulte Criar e gerir um cluster de tecido de [serviço Azure autónomo](service-fabric-cluster-creation-for-windows-server.md) e [configurações de configuração para um cluster windows autónomo](service-fabric-cluster-manifest.md). Para casos autónomos de Tecido de Serviço, os registos devem ser enviados para uma partilha de ficheiros local. **É-lhe exigido** que tenha estes registos para suporte, mas não se destinam a ser utilizáveis por ninguém fora da equipa de apoio ao cliente da Microsoft.

## <a name="measuring-performance"></a>Desempenho de medição

Medir o desempenho do seu cluster irá ajudá-lo a entender como é capaz de lidar com as decisões de carga e de condução em torno do escalonamento do seu cluster (ver mais sobre escalar um cluster [em Azure](service-fabric-cluster-scale-in-out.md), ou [no local).](service-fabric-cluster-windows-server-add-remove-nodes.md) Os dados de desempenho também são úteis quando comparados com ações que você ou as suas aplicações e serviços podem ter tomado, ao analisar registos no futuro. 

Para obter uma lista de contadores de desempenho a cobrar ao utilizar o Tecido de Serviço, consulte [os Contadores de Desempenho em Tecido de Serviço](service-fabric-diagnostics-event-generation-perf.md)

Aqui estão duas formas comuns de configurar a recolha de dados de desempenho para o seu cluster:

* **Usando um agente**  
Esta é a forma preferida de recolher o desempenho de uma máquina, uma vez que os agentes geralmente têm uma lista de possíveis métricas de desempenho que podem ser recolhidas, e é um processo relativamente fácil de escolher as métricas que pretende recolher ou alterar. O The Read about the Azure Monitor oferece registos Azure Monitor na integração de registos do Service Fabric's [Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) e [configura o agente Log Analytics](../azure-monitor/agents/agent-windows.md) para saber mais sobre o agente Log Analytics, que é um desses agentes de monitorização que é capaz de recolher dados de desempenho para VMs de cluster e contentores implantados.

* **Contadores de desempenho para armazenamento de mesa Azure**  
Também pode enviar métricas de desempenho para o mesmo armazenamento de mesa que os eventos. Isto requer alterar a configuração Azure Diagnostics para recolher os contadores de desempenho apropriados dos VMs no seu cluster, e permitir-lhe recolher estatísticas de estivadores se estiver a implantar quaisquer contentores. Leia sobre a configuração [dos contadores de desempenho em WAD](service-fabric-diagnostics-event-aggregation-wad.md) in Service Fabric para configurar a coleção de contadores de desempenho.

## <a name="next-steps"></a>Passos seguintes

* Leia sobre a [integração do Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) do Service Fabric para recolher diagnósticos de clusters e criar consultas e alertas personalizados
* Conheça a Service Fabric's em experiência de diagnóstico incorporada, o [EventStore](service-fabric-diagnostics-eventstore.md)
* Caminhe por [alguns cenários de diagnóstico comuns](service-fabric-diagnostics-common-scenarios.md) em Tecido de Serviço
