---
title: Resolver problemas com relatórios de estado de funcionamento do sistema | Documentos da Microsoft
description: Descreve os relatórios de estado de funcionamento enviados por componentes do Azure Service Fabric e seu uso para resolução de problemas de cluster ou problemas de aplicativos
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d62fd909d10515c9217a4dd0aa760afa376b8d7c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838906"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Utilizar relatórios de estado de funcionamento do sistema para resolver problemas
Componentes do Service Fabric do Azure fornecem reporta o estado de funcionamento do sistema de todas as entidades no cluster prontos a utilizar. O [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store) cria e elimina as entidades com base em relatórios do sistema. Ele também organiza numa hierarquia que captura as interações de entidade.

> [!NOTE]
> Para compreender conceitos relacionados com o estado de funcionamento, leia mais sobre [modelo de estado de funcionamento do Service Fabric](service-fabric-health-introduction.md).
> 
> 

Relatórios de estado de funcionamento do sistema proporcionam visibilidade para o cluster e funcionalidade do aplicativo e problemas de sinalizador. Para aplicações e serviços, relatórios de estado de funcionamento do sistema Certifique-se de que as entidades são implementadas e se comportam corretamente a partir da perspetiva do Service Fabric. Os relatórios não fornecem qualquer monitorização de estado de funcionamento de lógica de negócios do serviço ou a deteção de processos bloqueados. Serviços de utilizador podem enriquecer os dados de estado de funcionamento com informações específicas para sua lógica.

> [!NOTE]
> Relatórios de estado de funcionamento enviados por watchdogs de utilizador são visíveis apenas *depois de* os componentes de sistema criar uma entidade. Quando uma entidade é eliminada, o arquivo de estado de funcionamento exclui automaticamente todos os relatórios de estado de funcionamento associados ao mesmo. O mesmo acontece quando é criada uma nova instância da entidade. Um exemplo é quando é criada uma nova instância da réplica de serviço persistidas com monitoração de estado. Todos os relatórios associados à instância antiga são eliminados e removidos da loja.
> 
> 

O componente do sistema relatórios são identificados pela origem, que começa com a "**System.**" prefixo. Watchdogs não é possível utilizar o mesmo prefixo para suas origens, como relatórios com parâmetros inválidos são rejeitados.

Vamos examinar alguns relatórios de sistema para compreender o que aciona-los e para saber como corrigir os problemas potenciais que elas representam.

> [!NOTE]
> Service Fabric continua a adicionar relatórios em condições de interesse que aumentar a visibilidade, o que acontece no cluster e as aplicações. Os relatórios existentes podem ser aprimorados com mais detalhes para ajudar a resolver o problema mais rapidamente.
> 
> 

## <a name="cluster-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de cluster
A entidade de estado de funcionamento do cluster é criada automaticamente no arquivo de estado de funcionamento. Se tudo funcionar adequadamente, ele não tem um relatório do sistema.

### <a name="neighborhood-loss"></a>Perda de proximidade
**System.Federation** relata um erro quando Deteta uma perda de proximidade. O relatório é nós individuais e o ID de nó que está incluído no nome da propriedade. Se uma vizinhança é perdida no anel de Service Fabric todo, normalmente, pode esperar dois eventos que representam os dois lados do relatório lacuna. Se perdem-se mais vizinhanças, existem mais eventos.

O relatório Especifica o tempo limite global concessão como o time-to-live (TTL). O relatório é reenviado a duração do TTL para a cada meia, desde que a condição permanece ativa. O evento é removido automaticamente quando este expirar. Expirou em remove-quando o comportamento garante que o relatório é limpo do arquivo de estado de funcionamento corretamente, mesmo que o nó de geração de relatórios está inativo.

* **SourceId**: System.Federation
* **Propriedade**: Começa com **Vizinhança** e inclui informações do nó.
* **Próximos passos**: Investigar por que toda a Vizinhança é perdida. Por exemplo, verificar a comunicação entre nós de cluster.

### <a name="rebuild"></a>Reconstruir

O serviço de Gestor de ativação pós-falha (FM) gere informações sobre os nós do cluster. Quando FM perde os seus dados e entra em perda de dados, não pode garantir que tem as informações mais atualizadas sobre os nós do cluster. Neste caso, o sistema passa através de uma reconstrução e System.FM reúne dados de todos os nós do cluster para recriar o seu estado. Às vezes, devido a problemas de nó ou de sistema de rede, recompilação pode obter preso ou parada. O mesmo pode acontecer com o serviço de mestre de Gestor de ativação pós-falha (FMM). O FMM é um serviço de sistema sem monitoração de estado que controla onde todos os FMs estão no cluster. Principal de FMM está sempre o nó com o ID mais próximo de 0. Se esse nó é removido, é acionada uma reconstrução.
Quando uma das condições anteriores acontece, **System.FM** ou **System.FMM** sinaliza-lo por meio de um relatório de erros. A recriação de poderão estar bloqueada em uma de duas fases:

* **A aguardar para difusão**: FM/FMM aguarda a resposta de mensagem de difusão de outros nós.

  * **Próximos passos**: Investigar se há um problema de ligação de rede entre os nós.
* **A aguardar por nós**: FM/FMM já recebeu uma resposta de difusão de outros nós e está a aguardar uma resposta de nós específicos. O relatório de estado de funcionamento indica os nós para os quais o FM/FMM está a aguardar uma resposta.
   * **Próximos passos**: Investigue a ligação de rede entre o FM/FMM e os nós listados. Investigue cada nó listada para outros problemas possíveis.

* **SourceID**: System.FM ou System.FMM
* **Propriedade**: Reconstrução.
* **Próximos passos**: Investigue a ligação de rede entre os nós, bem como o estado de quaisquer nós específicos que estão listados na descrição do relatório de estado de funcionamento.

## <a name="node-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de nó
System.FM, que representa o serviço do Gestor de ativação pós-falha, é a autoridade que gere informações sobre nós de cluster. Cada nó deve ter um relatório a partir da System.FM que mostra o estado. As entidades de nó são removidas quando o estado do nó é removido. Para obter mais informações, consulte [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### <a name="node-updown"></a>Nó para cima/para baixo
System.FM relatórios como OK quando o nó se associa o anel (está em execução). Ele relata um erro quando o nó departs o anel (estiver desativado, seja para atualizar ou simplesmente porque ele não conseguiu). A hierarquia de estado de funcionamento criada pelo arquivo de estado de funcionamento age em entidades implementadas na correlação com relatórios de nó System.FM. Ele considera o nó virtual principal de todas as entidades implementados. As entidades implementadas nesse nó são expostas através de consultas, se o nó é reportado como operacional por System.FM, com a mesma instância que a instância associada com as entidades. Quando System.FM relatórios que o nó está inativo ou reiniciado, como uma nova instância, o arquivo de estado de funcionamento limpa automaticamente as entidades implementadas que podem existir apenas o nó em baixo ou na instância anterior do nó.

* **SourceId**: System.FM
* **Propriedade**: Estado.
* **Próximos passos**: Se o nó estiver em baixo para uma atualização, ele deve se levante depois é foi atualizado. Neste caso, o estado de funcionamento deve mude para OK. Se o nó não voltar ou falhar, o problema tem mais investigação.

O exemplo seguinte mostra o evento de System.FM com um Estado de funcionamento de OK para o nó:

```PowerShell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Expiração do certificado
**System.FabricNode** produz um aviso quando os certificados utilizados pelo nó de estão prestes a expirar. Existem três certificados por nó: **Certificate_cluster**, **Certificate_server**, e **Certificate_default_client**. Quando a expiração é, pelo menos, duas semanas de distância, o estado de funcionamento do relatório está OK. Quando a expiração é em duas semanas, o tipo de relatório é um aviso. TTL desses eventos é infinita e removidas quando um nó deixa o cluster.

* **SourceId**: System.FabricNode
* **Propriedade**: Começa com **certificado** e contém mais informações sobre o tipo de certificado.
* **Próximos passos**: Atualize os certificados se eles estiverem prestes a expirar.

### <a name="load-capacity-violation"></a>Violação de capacidade de carga
O Balanceador de carga do Service Fabric produz um aviso quando Deteta uma violação de capacidade do nó.

* **SourceId**: System.PLB
* **Propriedade**: Começa com **capacidade**.
* **Próximos passos**: Verifique as métricas fornecidas e ver a capacidade atual no nó.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Erro de correspondência de capacidade de nó de métricas de governação de recursos
System.Hosting comunica um aviso se definido as capacidades de nós no manifesto do cluster são maiores do que as capacidades reais de nós para métricas de governação de recursos (memória e núcleos de CPU). Um relatório de estado de funcionamento é apresentado quando o primeiro pacote de serviço que utiliza [governação de recursos](service-fabric-resource-governance.md) registra num nó especificado.

* **SourceId**: System.Hosting
* **Propriedade**: **ResourceGovernance**.
* **Próximos passos**: Este problema pode ser um problema porque governação pacotes de serviço não são impostas conforme esperado e [governação de recursos](service-fabric-resource-governance.md) não funciona corretamente. Atualizar manifesto do cluster com as capacidades de nó corretos para estas métricas, ou não especificá-los e permitir que o Service Fabric detetar automaticamente recursos disponíveis.

## <a name="application-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de aplicação
System.CM, que representa o serviço de Gestor de clusters, é a autoridade que gere informações sobre uma aplicação.

### <a name="state"></a>Estado
System.CM relatórios como OK quando a aplicação foi criada ou atualizada. Ele informa o arquivo de estado de funcionamento quando a aplicação for eliminada, de modo a que pode ser removido da loja.

* **SourceId**: System.CM
* **Propriedade**: Estado.
* **Próximos passos**: Se a aplicação foi criada ou atualizada, deve incluir o relatório de estado de funcionamento do Gestor de clusters. Caso contrário, verifique o estado da aplicação através da emissão de uma consulta. Por exemplo, utilize o cmdlet do PowerShell **Get ServiceFabricApplication - ApplicationName** *applicationName*.

O exemplo seguinte mostra o evento de estado sobre o **fabric: / WordCount** aplicação:

```PowerShell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de serviço
System.FM, que representa o serviço do Gestor de ativação pós-falha, é a autoridade que gere informações sobre os serviços.

### <a name="state"></a>Estado
System.FM relatórios como OK quando o serviço tiver sido criado. Elimina a entidade do arquivo de estado de funcionamento quando o serviço for eliminado.

* **SourceId**: System.FM
* **Propriedade**: Estado.

O exemplo seguinte mostra o evento de estado do serviço **fabric: / WordCount/WordCountWebService**:

```PowerShell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Erro de serviço de correlação
**System** relata um erro quando Deteta que um serviço de atualização está correlacionado com outro serviço que cria uma cadeia de afinidade. O relatório será eliminado quando ocorre uma atualização efetuada com êxito.

* **SourceId**: System.PLB
* **Propriedade**: **ServiceDescription**.
* **Próximos passos**: Consulte as descrições de serviço correlacionados.

## <a name="partition-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de partição
System.FM, que representa o serviço do Gestor de ativação pós-falha, é a autoridade que gere informações sobre as partições do serviço.

### <a name="state"></a>Estado
System.FM relatórios como OK quando a partição tiver sido criada e está em bom estada. Elimina a entidade do arquivo de estado de funcionamento quando a partição é eliminada.

Se a partição for inferior a contagem mínima de réplica, relata um erro. Se a partição não está abaixo a contagem mínima de réplica, mas que fique abaixo da contagem de réplicas de destino, produz um aviso. Se a partição está em perda de quórum, System.FM relata um erro.

Outros eventos notáveis incluem um aviso quando a reconfiguração demora mais tempo do que o esperado e quando a compilação demora mais tempo do que o esperado. A esperada tempos para a compilação e a reconfiguração são configuráveis com base em cenários de serviço. Por exemplo, se um serviço tem um terabyte de estado, como a base de dados SQL do Azure, a compilação exige mais tempo para um serviço com uma pequena quantidade de estado.

* **SourceId**: System.FM
* **Propriedade**: Estado.
* **Próximos passos**: Se o estado de funcionamento não está OK, é possível que algumas réplicas não foram criadas, abrir ou promovidas a primária ou secundária corretamente. 

Se a descrição descreve a perda de quórum, em seguida, examinando o relatório de estado de funcionamento detalhadas para réplicas que estão indisponíveis e colocando-as cópia de segurança ajuda a colocar a partição novamente online.

Se a descrição descreve uma partição bloqueada num [reconfiguração](service-fabric-concepts-reconfiguration.md), em seguida, o relatório de estado de funcionamento na réplica primária fornece informações adicionais.

Para outros relatórios de estado de funcionamento de System.FM, haveria relatórios sobre as réplicas ou a partição ou o serviço a partir de outros componentes do sistema. 

Os exemplos seguintes descrevem algumas desses relatórios. 

O exemplo seguinte mostra uma partição de bom estado de funcionamento:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

O exemplo seguinte mostra o estado de funcionamento de uma partição que é inferior a contagem de réplicas de destino. A próxima etapa é obter a descrição de partição, que mostra a configuração: **MinReplicaSetSize** é a terceira e **TargetReplicaSetSize** é sete. Em seguida, obter o número de nós do cluster, que neste caso é cinco. Então, neste caso, duas réplicas não não possível colocar, porque o número de destino de réplicas é maior do que o número de nós disponíveis.

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

O exemplo seguinte mostra o estado de funcionamento de uma partição que está parada na reconfiguração devido ao utilizador não respeitar o cancelamento o token do **RunAsync** método. Investigar o relatório de estado de funcionamento de qualquer réplica marcado como primário (P) pode ajudar a analisar para baixo mais detalhadamente o problema.

```PowerShell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Este relatório de estado de funcionamento mostra o estado das réplicas da partição em reconfiguração: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Para cada réplica, o relatório de estado de funcionamento contém:
- Função de configuração anterior
- Função de configuração atual
- [Estado da réplica](service-fabric-concepts-replica-lifecycle.md)
- Nó em que a réplica está em execução
- ID de réplica

Ainda mais o caso, como o exemplo, é necessário investigação. Investigar o estado de funcionamento de cada réplica individual a partir das réplicas marcadas como `Primary` e `Secondary` (131482789658160654 e 131482789688598467) no exemplo anterior.

### <a name="replica-constraint-violation"></a>Violação de restrição de réplica
**System** produz um aviso se Deteta uma violação de restrição de réplica e não é possível colocar todas as réplicas de partição. Os detalhes do relatório mostram quais restrições e propriedades de impedem a colocação de réplica.

* **SourceId**: System.PLB
* **Propriedade**: Começa com **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de réplica
**System.RA**, que representa o componente do agente de reconfiguração, é a autoridade para o estado da réplica.

### <a name="state"></a>Estado
System.RA relatórios OK quando tiver sido criada a réplica.

* **SourceId**: System.RA
* **Propriedade**: Estado.

O exemplo seguinte mostra uma réplica de bom estado de funcionamento:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Esta propriedade é utilizada para indicar os avisos ou falhas durante a tentativa de abrir uma réplica, fechar uma réplica ou fazer a transição de uma réplica de uma função para outro. Para obter mais informações, consulte [ciclo de vida de réplica](service-fabric-concepts-replica-lifecycle.md). As falhas podem ser exceções acionadas a partir de chamadas de API ou falhas do processo de anfitrião do serviço durante este período. Para falhas devido a API chama de C# código, recursos de infraestrutura do serviço adiciona o rastreio de pilha de exceção e para o relatório de estado de funcionamento.

Esses avisos de estado de funcionamento são gerados depois de repetir a ação localmente um número de vezes (dependendo da política). O Service Fabric repete a ação até um limite máximo. Após ter sido atingido o limite máximo, ele poderá tentar agir para corrigir a situação. Esta tentativa pode fazer com que estes avisos para será desmarcada quando desistir na ação neste nó. Por exemplo, se uma réplica está a conseguir abrir num nó, o Service Fabric gera um aviso do Estado de funcionamento. Se a réplica continuar a falhar abrir, Service Fabric funciona ao reparo automático. Esta ação pode envolver a tentar a mesma operação noutro nó. Esta tentativa faz com que o aviso gerado para esta réplica ser limpo. 

* **SourceId**: System.RA
* **Propriedade**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, e **ReplicaChangeRoleStatus**.
* **Próximos passos**: Investigar o código de serviço ou informações de falhas para identificar o motivo pelo qual a operação está a falhar.

O exemplo seguinte mostra o estado de funcionamento de uma réplica que está a lançar `TargetInvocationException` do seu método open. A descrição contém o ponto de falha, **IStatefulServiceReplica.Open**, o tipo de exceção **TargetInvocationException**e o rastreio de pilha.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

O exemplo seguinte mostra uma réplica que está constantemente a falhar durante o encerramento:

```PowerShell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Reconfiguração
Esta propriedade é utilizada para indicar quando uma réplica a efetuar uma [reconfiguração](service-fabric-concepts-reconfiguration.md) Deteta que a reconfiguração está bloqueada ou preso. Este relatório de estado de funcionamento poderá ser na réplica cuja função atual é primária, exceto no caso de uma reconfiguração de principais de comutação, onde poderá ser na réplica que está a ser despromovida a partir do secundário do primário para o Active Directory.

A reconfiguração pode estar bloqueada por um dos seguintes motivos:

- Uma ação na réplica local, a réplica mesmo que o desempenho a reconfiguração não está a concluir. Neste caso, investigar os relatórios de estado de funcionamento nesta réplica de outros componentes, System ou meio, pode fornecer informações adicionais.

- Não está a concluir uma ação numa réplica remota. Para os quais ações estão pendentes de réplicas estão listadas no relatório de estado de funcionamento. Investigação adicional deve ser feita em relatórios de estado de funcionamento para essas réplicas remotos. Também pode haver problemas de comunicação entre este nó e o nó remoto.

Em casos raros, a reconfiguração pode estar bloqueada devido a comunicação ou outros problemas entre este nó e o serviço de Gestor de ativação pós-falha.

* **SourceId**: System.RA
* **Propriedade**: Reconfiguração.
* **Próximos passos**: Investigar réplicas locais ou remotas, consoante a descrição do relatório de estado de funcionamento.

O exemplo seguinte mostra um relatório de estado de funcionamento em que uma reconfiguração está bloqueada na réplica local. Neste exemplo, ele é devido a um serviço não respeitar o token de cancelamento.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

A exemplo a seguir mostra um Estado de funcionamento de relatórios sempre que uma reconfiguração está bloqueada a aguardar uma resposta de duas réplicas remotas. Neste exemplo, há três réplicas na partição, incluindo o principal atual. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Este relatório de estado de funcionamento mostra que a reconfiguração está bloqueada a aguardar uma resposta de duas réplicas: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Para cada réplica, é dado as seguintes informações:
- Função de configuração anterior
- Função de configuração atual
- [Estado da réplica](service-fabric-concepts-replica-lifecycle.md)
- ID do Nó
- ID de réplica

Para desbloquear a reconfiguração:
- O **baixo** réplicas devem ser recuperadas. 
- O **inbuild** réplicas devem concluir a compilação e faça a transição para pronto.

### <a name="slow-service-api-call"></a>Chamada de API do serviço lenta
**System** e **System.Replicator** reportar um aviso se a uma chamada para o código do serviço de utilizador demora mais do que o tempo configurado. O aviso será eliminado quando a chamada é concluída.

* **SourceId**: System ou System.Replicator
* **Propriedade**: O nome da API lenta. A descrição apresenta mais detalhes sobre o tempo que a API está no estado pendente.
* **Próximos passos**: Investigar por que a chamada demora mais tempo do que o esperado.

O exemplo seguinte mostra o evento de estado de funcionamento do System para um serviço fiável que não está a respeitar o cancelamento o token **RunAsync**:

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

A propriedade e o texto indicam qual API parou. Os passos seguintes a efetuar para diferentes APIs bloqueadas são diferentes. Qualquer API na *IStatefulServiceReplica* ou *IStatelessServiceInstance* costuma ser um bug no código de serviço. A seguinte secção descreve como eles traduzir para o [modelo de Reliable Services](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: Este aviso indica que uma chamada para `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, ou se tiverem sido substituídas, `OnOpenAsync` está bloqueada.

- **IStatefulServiceReplica.Close** e **IStatefulServiceReplica.Abort**: O caso mais comum é um serviço não respeitar o token de cancelamento passado para `RunAsync`. Também pode ser que `ICommunicationListener.CloseAsync`, ou se tiverem sido substituídas, `OnCloseAsync` está bloqueada.

- **(S) de IStatefulServiceReplica.ChangeRole** e **IStatefulServiceReplica.ChangeRole(N)**: O caso mais comum é um serviço não respeitar o token de cancelamento passado para `RunAsync`.

- **IStatefulServiceReplica.ChangeRole(P)**: O caso mais comum é que o serviço não devolveu uma tarefa de `RunAsync`.

Outras chamadas de API que podem ficar preso são sobre o **IReplicator** interface. Por exemplo:

- **IReplicator.CatchupReplicaSet**: Este aviso indica uma das duas coisas. Existem insuficiente segurança de réplicas. Para ver se for este o caso, ver o estado da réplica das réplicas na partição ou o relatório de estado de funcionamento System.FM para uma reconfiguração paralisado. Ou as réplicas não são ter consciência de operações. O cmdlet do PowerShell `Get-ServiceFabricDeployedReplicaDetail` pode ser utilizado para determinar o progresso de todas as réplicas. O problema está com réplicas cujos `LastAppliedReplicationSequenceNumber` valor está por trás da primária `CommittedSequenceNumber` valor.

- **IReplicator.BuildReplica(<Remote ReplicaId>)**: Este aviso indica um problema no processo de compilação. Para obter mais informações, consulte [ciclo de vida de réplica](service-fabric-concepts-replica-lifecycle.md). Tal poderá dever-se uma configuração incorreta do endereço replicador. Para obter mais informações, consulte [configurar a Reliable Services com estado](service-fabric-reliable-services-configuration.md) e [especificar recursos num manifesto do serviço](service-fabric-service-manifest-resources.md). Também pode ser um problema no nó remoto.

### <a name="replicator-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de replicador
**Total de fila de replicação:**
**System.Replicator** produz um aviso quando a fila de replicação está cheia. Principal, a fila de replicação geralmente fica cheio porque um ou mais réplicas secundárias são lentas reconhecer operações. Secundário, normalmente, isto acontece quando o serviço está lento aplicar as operações. O aviso será eliminado quando a fila já não está cheia.

* **SourceId**: System.Replicator
* **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, consoante a função de réplica.
* **Próximos passos**: Se o relatório principal, verifique a ligação entre os nós do cluster. Se todas as ligações estão em bom estadas, poderá haver, pelo menos, uma secundária lenta com uma latência de disco elevados para aplicar as operações. Se o relatório no secundário, verifique primeiro a utilização do disco e o desempenho no nó. Em seguida, verifique a ligação de saída do nó lento para o primário.

**RemoteReplicatorConnectionStatus:**
**System.Replicator** na réplica primária produz um aviso quando a ligação para um secundário replicador (remoto) não está em bom estada. Endereço do replicador remoto é mostrado na mensagem do relatório, o que torna mais conveniente para detetar se a configuração incorreta foi passada ou se existirem problemas de rede entre os replicators.

* **SourceId**: System.Replicator
* **Propriedade**: **RemoteReplicatorConnectionStatus**.
* **Próximos passos**: Verifique a mensagem de erro e certifique-se de que o endereço do replicador remoto está configurado corretamente. Por exemplo, se o replicador remoto é aberto com o endereço de escuta "localhost", não é acessível de fora. Se o endereço parecer correto, verifique a ligação entre o nó principal e o endereço remoto para localizar outros possíveis problemas de rede.

### <a name="replication-queue-full"></a>Total de fila de replicação
**System.Replicator** produz um aviso quando a fila de replicação está cheia. Principal, a fila de replicação geralmente fica cheio porque um ou mais réplicas secundárias são lentas reconhecer operações. Secundário, normalmente, isto acontece quando o serviço está lento aplicar as operações. O aviso será eliminado quando a fila já não está cheia.

* **SourceId**: System.Replicator
* **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, consoante a função de réplica.

### <a name="slow-naming-operations"></a>Operações de nomenclatura lentas
**System.NamingService** reporta o estado de funcionamento na respetiva réplica primária quando uma operação de nomenclatura exige mais tempo aceitável. São exemplos de operações de nomenclatura [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) ou [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Mais métodos podem ser encontrados em FabricClient. Por exemplo, pode ser encontrados na [métodos de gestão do serviço](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) ou [métodos de gestão de propriedade](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> No serviço de nomes resolva nomes de serviço para uma localização do cluster. Os utilizadores podem utilizá-lo para gerir os nomes de serviço e propriedades. É um serviço do Service Fabric persistente particionados. Uma das partições representa a *Authority Owner*, que contém metadados sobre todos os nomes do Service Fabric e serviços. Os nomes do Service Fabric são mapeados para diferentes partições, chamadas *Name Owner* partição, para que o serviço é extensível. Leia mais sobre o [serviço de cadastramento](service-fabric-architecture.md).
> 
> 

Quando uma operação de nomenclatura demora mais tempo do que o esperado, a operação é sinalizada com um relatório de aviso na réplica primária da partição de serviço de nomenclatura que serve a operação. Se a operação for concluída com êxito, o aviso está desmarcado. Se a operação for concluída com um erro, o relatório de estado de funcionamento inclui detalhes sobre o erro.

* **SourceId**: System.NamingService
* **Propriedade**: Começa com o prefixo "**Duration_**" e identifica a operação lenta e o nome do Service Fabric no qual é aplicada a operação. Por exemplo, se criar o serviço em nome **fabric: / MyApp/Meuserviço** demora demasiado tempo, a propriedade é **Duration_AOCreateService.fabric:/MyApp/MyService**. "Pedidos" aponta para a função de partição de nomenclatura para este nome e a operação.
* **Próximos passos**: Certifique-se de por que a operação de nomenclatura de falha. Cada operação pode ter diferentes causas. Por exemplo, o serviço de eliminação pode estar bloqueado. O serviço pode estar bloqueado porque o anfitrião de aplicação mantém a falhar num nó devido a um erro de utilizador com o código do serviço.

O exemplo seguinte mostra uma operação de serviço de criação. A operação demorou mais do que a duração configurada. "Pedidos" repetirá e envia o trabalho para "não". A última operação com o tempo limite "Não" concluída. Neste caso, a mesma réplica é primária para o "pedidos" e "Não" funções.

```PowerShell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de DeployedApplication
**System.Hosting** é a autoridade em entidades implementadas.

### <a name="activation"></a>Ativação
System.Hosting relatórios como OK quando um aplicativo foi ativado com êxito no nó. Caso contrário, ele relata um erro.

* **SourceId**: System.Hosting
* **Propriedade**: **Ativação**, incluindo a versão de implementação.
* **Próximos passos**: Se a aplicação está em mau estado de funcionamento, investigue o motivo pelo qual a ativação falhou.

O exemplo seguinte mostra uma ativação com êxito:

```PowerShell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Transferência
System.Hosting relata um erro se o download do pacote de aplicação falhar.

* **SourceId**: System.Hosting
* **Propriedade**: **Transferir**, incluindo a versão de implementação.
* **Próximos passos**: Investigar por que a transferência falhou no nó.

## <a name="deployedservicepackage-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de DeployedServicePackage
**System.Hosting** é a autoridade em entidades implementadas.

### <a name="service-package-activation"></a>Ativação do pacote de serviço
System.Hosting relatórios como OK se a ativação do pacote de serviço no nó é efetuada com êxito. Caso contrário, ele relata um erro.

* **SourceId**: System.Hosting
* **Propriedade**: Ativação.
* **Próximos passos**: Investigue o motivo pelo qual a ativação falhou.

### <a name="code-package-activation"></a>Ativação do pacote de código
System.Hosting relatórios como OK para cada pacote de código se a ativação é efetuada com êxito. Se a ativação falhar, ele produz um aviso, conforme configurado. Se **CodePackage** falhar a ativação ou termina com um erro superior configurada **CodePackageHealthErrorThreshold**, alojamento relata um erro. Se um pacote de serviço contém vários pacotes de código, é gerado um relatório de ativação para cada um deles.

* **SourceId**: System.Hosting
* **Propriedade**: Utiliza o prefixo **CodePackageActivation** e contém o nome do pacote de código e o ponto de entrada como *CodePackageActivation:CodePackageName:SetupEntryPoint / EntryPoint*. Por exemplo, **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Registo do tipo de serviço
System.Hosting relatórios como OK se o tipo de serviço foi registado com êxito. Ele relata um erro se o registo não foi concluído no tempo, conforme configurado através da utilização **ServiceTypeRegistrationTimeout**. Se o tempo de execução estiver fechado, o tipo de serviço é anulado o registo do nó e de alojamento produz um aviso.

* **SourceId**: System.Hosting
* **Propriedade**: Utiliza o prefixo **ServiceTypeRegistration** e contém o nome do tipo de serviço. Por exemplo, **ServiceTypeRegistration:FileStoreServiceType**.

O exemplo seguinte mostra um pacote de serviço implementado bom estado de funcionamento:

```PowerShell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Transferência
System.Hosting relata um erro se o download do pacote de serviço falhar.

* **SourceId**: System.Hosting
* **Propriedade**: **Transferir**, incluindo a versão de implementação.
* **Próximos passos**: Investigar por que a transferência falhou no nó.

### <a name="upgrade-validation"></a>Validação da atualização
System.Hosting relata um erro se a validação durante a atualização falhar ou se a atualização falhar no nó.

* **SourceId**: System.Hosting
* **Propriedade**: Utiliza o prefixo **FabricUpgradeValidation** e contém a versão de atualização.
* **Descrição**: Aponta para o erro encontrado.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Capacidade de nó não definido para métricas de governação de recursos
System.Hosting produz um aviso se as capacidades de nós não são definidas no manifesto do cluster e a configuração para a deteção automática está desativada. Service Fabric gera um aviso de estado de funcionamento sempre que o pacote de serviço que utiliza [governação de recursos](service-fabric-resource-governance.md) registra num nó especificado.

* **SourceId**: System.Hosting
* **Propriedade**: **ResourceGovernance**.
* **Próximos passos**: A maneira preferencial de solucionar esse problema é alterar o manifesto do cluster para ativar a deteção automática de recursos disponíveis. Outra forma é atualizar o manifesto do cluster com as capacidades de nó especificado corretamente para estas métricas.

## <a name="next-steps"></a>Passos Seguintes
* [Ver relatórios de estado de funcionamento do Service Fabric](service-fabric-view-entities-aggregated-health.md)

* [Como comunicar e verificar o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Monitorizar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)

