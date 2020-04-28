---
title: Monitorização do nível da plataforma de tecido de serviço Azure
description: Conheça os eventos e registos de nível da plataforma utilizados para monitorizar e diagnosticar clusters de tecido de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 720cc157111293146b796f8567f94a4f1f4830c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75376941"
---
# <a name="monitoring-the-cluster"></a>Monitorização do cluster

É importante monitorizar ao nível do cluster para determinar se o seu hardware e cluster estão ou não a comportar-se como esperado. Embora o Service Fabric possa manter as aplicações em funcionamento durante uma falha de hardware, mas ainda precisa de diagnosticar se está a ocorrer um erro numa aplicação ou na infraestrutura subjacente. Também deve monitorizar os seus clusters para planear melhor a capacidade, ajudando nas decisões sobre a adição ou remoção de hardware.

O Service Fabric expõe vários eventos estruturados da plataforma, como [eventos de Tecido de Serviço,](service-fabric-diagnostics-events.md)através da EventStore e de vários canais de registo fora da caixa. 

No Windows, os eventos service Fabric estão disponíveis a `logLevelKeywordFilters` partir de um único fornecedor de ETW com um conjunto de relevantes usados para escolher entre operacionais e dados & canais de mensagens - é assim que separamos os eventos de fabricação de serviço de saída para serem filtrados conforme necessário.

* **Operacional** Operações de alto nível realizadas pela Service Fabric e pelo cluster, incluindo eventos para um nó que se aproxima, uma nova aplicação a ser implementada, ou uma reversão de atualização, etc. Veja a lista completa de eventos [aqui.](service-fabric-diagnostics-event-generation-operational.md)  

* **Operacional - detalhado**  
Relatórios de saúde e decisões de equilíbrio de carga.

O canal de operação pode ser acedido através de várias formas, incluindo Registos de Eventos ETW/Windows, a [EventStore](service-fabric-diagnostics-eventstore.md) (disponível no Windows nas versões 6.2 e posteriormente para clusters Windows). A EventStore dá-lhe acesso aos eventos do seu cluster numa base de entidade (entidades incluindo cluster, nós, aplicações, serviços, divisórias, réplicas e contentores) e expõe-nos através de APIs REST e da biblioteca de clientes service Fabric. Utilize a EventStore para monitorizar os seus clusters de dev/teste e para obter uma compreensão ponto-a-tempo do estado dos seus clusters de produção.

* **Mensagens de & de dados**  
Registos e eventos críticos gerados nas mensagens (atualmente apenas o ReverseProxy) e na trajetória de dados (modelos de serviços fiáveis).

* **Mensagens de & de dados - detalhadas**  
Canal verboso que contém todos os registos não críticos a partir de dados e mensagens no cluster (este canal tem um volume muito elevado de eventos).

Além destes, existem dois canais estruturados do EventSource fornecidos, bem como registos que recolhemos para fins de suporte.

* [Eventos do Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Modelo de programação eventos específicos.

* [Eventos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Modelo de programação eventos específicos e contadores de desempenho.

* Registos de suporte  
Registos de sistema gerados pelo Tecido de Serviço apenas para serem utilizados por nós ao fornecer suporte.

Estes vários canais cobrem a maior parte do nível de registo de nível da plataforma que é recomendado. Para melhorar o nível da exploração madeireira, considere investir na melhor compreensão do modelo de saúde e na adição de relatórios de saúde **personalizados,** e adicionar contadores de desempenho personalizados para construir uma compreensão em tempo real do impacto dos seus serviços e aplicações no cluster.

Para tirar partido destes registos, é altamente recomendado deixar "Diagnósticos" ativados durante a criação de cluster no Portal Azure. Ao ligar os diagnósticos, quando o cluster é implementado, o Windows Azure Diagnostics é capaz de reconhecer os canais Operacionais, Confiáveis e Atores Fiáveis, e armazenar os dados como explicado em [eventos agregados com diagnósticos Azure.](service-fabric-diagnostics-event-aggregation-wad.md)

## <a name="azure-service-fabric-health-and-load-reporting"></a>Relatório de saúde e carga de tecido de serviço Azure

A Service Fabric tem o seu próprio modelo de saúde, que é descrito em detalhe nestes artigos:

- [Introdução à monitorização do estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)
- [Comunicar e verificar o estado de funcionamento dos serviços](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Adicione relatórios de saúde personalizados de tecido de serviço](service-fabric-report-health.md)
- [Ver relatórios de saúde de tecido de serviço](service-fabric-view-entities-aggregated-health.md)

A monitorização da saúde é fundamental para vários aspetos de funcionamento de um serviço, especialmente durante uma atualização de aplicação. Depois de cada domínio de atualização do serviço ser atualizado, o domínio de atualização deve passar em verificações de saúde antes de a implementação passar para o próximo domínio de atualização. Se o estado de saúde ok não puder ser alcançado, a implementação é retrometo, de modo que a aplicação permaneça num estado OK conhecido. Embora alguns clientes possam ser afetados antes de os serviços serem relançados, a maioria dos clientes não vai sentir um problema. Além disso, uma resolução ocorre relativamente rapidamente sem ter que esperar pela ação de um operador humano. Quanto mais verificações de saúde forem incorporadas no seu código, mais resiliente o seu serviço é para problemas de implantação.

Outro aspeto da saúde do serviço é reportar métricas do serviço. As métricas são importantes no Tecido de Serviço porque são usadas para equilibrar o uso de recursos. As métricas também podem ser um indicador de saúde do sistema. Por exemplo, você pode ter uma aplicação que tem muitos serviços, e cada instância reporta uma métrica de pedidos por segundo (RPS). Se um serviço estiver a utilizar mais recursos do que outro serviço, o Service Fabric move as instâncias de serviço em torno do cluster, para tentar manter a utilização uniforme dos recursos. Para obter uma explicação mais detalhada de como funciona a utilização de recursos, consulte Gerir o [consumo de recursos e carregar em Tecido de Serviço com métricas](service-fabric-cluster-resource-manager-metrics.md).

As métricas também podem ajudar a dar-lhe uma ideia de como o seu serviço está a funcionar. Com o tempo, pode usar métricas para verificar se o serviço está a funcionar dentro dos parâmetros esperados. Por exemplo, se as tendências mostrarem que às 9:00 da manhã de segunda-feira o RPS médio é de 1.000, então você pode configurar um relatório de saúde que o alerta se o RPS estiver abaixo de 500 ou acima de 1.500. Tudo pode estar perfeitamente bem, mas pode valer a pena olhar para ter certeza de que os seus clientes estão tendo uma grande experiência. O seu serviço pode definir um conjunto de métricas que podem ser reportadas para efeitos de verificação de saúde, mas que não afetam o equilíbrio de recursos do cluster. Para isso, coloque o peso métrico em zero. Recomendamos que inicie todas as métricas com um peso de zero e não aumente o peso até ter a certeza de que compreende como a ponderação das métricas afeta o equilíbrio de recursos para o seu cluster.

> [!TIP]
> Não use muitas métricas ponderadas. Pode ser difícil compreender porque é que as instâncias de serviço estão a ser deslocadas para equilibrar. Algumas métricas podem ir longe!

Qualquer informação que possa indicar a saúde e o desempenho da sua candidatura é um candidato a métricas e relatórios de saúde. **Um contador de desempenho da CPU pode dizer-lhe como o seu nó é utilizado, mas não lhe diz se um determinado serviço é saudável, porque vários serviços podem estar funcionando em um único nó.** Mas, métricas como RPS, itens processados, e pedidos de latência todos podem indicar a saúde de um serviço específico.

## <a name="service-fabric-support-logs"></a>Registos de suporte de tecido de serviço

Se necessitar de contactar o suporte da Microsoft para obter ajuda com o seu cluster Azure Service Fabric, os registos de suporte são quase sempre necessários. Se o seu cluster estiver hospedado em Azure, os registos de suporte são automaticamente configurados e recolhidos como parte da criação de um cluster. Os registos são armazenados numa conta de armazenamento dedicada no grupo de recursos do seu cluster. A conta de armazenamento não tem um nome fixo, mas na conta, vê-se recipientes e mesas blob com nomes que começam com *tecido*. Para obter informações sobre a configuração de coleções de registo para um cluster autónomo, consulte [Create e gerencie um cluster](service-fabric-cluster-creation-for-windows-server.md) de tecido de serviço azure autónomo e configurações de [configuração para um cluster Windows autónomo](service-fabric-cluster-manifest.md). Para casos autónomos de tecido de serviço, os registos devem ser enviados para uma partilha de ficheirolocal. **É-lhe exigido** que tenha estes registos para suporte, mas não se destina a ser utilizável por ninguém fora da equipa de apoio ao cliente da Microsoft.

## <a name="measuring-performance"></a>Desempenho de medição

Medir o desempenho do seu cluster irá ajudá-lo a entender como é capaz de lidar com a carga e conduzir decisões em torno da escala do seu cluster (ver mais sobre escalar um cluster [em Azure](service-fabric-cluster-scale-up-down.md), ou [no local).](service-fabric-cluster-windows-server-add-remove-nodes.md) Os dados de desempenho também são úteis quando comparados com as ações que você ou as suas aplicações e serviços podem ter tomado, ao analisar registos no futuro. 

Para obter uma lista de contadores de desempenho para recolher ao utilizar o Tecido de Serviço, consulte Contadores de [Desempenho em Tecido de Serviço](service-fabric-diagnostics-event-generation-perf.md)

Aqui estão duas formas comuns de configurar a recolha de dados de desempenho para o seu cluster:

* **Usando um agente**  
Esta é a forma preferida de recolher o desempenho de uma máquina, uma vez que os agentes geralmente têm uma lista de possíveis métricas de desempenho que podem ser recolhidas, e é um processo relativamente fácil de escolher as métricas que pretende recolher ou alterar. A Leitura sobre o Monitor Azure oferecendo registos do Monitor Azure na [integração](service-fabric-diagnostics-event-analysis-oms.md) de registos do Monitor Azure do Serviço fabrica e [a criação do agente Log Analytics](../log-analytics/log-analytics-windows-agent.md) para saber mais sobre o agente Log Analytics, que é um desses agentes de monitorização que é capaz de recolher dados de desempenho para VMs de cluster e contentores implantados.

* **Contadores de desempenho para armazenamento de mesa Azure**  
Também pode enviar métricas de desempenho para o mesmo armazenamento de mesa que os eventos. Isto requer alterar a configuração do Azure Diagnostics para recolher os contadores de desempenho apropriados dos VMs do seu cluster, e permitir-lhe recolher estatísticas de estivadores se estiver a implantar quaisquer recipientes. Leia sobre configurar contadores de [desempenho em WAD](service-fabric-diagnostics-event-aggregation-wad.md) em Tecido de Serviço para configurar a recolha de contadores de desempenho.

## <a name="next-steps"></a>Passos seguintes

* Leia sobre a [integração de logs azure monitor](service-fabric-diagnostics-event-analysis-oms.md) da Service Fabric para recolher diagnósticos de cluster e criar consultas e alertas personalizados
* Saiba mais sobre o Service Fabric's em experiência de diagnóstico incorporada, a [EventStore](service-fabric-diagnostics-eventstore.md)
* Caminhe por [alguns cenários de diagnóstico comuns](service-fabric-diagnostics-common-scenarios.md) em Tecido de Serviço
