---
title: Monitorização de nível de plataforma de recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Saiba mais sobre os eventos de nível de plataforma e registos utilizados para monitorizar e diagnosticar os clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 90ec06b01b11b5cbe119f41483eaf794af4e991b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243096"
---
# <a name="monitoring-the-cluster"></a>Monitorizar o cluster

É importante monitorizar no nível do cluster para determinar se é ou não o hardware e o cluster se comportam conforme o esperado. Embora o Service Fabric pode manter as aplicações em execução durante uma falha de hardware, mas ainda precisar de diagnosticar se um erro está ocorrendo num aplicativo ou na infraestrutura subjacente. Também deve monitorar os clusters para melhor planear a capacidade, ajudando a decisões sobre a adição ou remoção de hardware.

Service Fabric expõe vários eventos de plataforma estruturados, como [eventos do Service Fabric](service-fabric-diagnostics-events.md), por meio do EventStore e vários log canais-de-pronta. 

No Windows, eventos do Service Fabric estão disponíveis a partir de um fornecedor ETW único com um conjunto de relevantes `logLevelKeywordFilters` usado para selecionar entre canais operacional e de mensagens & de dados - esta é a maneira na qual vamos separar saídos eventos do Service Fabric a filtrar no conforme necessário.

* **Operacional** alto nível operações executadas pelo Service Fabric e o cluster, incluindo eventos para um nó em breve, uma nova aplicação que está sendo implantado ou reverter uma atualização, etc. Ver a lista completa de eventos [aqui](service-fabric-diagnostics-event-generation-operational.md).  

* **Operacional - detalhado**  
Os relatórios de estado de funcionamento e decisões de balanceamento de carga.

O canal de operação pode ser acessado por meio de diversas formas, incluindo registos de eventos do ETW/Windows, o [EventStore](service-fabric-diagnostics-eventstore.md) (disponível no Windows em versões 6.2 e posterior para clusters do Windows). O EventStore dá-lhe acesso a eventos do seu cluster numa base por entidade (incluindo o cluster, nós, aplicativos, serviços, partições, réplicas e contentores de entidades) e expõe-los por meio de REST APIs e a biblioteca de cliente do Service Fabric. Utilize o EventStore para monitorizar os seus clusters de programador/teste e para obter uma compreensão de ponto no tempo do Estado de seus clusters de produção.

* **& Mensagens de dados**  
Críticos registos e eventos gerados no (atualmente apenas o ReverseProxy) de mensagens e o caminho de dados (modelos de serviços fiáveis).

* **& Mensagens - detalhadas de dados**  
Canal verboso, que contém todos os registos não críticas dos dados e de mensagens do cluster (este canal tem um volume muito elevado de eventos).

Além destas, existem dois canais de EventSource estruturados fornecidos, bem como os registos que recolhemos para fins de suporte.

* [Eventos do Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Eventos específicos do modelo de programação.

* [Eventos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Eventos específicos de modelo de programação e contadores de desempenho.

* Registos de suporte  
Registos de sistema gerados pelo Service Fabric apenas para ser utilizado por nós ao indicar o suporte.

Estes canais vários cobrem a maior parte o registo de nível de plataforma que é recomendado. Para melhorar o registo de nível de plataforma, considerar o investimento na melhor compreender o modelo de estado de funcionamento e adicionar relatórios de estado de funcionamento personalizados e adicionar personalizado **contadores de desempenho** para criar uma compreensão em tempo real do impacto da sua os serviços e aplicações no cluster.

Para aproveitar estes registos, é altamente recomendado deixar "Diagnóstico" ativado durante a criação do cluster no Portal do Azure. Ao ativar diagnósticos, quando o cluster é implementado, Windows Azure Diagnostics é capaz de reconhecer operacional, do Reliable Services e do canais de Reliable actors e armazenar os dados como explicado mais no [agregar eventos com o Azure Diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Estado de funcionamento de recursos de infraestrutura do serviço do Azure e relatórios de carga

Service Fabric tem seu próprio modelo de estado de funcionamento, o que é descrito detalhadamente nestes artigos:

- [Introdução à monitorização de estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)
- [Comunicar e verificar o estado de funcionamento dos serviços](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Adicionar relatórios de estado de funcionamento personalizados do Service Fabric](service-fabric-report-health.md)
- [Ver relatórios de estado de funcionamento do Service Fabric](service-fabric-view-entities-aggregated-health.md)

Monitorização de estado de funcionamento é essencial para vários aspectos de operar um serviço, especialmente durante uma atualização da aplicação. Após cada domínio de atualização do serviço de atualização, o domínio de atualização tem de passar verificações de estado de funcionamento antes da implantação passe para o domínio de atualização seguinte. Se não pode ser obtido o estado de funcionamento OK, a implementação é revertida, para que o aplicativo permanece num estado conhecido de OK. Embora alguns clientes poderão ser afetados antes dos serviços são revertidos, a maioria dos clientes não ocorrerá um problema. Além disso, uma resolução ocorre relativamente rapidamente sem ter de esperar por ação a partir de um operador humano. As verificações de estado de funcionamento mais que estão incorporadas no seu código, o mais resiliente do que seu serviço é para problemas de implantação.

Outro aspecto do Estado de funcionamento do serviço está a comunicar as métricas do serviço. As métricas são importantes nos recursos de infraestrutura do serviço porque eles são usados para equilibrar a utilização de recursos. Métricas também podem ser um indicador de estado de funcionamento do sistema. Por exemplo, poderá ter uma aplicação que tenha vários serviços, e cada instância reporta um pedidos por segundo (RPS) métrica. Se um serviço estiver a utilizar mais recursos do que outro serviço, o Service Fabric move instâncias do serviço em todo o cluster, para tentar manter a utilização de recursos do mesmo. Para obter uma explicação mais detalhada de como funciona a utilização de recursos, consulte [gerenciar o consumo de recursos e carregar no Service Fabric com a métrica](service-fabric-cluster-resource-manager-metrics.md).

Métricas também podem ajudar a dar-lhe informações sobre o desempenho do seu serviço. Ao longo do tempo, pode utilizar métricas para verificar que o serviço está a funcionar dentro de parâmetros esperados. Por exemplo, se tendências mostram que, às 9h na manhã de segunda-feira a média RPS é 1.000, em seguida, pode configurar um relatório de estado de funcionamento que o alerta se o RPS for abaixo 500 ou superiores 1.500. Tudo o que pode ser perfeitamente bem, mas pode ser a pena examinar se certificar de que seus clientes estão a ter uma excelente experiência. O serviço pode definir um conjunto de métricas que pode ser relatada para fins de verificação de estado de funcionamento, mas que não afetam o balanceamento de recurso do cluster. Para tal, defina o peso de métrica a zero. Recomendamos que comece todas as métricas com uma ponderação igual a zero e não aumente o peso até que tem a certeza de que compreende como equilibrar as métricas afeta para o cluster de balanceamento de recurso.

> [!TIP]
> Não utilize demasiados métricas ponderadas. Pode ser difícil de entender por que instâncias de serviço estão sendo movidas para o balanceamento. Algumas métricas podem percorrem um longo caminho!

Todas as informações que podem indicar o estado de funcionamento e o desempenho da sua aplicação são um candidato para relatórios de métricas e estado de funcionamento. **Um contador de desempenho da CPU pode informar a como o seu nó está utilizado, mas ele não diz a se um determinado serviço está em bom estado, uma vez que vários serviços podem estar em execução num único nó.** No entanto, as métricas, como RPS, itens processados, e latência de pedidos de todos os pode indicar o estado de funcionamento de um serviço específico.

## <a name="service-fabric-support-logs"></a>Registos de suporte do Service Fabric

Se precisar de contactar o suporte da Microsoft para obter ajuda com o seu cluster do Azure Service Fabric, registos de suporte quase sempre são necessários. Se o seu cluster está alojado no Azure, os registos de suporte são automaticamente configurados e recolhidos como parte da criação de um cluster. Os registos são armazenados numa conta de armazenamento dedicado no grupo de recursos do seu cluster. A conta de armazenamento não tem um nome fixo, mas na conta de ver os contentores de BLOBs e tabelas com nomes que começam por *fabric*. Para obter informações sobre como configurar coleções de registo para um cluster autónomo, consulte [criar e gerir um cluster autónomo de Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) e [declusterdedefiniçõesdeconfiguraçãoparaWindowsautónomo](service-fabric-cluster-manifest.md). Para as instâncias de recursos de infraestrutura do serviço autónomo, os registos devem ser enviados para uma partilha de ficheiros local. Está **necessário** ter estes registos para suporte, mas eles não se destinam a ser utilizado por qualquer pessoa fora da equipa de suporte de cliente do Microsoft.

## <a name="measuring-performance"></a>Avaliando o desempenho

Medir o desempenho do seu cluster irá ajudá-lo a compreender como ela é capaz de lidar com a carga e a unidade de decisões relacionadas ao dimensionamento do cluster (ver mais sobre o dimensionamento de um cluster [no Azure](service-fabric-cluster-scale-up-down.md), ou [locais](service-fabric-cluster-windows-server-add-remove-nodes.md)). Dados de desempenho também são útil quando comparado com as ações que ou com os seus aplicativos e serviços podem ter demorado, quando a análise de registos no futuro. 

Para obter uma lista dos contadores de desempenho a recolher quando utilizar o Service Fabric, consulte [contadores de desempenho no Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Eis duas formas comuns em que pode configurar a recolha de dados de desempenho para o seu cluster:

* **Utilização de um agente**  
Essa é a maneira preferencial de recolha de desempenho a partir de uma máquina, uma vez que os agentes têm, normalmente, uma lista de métricas de desempenho possíveis que podem ser coletadas e é um processo relativamente fácil para escolher as métricas que pretende recolher ou alterar. A leitura sobre o Azure Monitor oferta do Azure Monitor regista no Service Fabric [integração de registos do Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) e [como configurar o agente Log Analytics](../log-analytics/log-analytics-windows-agent.md) para saber mais sobre o agente do Log Analytics, que é um desse agente de monitorização que pode recolher dados de desempenho para VMs do cluster e contentores de implementado.

* **Contadores de desempenho para o armazenamento de tabelas do Azure**  
Também pode enviar métricas de desempenho para o mesmo armazenamento de tabela como os eventos. Isto requer a alteração da configuração de diagnósticos do Azure para recolher os contadores de desempenho adequado das VMs no seu cluster e ativá-la recolher estatísticas de docker se for implementar quaisquer contentores. Leia sobre como configurar [contadores de desempenho na WAD](service-fabric-diagnostics-event-aggregation-wad.md) no Service Fabric para configurar a recolha do contador de desempenho.

## <a name="next-steps"></a>Passos Seguintes

* Leia sobre o Service Fabric [integração de registos do Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) para recolher diagnósticos de cluster e criar consultas personalizadas e alertas
* Saiba mais sobre o Service Fabric na experiência de diagnóstico incorporada, o [EventStore](service-fabric-diagnostics-eventstore.md)
* Percorrer algumas [cenários comuns de diagnóstico](service-fabric-diagnostics-common-scenarios.md) no Service Fabric
