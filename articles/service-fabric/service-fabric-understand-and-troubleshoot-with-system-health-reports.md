---
title: Resolução de problemas com relatórios de estado de funcionamento do sistema
description: Descreve os relatórios de integridade enviados pelos componentes de Service Fabric do Azure e seu uso para solucionar problemas de cluster ou de aplicativo
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: a76ae803b1283ce50d2f4e259943ce5ffcf0274c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75370380"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Utilizar relatórios de estado de funcionamento do sistema para resolver problemas
Os componentes do Service Fabric do Azure fornecem relatórios de integridade do sistema em todas as entidades no cluster imediatamente. O [repositório de integridade](service-fabric-health-introduction.md#health-store) cria e exclui entidades com base nos relatórios do sistema. Ele também as organiza em uma hierarquia que captura interações de entidade.

> [!NOTE]
> Para entender os conceitos relacionados à integridade, leia mais em [Service Fabric modelo de integridade](service-fabric-health-introduction.md).
> 
> 

Os relatórios de integridade do sistema fornecem visibilidade da funcionalidade do cluster e do aplicativo e sinalizam problemas. Para aplicativos e serviços, os relatórios de integridade do sistema verificam se as entidades estão implementadas e se comportando corretamente da perspectiva Service Fabric. Os relatórios não fornecem nenhum monitoramento de integridade da lógica de negócios do serviço ou detecção de processos que não estão respondendo. Os serviços de usuário podem enriquecer os dados de integridade com informações específicas para sua lógica.

> [!NOTE]
> Os relatórios de integridade enviados pelos Watchdogs do usuário são visíveis somente *depois* que os componentes do sistema criam uma entidade. Quando uma entidade é excluída, o repositório de integridade exclui automaticamente todos os relatórios de integridade associados a ele. O mesmo é verdadeiro quando uma nova instância da entidade é criada. Um exemplo é quando uma nova instância de réplica de serviço persistente com estado é criada. Todos os relatórios associados à instância antiga são excluídos e limpos da loja.
> 
> 

Os relatórios do componente do sistema são identificados pela origem, que começa com o "**sistema".** . Os Watchdogs não podem usar o mesmo prefixo para suas origens, pois os relatórios com parâmetros inválidos são rejeitados.

Vamos examinar alguns relatórios do sistema para entender o que os dispara e saber como corrigir os possíveis problemas que eles representam.

> [!NOTE]
> Service Fabric continua a adicionar relatórios sobre condições de interesse que melhoram a visibilidade do que está acontecendo no cluster e nos aplicativos. Os relatórios existentes podem ser aprimorados com mais detalhes para ajudar a solucionar o problema mais rapidamente.
> 
> 

## <a name="cluster-system-health-reports"></a>Relatórios de integridade do sistema de cluster
A entidade de integridade do cluster é criada automaticamente no repositório de integridade. Se tudo funcionar corretamente, ele não terá um relatório do sistema.

### <a name="neighborhood-loss"></a>Perda de vizinhança
**System. Federation** relata um erro ao detectar uma perda de vizinhança. O relatório é de nós individuais e a ID do nó é incluída no nome da propriedade. Se uma vizinhança for perdida em todo o Service Fabric anel, você normalmente pode esperar dois eventos que representam os dois lados do relatório de lacunas. Se mais vizinhanças forem perdidos, haverá mais eventos.

O relatório especifica o tempo limite de concessão global como tempo de vida (TTL). O relatório é reenviado a cada metade da duração da TTL, desde que a condição permaneça ativa. O evento é removido automaticamente quando expira. O comportamento de remover quando expirado garante que o relatório seja limpo do repositório de integridade corretamente, mesmo que o nó de relatório esteja inoperante.

* **SourceID**: System. Federation
* **Propriedade**: começa com **ambiente** e inclui informações de nó.
* **Próximas etapas**: investigue por que a vizinhança foi perdida. Por exemplo, verifique a comunicação entre os nós de cluster.

### <a name="rebuild"></a>Reconstruir

O serviço de Gerenciador de Failover (FM) gerencia informações sobre os nós de cluster. Quando a FM perde seus dados e entra em perda de dados, ela não pode garantir que ele tenha as informações mais atualizadas sobre os nós de cluster. Nesse caso, o sistema passa por uma recompilação e o System.FM coleta dados de todos os nós no cluster para recompilar seu estado. Às vezes, devido a problemas de rede ou nó, a recompilação pode ficar presa ou paralisada. O mesmo pode ocorrer com o serviço de Gerenciador de Failover mestre (FMM). O FMM é um serviço de sistema sem estado que controla onde todos os FMs estão no cluster. O primário do FMM é sempre o nó com a ID mais próxima de 0. Se esse nó for descartado, uma recompilação será disparada.
Quando uma das condições anteriores acontece, **System.FM** ou **System. FMM** os sinaliza por meio de um relatório de erro. A recompilação pode estar presa em uma das duas fases:

* **Aguardando difusão**: FM/FMM aguarda a resposta da mensagem de difusão dos outros nós.

  * **Próximas etapas**: investigue se há um problema de conexão de rede entre os nós.
* **Aguardando nós**: FM/FMM já recebeu uma resposta de difusão dos outros nós e está aguardando uma resposta de nós específicos. O relatório de integridade lista os nós para os quais o FM/FMM está aguardando uma resposta.
   * **Próximas etapas**: investigue a conexão de rede entre o FM/FMM e os nós listados. Investigue cada nó listado para outros possíveis problemas.

* **SourceID**: System.FM ou System. FMM
* **Propriedade**: recompilar.
* **Próximas etapas**: investigue a conexão de rede entre os nós, bem como o estado de quaisquer nós específicos listados na descrição do relatório de integridade.

### <a name="seed-node-status"></a>Status do nó de semente
O **System.FM** relatará um aviso de nível de cluster se alguns nós de propagação não estiverem íntegros. Os nós de semente são os nós que mantêm a disponibilidade do cluster subjacente. Esses nós ajudam a garantir que o cluster permaneça ativo estabelecendo concessões com outros nós e servindo como desempates durante determinados tipos de falhas de rede. Se a maioria dos nós de semente estiver inoperante no cluster e eles não forem trazidos de volta, o cluster será desligado automaticamente. 

Um nó de semente não estará íntegro se o status do nó for inoperante, removido ou desconhecido.
O relatório de aviso para o status do nó de semente listará todos os nós de semente não íntegros com informações detalhadas.

* **SourceID**: System.FM
* **Propriedade**: SeedNodeStatus
* **Próximas etapas**: se este aviso aparecer no cluster, siga as instruções abaixo para corrigi-lo: para cluster em execução Service Fabric versão 6,5 ou superior: para Service Fabric cluster no Azure, depois que o nó semente ficar inativo, Service Fabric tentará alterá-lo para um nó não semente automaticamente. Para fazer isso acontecer, verifique se o número de nós que não são de semente no tipo de nó primário é maior ou igual ao número de nós de semente inativos. Se necessário, adicione mais nós ao tipo de nó primário para conseguir isso.
Dependendo do status do cluster, pode levar algum tempo para corrigir o problema. Quando isso for feito, o relatório de aviso será limpo automaticamente.

Para Service Fabric cluster autônomo, para limpar o relatório de aviso, todos os nós de semente precisam se tornar íntegros. Dependendo de por que os nós de semente não estão íntegros, ações diferentes precisam ser tomadas: se o nó semente estiver inativo, os usuários precisarão trazer esse nó de semente para cima; Se o nó de semente for removido ou desconhecido, esse nó semente [precisará ser removido do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes).
O relatório de aviso é limpo automaticamente quando todos os nós de propagação se tornam íntegros.

Para o cluster que executa a versão Service Fabric mais antiga que 6,5: nesse caso, o relatório de aviso precisa ser limpo manualmente. **Os usuários devem garantir que todos os nós de semente se tornem íntegros antes de limpar o relatório**: se o nó semente estiver inativo, os usuários precisarão trazer esse nó de semente para cima; se o nó semente for removido ou desconhecido, esse nó semente precisará ser removido do cluster.
Depois que todos os nós de semente se tornarem íntegros, use o comando a seguir do PowerShell para [limpar o relatório de aviso](https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricclusterhealthreport):

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
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
**System. FabricNode** relata um aviso quando os certificados usados pelo nó estão prestes a expirar. Há três certificados por nó: **Certificate_cluster**, **Certificate_server**e **Certificate_default_client**. Quando a expiração tiver pelo menos duas semanas de distância, o estado de integridade do relatório será OK. Quando a expiração estiver dentro de duas semanas, o tipo de relatório será um aviso. O TTL desses eventos é infinito e eles são removidos quando um nó sai do cluster.

* **SourceID**: System. FabricNode
* **Propriedade**: começa com **certificado** e contém mais informações sobre o tipo de certificado.
* **Próximas etapas**: atualize os certificados se eles estiverem próximos da expiração.

### <a name="load-capacity-violation"></a>Violação de capacidade de carga
O Service Fabric Load Balancer relata um aviso quando detecta uma violação de capacidade de nó.

* **SourceID**: System. PLB
* **Propriedade**: começa com a **capacidade**.
* **Próximas etapas**: Verifique as métricas fornecidas e exiba a capacidade atual no nó.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Incompatibilidade de capacidade de nó para métricas de governança de recursos
System. Hosting relatará um aviso se as capacidades de nó definidas no manifesto do cluster forem maiores do que as capacidades de nó real para métricas de governança de recursos (núcleos de CPU e memória). Um relatório de integridade é exibido quando o primeiro pacote de serviço que usa a [governança de recursos](service-fabric-resource-governance.md) é registrado em um nó especificado.

* **SourceID**: sistema. Hosting
* **Propriedade**: **ResourceGovernance**.
* **Próximas etapas**: esse problema pode ser um problema porque o controle de pacotes de serviço não é imposto conforme o esperado e a [governança de recursos](service-fabric-resource-governance.md) não funciona corretamente. Atualize o manifesto do cluster com as capacidades de nó corretas para essas métricas ou não especifique-as e permita que Service Fabric detectar automaticamente os recursos disponíveis.

## <a name="application-system-health-reports"></a>Relatórios de integridade do sistema de aplicativos
System.CM, que representa o serviço Gerenciador de cluster, é a autoridade que gerencia informações sobre um aplicativo.

### <a name="state"></a>Estado
System.CM relata como OK quando o aplicativo foi criado ou atualizado. Ele informa o repositório de integridade quando o aplicativo é excluído para que ele possa ser removido do repositório.

* **SourceID**: System.cm
* **Propriedade**: estado.
* **Próximas etapas**: se o aplicativo tiver sido criado ou atualizado, ele deverá incluir o relatório de integridade do Gerenciador de cluster. Caso contrário, verifique o estado do aplicativo emitindo uma consulta. Por exemplo, use o cmdlet do PowerShell **Get-ServiceFabricApplication-ApplicationName** *ApplicationName*.

O exemplo a seguir mostra o evento de estado no aplicativo **Fabric:/WordCount** :

```powershell
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

## <a name="service-system-health-reports"></a>Relatórios de integridade do sistema de serviço
System.FM, que representa o serviço Gerenciador de Failover, é a autoridade que gerencia informações sobre serviços.

### <a name="state"></a>Estado
System.FM relata como OK quando o serviço foi criado. Ele exclui a entidade do repositório de integridade quando o serviço é excluído.

* **SourceID**: System.FM
* **Propriedade**: estado.

O exemplo a seguir mostra o evento de estado no Service **Fabric:/WordCount/WordCountWebService**:

```powershell
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

### <a name="service-correlation-error"></a>Erro de correlação de serviço
**System. PLB** relata um erro ao detectar que a atualização de um serviço está correlacionada a outro serviço que cria uma cadeia de afinidade. O relatório é limpo quando ocorre uma atualização bem-sucedida.

* **SourceID**: System. PLB
* **Propriedade**: **ServiceDescription**.
* **Próximas etapas**: Verifique as descrições de serviço correlacionadas.

## <a name="partition-system-health-reports"></a>Relatórios de integridade do sistema de partição
System.FM, que representa o serviço Gerenciador de Failover, é a autoridade que gerencia as informações sobre as partições de serviço.

### <a name="state"></a>Estado
System.FM relata como OK quando a partição foi criada e está íntegra. Ele exclui a entidade do repositório de integridade quando a partição é excluída.

Se a partição estiver abaixo da contagem mínima de réplica, ela relatará um erro. Se a partição não estiver abaixo da contagem mínima de réplicas, mas estiver abaixo da contagem de réplica de destino, ela relatará um aviso. Se a partição estiver em perda de quorum, System.FM relatará um erro.

Outros eventos notáveis incluem um aviso quando a reconfiguração demora mais do que o esperado e quando a compilação demora mais do que o esperado. Os tempos esperados para a compilação e reconfiguração são configuráveis com base nos cenários de serviço. Por exemplo, se um serviço tiver um terabyte de estado, como o banco de dados SQL do Azure, a compilação levará mais tempo do que um serviço com uma pequena quantidade de estado.

* **SourceID**: System.FM
* **Propriedade**: estado.
* **Próximas etapas**: se o estado de integridade não for Ok, é possível que algumas réplicas não tenham sido criadas, abertas ou promovidas para o primário ou secundário corretamente. 

Se a descrição descrever a perda de quorum, examinar o relatório de integridade detalhado para réplicas que estão inativas e colocá-las novamente ajuda a colocar a partição online novamente.

Se a descrição descrever uma partição presa na [reconfiguração](service-fabric-concepts-reconfiguration.md), o relatório de integridade na réplica primária fornecerá informações adicionais.

Para outros relatórios de integridade do System.FM, haveria relatórios sobre as réplicas ou a partição ou o serviço de outros componentes do sistema. 

Os exemplos a seguir descrevem alguns desses relatórios. 

O exemplo a seguir mostra uma partição íntegra:

```powershell
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

O exemplo a seguir mostra a integridade de uma partição que está abaixo da contagem de réplica de destino. A próxima etapa é obter a descrição da partição, que mostra como ela está configurada: **MinReplicaSetSize** é três e **TargetReplicaSetSize** é sete. Em seguida, obtenha o número de nós no cluster, que nesse caso é cinco. Portanto, nesse caso, duas réplicas não podem ser colocadas, pois o número de destino de réplicas é maior do que o número de nós disponíveis.

```powershell
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

O exemplo a seguir mostra a integridade de uma partição que está presa na reconfiguração porque o usuário não está respeitando o token de cancelamento no método **RunAsync** . Investigar o relatório de integridade de qualquer réplica marcada como primária (P) pode ajudar a detalhar ainda mais o problema.

```powershell
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
Este relatório de integridade mostra o estado das réplicas da partição que está passando pela reconfiguração: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Para cada réplica, o relatório de integridade contém:
- Função de configuração anterior
- Função de configuração atual
- [Estado da réplica](service-fabric-concepts-replica-lifecycle.md)
- Nó no qual a réplica está em execução
- ID de réplica

Em um caso como o exemplo, uma investigação adicional é necessária. Investigue a integridade de cada réplica individual começando com as réplicas marcadas como `Primary` e `Secondary` (131482789658160654 e 131482789688598467) no exemplo anterior.

### <a name="replica-constraint-violation"></a>Violação de restrição de réplica
**System. PLB** relata um aviso se detectar uma violação de restrição de réplica e não puder posicionar todas as réplicas de partição. Os detalhes do relatório mostram quais restrições e propriedades impedem o posicionamento da réplica.

* **SourceID**: System. PLB
* **Propriedade**: começa com **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Relatórios de integridade do sistema de réplica
**System. ra**, que representa o componente do agente de reconfiguração, é a autoridade para o estado da réplica.

### <a name="state"></a>Estado
System. RA relata OK quando a réplica foi criada.

* **SourceID**: System. ra
* **Propriedade**: estado.

O exemplo a seguir mostra uma réplica íntegra:

```powershell
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
Essa propriedade é usada para indicar avisos ou falhas ao tentar abrir uma réplica, fechar uma réplica ou fazer a transição de uma réplica de uma função para outra. Para obter mais informações, consulte [ciclo de vida da réplica](service-fabric-concepts-replica-lifecycle.md). As falhas podem ser exceções geradas das chamadas à API ou falhas do processo de host do serviço durante esse tempo. Para falhas devido a chamadas de API C# do código, Service Fabric adiciona a exceção e o rastreamento de pilha ao relatório de integridade.

Esses avisos de integridade são gerados depois de repetir a ação localmente, certo número de vezes (dependendo da política). Service Fabric tenta novamente a ação até um limite máximo. Depois que o limite máximo for atingido, ele poderá tentar agir para corrigir a situação. Essa tentativa pode fazer com que esses avisos sejam apagados, pois eles aparecem na ação neste nó. Por exemplo, se uma réplica estiver falhando em abrir em um nó, Service Fabric gerará um aviso de integridade. Se a réplica continuar a ser aberta, Service Fabric agirá para o reparo automático. Essa ação pode envolver a tentativa da mesma operação em outro nó. Essa tentativa faz com que o aviso gerado para esta réplica seja limpo. 

* **SourceID**: System. ra
* **Propriedade**: **ReplicaOpenStatus**, **ReplicaCloseStatus**e **ReplicaChangeRoleStatus**.
* **Próximas etapas**: investigue o código de serviço ou os despejos de memória para identificar por que a operação está falhando.

O exemplo a seguir mostra a integridade de uma réplica que está lançando `TargetInvocationException` de seu método Open. A descrição contém o ponto de falha, **IStatefulServiceReplica. Open**, o tipo de exceção **TargetInvocationException**e o rastreamento de pilha.

```powershell
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

O exemplo a seguir mostra uma réplica que está falhando constantemente durante o fechamento:

```powershell
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
Essa propriedade é usada para indicar quando uma réplica que executa uma [reconfiguração](service-fabric-concepts-reconfiguration.md) detecta que a reconfiguração está parada ou travada. Esse relatório de integridade pode estar na réplica cuja função atual é primária, exceto nos casos de uma reconfiguração primária de permuta, em que ela pode estar na réplica que está sendo rebaixada do primário para o secundário ativo.

A reconfiguração pode ser paralisada por um dos seguintes motivos:

- Uma ação na réplica local, a mesma réplica que está executando a reconfiguração, não está sendo concluída. Nesse caso, investigar os relatórios de integridade nessa réplica de outros componentes, System. RAP ou System.RE, pode fornecer informações adicionais.

- Uma ação não está sendo concluída em uma réplica remota. As réplicas para as quais as ações estão pendentes são listadas no relatório de integridade. Uma investigação adicional deve ser feita em relatórios de integridade para essas réplicas remotas. Também pode haver problemas de comunicação entre este nó e o nó remoto.

Em casos raros, a reconfiguração pode estar paralisada devido à comunicação ou a outros problemas entre esse nó e o serviço de Gerenciador de Failover.

* **SourceID**: System. ra
* **Propriedade**: reconfiguração.
* **Próximas etapas**: investigue as réplicas locais ou remotas dependendo da descrição do relatório de integridade.

O exemplo a seguir mostra um relatório de integridade em que uma reconfiguração está presa na réplica local. Neste exemplo, é devido a um serviço não respeitar o token de cancelamento.

```powershell
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

O exemplo a seguir mostra um relatório de integridade em que uma reconfiguração está presa aguardando uma resposta de duas réplicas remotas. Neste exemplo, há três réplicas na partição, incluindo a primária atual. 

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

Este relatório de integridade mostra que a reconfiguração está presa aguardando uma resposta de duas réplicas: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Para cada réplica, as seguintes informações são fornecidas:
- Função de configuração anterior
- Função de configuração atual
- [Estado da réplica](service-fabric-concepts-replica-lifecycle.md)
- ID do nó
- ID de réplica

Para desbloquear a reconfiguração:
- As réplicas **abaixo** devem ser ativadas. 
- As réplicas de **incompilação** devem concluir a compilação e a transição para pronto.

### <a name="slow-service-api-call"></a>Chamada à API de serviço lenta
**System. rap** e **System. Replicator** relatarão um aviso se uma chamada para o código do serviço de usuário demorar mais do que o tempo configurado. O aviso é apagado quando a chamada é concluída.

* **SourceID**: System. rap ou System. Replicator
* **Propriedade**: o nome da API lenta. A descrição fornece mais detalhes sobre a hora em que a API está pendente.
* **Próximas etapas**: investigue por que a chamada demora mais do que o esperado.

O exemplo a seguir mostra o evento de integridade de System. RAP para um serviço confiável que não está respeitando o token de cancelamento em **RunAsync**:

```powershell
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

A propriedade e o texto indicam qual API ficou paralisada. As próximas etapas a serem tomadas para diferentes APIs presas são diferentes. Qualquer API no *IStatefulServiceReplica* ou *no istatelessserviceinstance* geralmente é um bug no código do serviço. A seção a seguir descreve como eles são convertidos para o [modelo de Reliable Services](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica. Open**: esse aviso indica que uma chamada para `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`ou, se substituída, `OnOpenAsync` está paralisada.

- **IStatefulServiceReplica. Close** e **IStatefulServiceReplica. Abort**: o caso mais comum é um serviço que não respeita o token de cancelamento passado para `RunAsync`. Também pode ser que `ICommunicationListener.CloseAsync`ou, se substituído, `OnCloseAsync` esteja preso.

- **IStatefulServiceReplica. ChangeRole (S)** e **IStatefulServiceReplica. ChangeRole (N)** : o caso mais comum é um serviço que não respeita o token de cancelamento passado para `RunAsync`. Nesse cenário, a melhor solução é reiniciar a réplica.

- **IStatefulServiceReplica. ChangeRole (P)** : o caso mais comum é que o serviço não retornou uma tarefa de `RunAsync`.

Outras chamadas à API que podem ficar presas estão na interface **IReplicator** . Por exemplo:

- **IReplicator. CatchupReplicaSet**: esse aviso indica uma das duas coisas. Há réplicas insuficientes. Para ver se esse é o caso, examine o status da réplica das réplicas na partição ou no relatório de integridade System.FM para uma reconfiguração presa. Ou as réplicas não estão confirmando operações. O cmdlet do PowerShell `Get-ServiceFabricDeployedReplicaDetail` pode ser usado para determinar o progresso de todas as réplicas. O problema está em réplicas cujo valor de `LastAppliedReplicationSequenceNumber` está atrás do valor de `CommittedSequenceNumber` primário.

- **IReplicator. BuildReplica (\<> de replicaId remota)** : esse aviso indica um problema no processo de compilação. Para obter mais informações, consulte [ciclo de vida da réplica](service-fabric-concepts-replica-lifecycle.md). Pode ser devido a uma configuração incorreta do endereço do replicador. Para obter mais informações, consulte [configurar Reliable Services com estado](service-fabric-reliable-services-configuration.md) e [especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md). Também pode ser um problema no nó remoto.

### <a name="replicator-system-health-reports"></a>Relatórios de integridade do sistema replicador
**Fila de replicação cheia:** 
**System. Replicator** relata um aviso quando a fila de replicação está cheia. No primário, a fila de replicação geralmente fica cheia porque uma ou mais réplicas secundárias estão lentas para confirmar as operações. No secundário, isso geralmente acontece quando o serviço está lento para aplicar as operações. O aviso é apagado quando a fila não está mais cheia.

* **SourceID**: System. Replicator
* **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, dependendo da função de réplica.
* **Próximas etapas**: se o relatório estiver no primário, verifique a conexão entre os nós no cluster. Se todas as conexões estiverem íntegras, pode haver pelo menos um secundário lento com uma alta latência de disco para aplicar operações. Se o relatório estiver no secundário, verifique o uso do disco e o desempenho no nó primeiro. Em seguida, verifique a conexão de saída do nó lento para o primário.

**RemoteReplicatorConnectionStatus:** 
**System. Replicator** na réplica primária relata um aviso quando a conexão com um replicador secundário (remoto) não está íntegra. O endereço do replicador remoto é mostrado na mensagem do relatório, o que torna mais conveniente detectar se a configuração errada foi passada ou se há problemas de rede entre os replicadores.

* **SourceID**: System. Replicator
* **Propriedade**: **RemoteReplicatorConnectionStatus**.
* **Próximas etapas**: Verifique a mensagem de erro e verifique se o endereço do replicador remoto está configurado corretamente. Por exemplo, se o replicador remoto for aberto com o endereço de escuta "localhost", ele não será acessível de fora. Se o endereço parecer correto, verifique a conexão entre o nó primário e o endereço remoto para encontrar possíveis problemas de rede.

### <a name="replication-queue-full"></a>Fila de replicação cheia
**System. Replicator** relata um aviso quando a fila de replicação está cheia. No primário, a fila de replicação geralmente fica cheia porque uma ou mais réplicas secundárias estão lentas para confirmar as operações. No secundário, isso geralmente acontece quando o serviço está lento para aplicar as operações. O aviso é apagado quando a fila não está mais cheia.

* **SourceID**: System. Replicator
* **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, dependendo da função de réplica.

### <a name="slow-naming-operations"></a>Operações de nomenclatura lentas
**System. NamingService** relata a integridade em sua réplica primária quando uma operação de nomenclatura demora mais do que o aceitável. Exemplos de operações de nomenclatura são [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) ou [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Mais métodos podem ser encontrados em FabricClient. Por exemplo, eles podem ser encontrados em métodos de [Gerenciamento de serviços](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) ou métodos de [Gerenciamento de propriedade](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> O serviço de nomenclatura resolve nomes de serviço para um local no cluster. Os usuários podem usá-lo para gerenciar nomes e propriedades de serviço. É um Service Fabric serviço com persistência particionado. Uma das partições representa o *proprietário da autoridade*, que contém metadados sobre todos os nomes de Service Fabric e serviços. Os nomes de Service Fabric são mapeados para partições diferentes, chamadas de partições de *proprietário de nome* , para que o serviço seja extensível. Leia mais sobre o [serviço de nomenclatura](service-fabric-architecture.md).
> 
> 

Quando uma operação de nomenclatura leva mais tempo do que o esperado, a operação é sinalizada com um relatório de aviso na réplica primária da partição do serviço de nomenclatura que atende à operação. Se a operação for concluída com êxito, o aviso será apagado. Se a operação for concluída com um erro, o relatório de integridade incluirá detalhes sobre o erro.

* **SourceId**: System.NamingService
* **Propriedade**: começa com o prefixo "**Duration_** " e identifica a operação lenta e o nome de Service Fabric no qual a operação é aplicada. Por exemplo, se criar serviço em nome **Fabric:/MyApp/MyService** demorar muito, a propriedade será **Duration_AOCreateService. Fabric:/MyApp/MyService**. "Ao" aponta para a função da partição de nomenclatura para esse nome e operação.
* **Próximas etapas**: Verifique por que a operação de nomenclatura falha. Cada operação pode ter causas raiz diferentes. Por exemplo, o serviço de exclusão pode estar paralisado. O serviço pode estar paralisado porque o host do aplicativo continua falhando em um nó devido a um bug do usuário no código do serviço.

O exemplo a seguir mostra uma operação de criação de serviço. A operação demorou mais do que a duração configurada. O "ao" tenta novamente e envia o trabalho para "não". "Não" concluiu a última operação com tempo limite. Nesse caso, a mesma réplica é primária para as funções "ao" e "não".

```powershell
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

## <a name="deployedapplication-system-health-reports"></a>Relatórios de integridade do sistema DeployedApplication
**System. Hosting** é a autoridade nas entidades implantadas.

### <a name="activation"></a>Ativação
System. Hosting relata como OK quando um aplicativo é ativado com êxito no nó. Caso contrário, ele relatará um erro.

* **SourceID**: sistema. Hosting
* **Propriedade**: **ativação**, incluindo a versão de distribuição.
* **Próximas etapas**: se o aplicativo não estiver íntegro, investigue por que a ativação falhou.

O exemplo a seguir mostra uma ativação bem-sucedida:

```powershell
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

### <a name="download"></a>Transferir
System. Hosting relatará um erro se o download do pacote de aplicativos falhar.

* **SourceID**: sistema. Hosting
* **Propriedade**: **Download**, incluindo a versão de distribuição.
* **Próximas etapas**: investigue por que o download falhou no nó.

## <a name="deployedservicepackage-system-health-reports"></a>Relatórios de integridade do sistema DeployedServicePackage
**System. Hosting** é a autoridade nas entidades implantadas.

### <a name="service-package-activation"></a>Ativação do pacote de serviço
System. Hosting relata como OK se a ativação do pacote de serviço no nó for bem-sucedida. Caso contrário, ele relatará um erro.

* **SourceID**: sistema. Hosting
* **Propriedade**: ativação.
* **Próximas etapas**: investigue por que a ativação falhou.

### <a name="code-package-activation"></a>Ativação do pacote de códigos
System. Hosting relata como OK para cada pacote de código se a ativação for bem-sucedida. Se a ativação falhar, ela relatará um aviso como configurado. Se **CodePackage** falhar em ativar ou terminar com um erro maior do que o **CodePackageHealthErrorThreshold**configurado, a hospedagem relatará um erro. Se um pacote de serviço contiver vários pacotes de código, um relatório de ativação será gerado para cada um deles.

* **SourceID**: sistema. Hosting
* **Propriedade**: usa o prefixo **CodePackageActivation** e contém o nome do pacote de códigos e o ponto de entrada como *CodePackageActivation: CodePackageName: SetupEntryPoint/EntryPoint*. Por exemplo, **CodePackageActivation: Code: SetupEntryPoint**.

### <a name="service-type-registration"></a>Registro de tipo de serviço
System. Hosting relatará como OK se o tipo de serviço tiver sido registrado com êxito. Ele relatará um erro se o registro não tiver sido feito no tempo, conforme configurado usando **ServiceTypeRegistrationTimeout**. Se o tempo de execução estiver fechado, o tipo de serviço será cancelado no registro do nó e a hospedagem relatará um aviso.

* **SourceID**: sistema. Hosting
* **Propriedade**: usa o prefixo **ServiceTypeRegistration** e contém o nome do tipo de serviço. Por exemplo, **ServiceTypeRegistration: FileStoreServiceType**.

O exemplo a seguir mostra um pacote de serviço íntegro implantado:

```powershell
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

### <a name="download"></a>Transferir
System. Hosting relatará um erro se o download do pacote de serviço falhar.

* **SourceID**: sistema. Hosting
* **Propriedade**: **Download**, incluindo a versão de distribuição.
* **Próximas etapas**: investigue por que o download falhou no nó.

### <a name="upgrade-validation"></a>Validação da atualização
System. Hosting relatará um erro se a validação durante a atualização falhar ou se a atualização falhar no nó.

* **SourceID**: sistema. Hosting
* **Propriedade**: usa o prefixo **FabricUpgradeValidation** e contém a versão de atualização.
* **Descrição**: aponta para o erro encontrado.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Capacidade de nó indefinida para métricas de governança de recursos
System. Hosting relatará um aviso se as capacidades do nó não estiverem definidas no manifesto do cluster e a configuração para detecção automática estiver desativada. Service Fabric gera um aviso de integridade sempre que o pacote de serviço que usa a [governança de recursos](service-fabric-resource-governance.md) é registrado em um nó especificado.

* **SourceID**: sistema. Hosting
* **Propriedade**: **ResourceGovernance**.
* **Próximas etapas**: a maneira preferida de superar esse problema é alterar o manifesto do cluster para habilitar a detecção automática de recursos disponíveis. Outra maneira é atualizar o manifesto do cluster com as capacidades de nó especificadas corretamente para essas métricas.

## <a name="next-steps"></a>Passos seguintes
* [Exibir Service Fabric relatórios de integridade](service-fabric-view-entities-aggregated-health.md)

* [Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Atualização do aplicativo Service Fabric](service-fabric-application-upgrade.md)

