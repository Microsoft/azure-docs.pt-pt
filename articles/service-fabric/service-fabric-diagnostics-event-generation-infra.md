---
title: Monitoramento no nível da plataforma de Service Fabric do Azure
description: Saiba mais sobre eventos e logs de nível de plataforma usados para monitorar e diagnosticar clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 720cc157111293146b796f8567f94a4f1f4830c6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75376941"
---
# <a name="monitoring-the-cluster"></a>Monitorando o cluster

É importante monitorar no nível do cluster para determinar se seu hardware e cluster estão se comportando conforme o esperado. Embora Service Fabric possa manter os aplicativos em execução durante uma falha de hardware, mas você ainda precisará diagnosticar se um erro está ocorrendo em um aplicativo ou na infraestrutura subjacente. Você também deve monitorar seus clusters para melhor planejar a capacidade, ajudando em decisões sobre como adicionar ou remover hardware.

O Service Fabric expõe vários eventos de plataforma estruturados, como [Service Fabric eventos](service-fabric-diagnostics-events.md), por meio do EventStore e de vários canais de log prontos para uso. 

No Windows, Service Fabric eventos estão disponíveis em um único provedor de ETW com um conjunto de `logLevelKeywordFilters` relevantes usado para escolher entre os canais operacionais e de dados & de mensagens – essa é a maneira na qual separamos os eventos de saída Service Fabric a serem filtrados conforme necessário.

* **Operacional** Operações de alto nível executadas por Service Fabric e o cluster, incluindo eventos para um nó surgindo, um novo aplicativo sendo implantado ou uma reversão de atualização, etc. Consulte a lista completa de eventos [aqui](service-fabric-diagnostics-event-generation-operational.md).  

* **Operacional-detalhado**  
Relatórios de integridade e decisões de balanceamento de carga.

O canal de operação pode ser acessado por meio de várias maneiras, incluindo logs de eventos ETW/Windows, o [EventStore](service-fabric-diagnostics-eventstore.md) (disponível no Windows nas versões 6,2 e posteriores para clusters do Windows). O EventStore fornece acesso aos eventos do cluster por entidade (entidades, incluindo cluster, nós, aplicativos, serviços, partições, réplicas e contêineres) e os expõe por meio de APIs REST e da biblioteca de cliente Service Fabric. Use o EventStore para monitorar seus clusters de desenvolvimento/teste e para obter uma compreensão pontual do estado de seus clusters de produção.

* **Mensagens de & de dados**  
Logs críticos e eventos gerados no sistema de mensagens (atualmente, apenas o ReverseProxy) e o caminho de dados (modelos de serviços confiáveis).

* **Dados & mensagens-detalhados**  
Canal detalhado que contém todos os logs não críticos de dados e mensagens no cluster (esse canal tem um volume muito alto de eventos).

Além disso, há dois canais de EventSource estruturados fornecidos, bem como os logs que coletamos para fins de suporte.

* [Eventos do Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Eventos específicos do modelo de programação.

* [Eventos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Eventos específicos do modelo de programação e contadores de desempenho.

* Logs de suporte  
Logs do sistema gerados por Service Fabric apenas para serem usados por nós ao fornecer suporte.

Esses vários canais abrangem a maior parte do log de nível de plataforma recomendado. Para melhorar o registro em log de nível de plataforma, considere investir em um melhor entendimento do modelo de integridade e adicionar relatórios de integridade personalizados e adicionar **contadores de desempenho** personalizados para criar uma compreensão em tempo real do impacto de seus serviços e aplicativos no cluster.

Para aproveitar esses logs, é altamente recomendável deixar o "diagnóstico" habilitado durante a criação do cluster no portal do Azure. Ao ativar o diagnóstico, quando o cluster é implantado, o Windows Diagnóstico do Azure é capaz de reconhecer os canais operacionais, Reliable Services e Reliable Actors e armazená-los conforme explicado em [eventos de agregação com diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Relatório de carga e integridade do Azure Service Fabric

Service Fabric tem seu próprio modelo de integridade, que é descrito em detalhes nestes artigos:

- [Introdução ao monitoramento de integridade Service Fabric](service-fabric-health-introduction.md)
- [Comunicar e verificar o estado de funcionamento dos serviços](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Adicionar relatórios de integridade de Service Fabric personalizados](service-fabric-report-health.md)
- [Exibir Service Fabric relatórios de integridade](service-fabric-view-entities-aggregated-health.md)

O monitoramento de integridade é essencial para vários aspectos da operação de um serviço, especialmente durante uma atualização de aplicativo. Depois que cada domínio de atualização do serviço é atualizado, o domínio de atualização deve passar verificações de integridade antes que a implantação passe para o próximo domínio de atualização. Se o status de integridade OK não puder ser obtido, a implantação será revertida, de modo que o aplicativo permaneça em um estado OK conhecido. Embora alguns clientes possam ser afetados antes que os serviços sejam revertidos, a maioria dos clientes não enfrentará um problema. Além disso, uma resolução ocorre relativamente rapidamente, sem a necessidade de aguardar a ação de um operador humano. Quanto mais verificações de integridade forem incorporadas ao seu código, mais resiliente o serviço será para problemas de implantação.

Outro aspecto da integridade do serviço é o relatório de métricas do serviço. As métricas são importantes na Service Fabric porque são usadas para balancear o uso de recursos. As métricas também podem ser um indicador de integridade do sistema. Por exemplo, você pode ter um aplicativo que tem muitos serviços e cada instância relata uma métrica de solicitações por segundo (RPS). Se um serviço estiver usando mais recursos do que outro serviço, Service Fabric moverá as instâncias de serviço ao cluster, para tentar manter a utilização de recursos uniforme. Para obter uma explicação mais detalhada de como funciona a utilização de recursos, consulte [gerenciar o consumo de recursos e a carga em Service Fabric com métricas](service-fabric-cluster-resource-manager-metrics.md).

As métricas também podem ajudar a fornecer informações sobre como o serviço está sendo executado. Ao longo do tempo, você pode usar métricas para verificar se o serviço está operando dentro dos parâmetros esperados. Por exemplo, se as tendências mostrarem que às 9h na segunda-feira de manhã a média do RPS é 1.000, você poderá configurar um relatório de integridade que o alertará se o RPS estiver abaixo de 500 ou acima de 1.500. Tudo pode ser perfeitamente bom, mas pode valer a pena verificar se os clientes estão tendo uma ótima experiência. O serviço pode definir um conjunto de métricas que podem ser relatadas para fins de verificação de integridade, mas que não afetam o balanceamento de recursos do cluster. Para fazer isso, defina o peso da métrica como zero. Recomendamos que você inicie todas as métricas com um peso de zero e não aumente o peso até ter certeza de que você entende como ponderar as métricas afeta o balanceamento de recursos para o cluster.

> [!TIP]
> Não use muitas métricas ponderadas. Pode ser difícil entender por que as instâncias de serviço estão sendo movidas para balanceamento. Algumas métricas podem ir muito para um longo caminho!

Qualquer informação que possa indicar a integridade e o desempenho do seu aplicativo é um candidato para relatórios de métricas e de integridade. **Um contador de desempenho de CPU pode informar como seu nó é utilizado, mas não informa se um serviço específico está íntegro, pois vários serviços podem estar em execução em um único nó.** Mas, as métricas como RPS, itens processados e latência de solicitação podem indicar a integridade de um serviço específico.

## <a name="service-fabric-support-logs"></a>Service Fabric logs de suporte

Se você precisar entrar em contato com o suporte da Microsoft para obter ajuda com o cluster de Service Fabric do Azure, os logs de suporte são quase sempre necessários. Se o cluster estiver hospedado no Azure, os logs de suporte serão automaticamente configurados e coletados como parte da criação de um cluster. Os logs são armazenados em uma conta de armazenamento dedicada no grupo de recursos do cluster. A conta de armazenamento não tem um nome fixo, mas na conta, você vê contêineres de BLOB e tabelas com nomes que começam com *malha*. Para obter informações sobre como configurar coleções de log para um cluster autônomo, consulte [criar e gerenciar um cluster autônomo do Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) e [definições de configuração para um cluster autônomo do Windows](service-fabric-cluster-manifest.md). Para instâncias Service Fabric autônomas, os logs devem ser enviados a um compartilhamento de arquivos local. Você precisa **ter** esses logs para obter suporte, mas eles não se destinam a ser utilizáveis por qualquer pessoa fora da equipe de suporte ao cliente da Microsoft.

## <a name="measuring-performance"></a>Medindo o desempenho

Medir o desempenho do cluster ajudará você a entender como ele é capaz de lidar com a carga e tomar decisões relacionadas ao dimensionamento do cluster (veja mais informações sobre como dimensionar um cluster [no Azure](service-fabric-cluster-scale-up-down.md)ou [localmente](service-fabric-cluster-windows-server-add-remove-nodes.md)). Os dados de desempenho também são úteis quando comparados com as ações que você ou seus aplicativos e serviços podem ter feito, ao analisar os logs no futuro. 

Para obter uma lista de contadores de desempenho a serem coletados ao usar Service Fabric, consulte [contadores de desempenho no Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Aqui estão duas maneiras comuns em que você pode configurar a coleta de dados de desempenho para seu cluster:

* **Usando um agente**  
Essa é a maneira preferida de coletar o desempenho de um computador, uma vez que os agentes geralmente têm uma lista de possíveis métricas de desempenho que podem ser coletadas e é um processo relativamente fácil de escolher as métricas que você deseja coletar ou alterar. A leitura sobre o Azure Monitor oferece Azure Monitor logs na integração dos [logs de Azure monitor](service-fabric-diagnostics-event-analysis-oms.md) do Service Fabric e [a configuração do agente de log Analytics](../log-analytics/log-analytics-windows-agent.md) para saber mais sobre o agente de log Analytics, que é um agente de monitoramento que é capaz de obter dados de desempenho para VMs de cluster e contêineres implantados.

* **Contadores de desempenho para o armazenamento de tabelas do Azure**  
Você também pode enviar métricas de desempenho para o mesmo armazenamento de tabela que os eventos. Isso requer a alteração da configuração de Diagnóstico do Azure para obter os contadores de desempenho apropriados das VMs em seu cluster e permitir que ele pegue as estatísticas do Docker se você estiver implantando qualquer contêiner. Leia sobre como configurar [contadores de desempenho no wad](service-fabric-diagnostics-event-aggregation-wad.md) em Service Fabric para configurar a coleta do contador de desempenho.

## <a name="next-steps"></a>Passos seguintes

* Leia sobre a [integração dos logs de Azure monitor](service-fabric-diagnostics-event-analysis-oms.md) do Service Fabric para coletar diagnósticos de cluster e criar consultas e alertas personalizados
* Saiba mais sobre a experiência de diagnóstico interna do Service Fabric, o [EventStore](service-fabric-diagnostics-eventstore.md)
* Percorra alguns [cenários comuns de diagnóstico](service-fabric-diagnostics-common-scenarios.md) em Service Fabric
