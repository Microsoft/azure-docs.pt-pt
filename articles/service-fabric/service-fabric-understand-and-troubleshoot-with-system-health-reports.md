---
title: Resolver problemas com relatórios de estado de funcionamento do sistema
description: Descreve os relatórios de saúde enviados pelos componentes do Azure Service Fabric e a sua utilização para problemas de resolução de problemas de aglomerado ou aplicação
ms.topic: conceptual
ms.date: 2/28/2018
ms.openlocfilehash: 483483746b2cce66588e9481bca7e0de391070b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625896"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Utilizar relatórios de estado de funcionamento do sistema para resolver problemas
Os componentes do Azure Service Fabric fornecem relatórios de saúde do sistema sobre todas as entidades do cluster fora da caixa. A [loja de saúde](service-fabric-health-introduction.md#health-store) cria e elimina entidades com base nos relatórios do sistema. Também os organiza numa hierarquia que capta interações de entidades.

> [!NOTE]
> Para compreender conceitos relacionados com a saúde, leia mais no modelo de [saúde service fabric.](service-fabric-health-introduction.md)
> 
> 

Os relatórios de saúde do sistema proporcionam visibilidade na funcionalidade do cluster e aplicação, e problemas de bandeira. Para aplicações e serviços, os relatórios de saúde do sistema verificam se as entidades estão implementadas e estão a comportar-se corretamente do ponto de vista do Tecido de Serviço. Os relatórios não fornecem qualquer monitorização de saúde da lógica empresarial do serviço ou deteção de processos que não estão a responder. Os serviços de utilizadores podem enriquecer os dados de saúde com informações específicas da sua lógica.

> [!NOTE]
> Os relatórios de saúde enviados pelos cães de guarda do utilizador só são visíveis *depois* de os componentes do sistema criarem uma entidade. Quando uma entidade é eliminada, a loja de saúde elimina automaticamente todos os relatórios de saúde que lhe estão associados. O mesmo acontece quando se cria um novo caso da entidade. Um exemplo é quando uma nova instância de réplica de serviço persistiu estatal é criada. Todos os relatórios associados à antiga instância são apagados e limpos da loja.
> 
> 

Os relatórios de componentes do sistema são identificados pela fonte, que começa com o "**Sistema ".** . Os cães de guarda não podem usar o mesmo prefixo para as suas fontes, uma vez que os relatórios com parâmetros inválidos são rejeitados.

Vamos analisar alguns relatórios do sistema para entender o que os desencadeia e para aprender a corrigir os potenciais problemas que representam.

> [!NOTE]
> A Service Fabric continua a adicionar relatórios sobre condições de interesse que melhoram a visibilidade do que está a acontecer no cluster e nas aplicações. Os relatórios existentes podem ser melhorados com mais detalhes para ajudar a resolver o problema mais rapidamente.
> 
> 

## <a name="cluster-system-health-reports"></a>Relatórios de saúde do sistema de cluster
A entidade de saúde do cluster é criada automaticamente na loja de saúde. Se tudo funcionar corretamente, não tem um relatório do sistema.

### <a name="neighborhood-loss"></a>Perda de bairro
**System.Federation** reporta um erro quando deteta uma perda de bairro. O relatório é de nós individuais, e o nó identificação está incluído no nome da propriedade. Se um bairro se perder em todo o anel de Tecido de Serviço, você pode normalmente esperar dois eventos que representam ambos os lados do relatório gap. Se mais bairros se perderem, há mais eventos.

O relatório especifica o tempo limite de locação global como o tempo de vida (TTL). O relatório é ressentido com cada metade da duração da TTL enquanto a condição se mantiver ativa. O evento é automaticamente removido quando expira. O comportamento removido quando expirado garante que o relatório é limpo corretamente da loja de saúde, mesmo que o nó de relato esteja em baixo.

* **SourceId**: System.Federation
* **Propriedade**: Começa com **Bairro** e inclui informações sobre nó.
* **Próximos passos**: Investigar por que o bairro está perdido. Por exemplo, verifique a comunicação entre os nós do cluster.

### <a name="rebuild"></a>Reconstruir

O serviço Failover Manager (FM) gere informações sobre os nós do cluster. Quando a FM perde os seus dados e entra em perda de dados, não pode garantir que tenha a informação mais atualizada sobre os nós do cluster. Neste caso, o sistema passa por uma reconstrução, e System.FM recolhe dados de todos os nós do cluster para reconstruir o seu estado. Por vezes, devido a problemas de rede ou nó, a reconstrução pode ficar presa ou paralisada. O mesmo pode acontecer com o serviço Failover Manager Master (FMM). O FMM é um serviço de sistema apátrida que acompanha onde estão todos os FMS no cluster. O primário do FMM é sempre o nó com o ID mais próximo de 0. Se o nó cair, uma reconstrução é desencadeada.
Quando uma das condições anteriores acontece, **System.FM** ou **System.FMM** assinala-a através de um relatório de erro. A reconstrução pode ficar presa numa de duas fases:

* **À espera da transmissão**: FM/FMM aguarda a resposta da mensagem de transmissão dos outros nós.

  * **Próximos passos**: Investigue se existe um problema de ligação à rede entre nós.
* **À espera de nós**: FM/FMM já recebeu uma resposta transmitida dos outros nós e aguarda uma resposta de nós específicos. O relatório de saúde lista os nós para os quais o FM/FMM aguarda uma resposta.
   * **Seguintes etapas**: Investigue a ligação de rede entre o FM/FMM e os nós listados. Investigue cada nó listado para outros possíveis problemas.

* **SourceID**: System.FM ou System.FMM
* **Propriedade**: Reconstruir.
* **Seguintes etapas**: Investigar a ligação da rede entre os nós, bem como o estado de quaisquer nós específicos listados na descrição do relatório de saúde.

### <a name="seed-node-status"></a>Estado do nó de sementes
**System.FM** relata um alerta de nível de aglomerado se alguns nós de sementes não forem saudáveis. Os nós de sementes são os nós que mantêm a disponibilidade do cluster subjacente. Estes nós ajudam a garantir que o cluster permanece em pé, estabelecendo arrendamentos com outros nós e servindo como desempates durante certos tipos de falhas de rede. Se a maioria dos nós de sementes estiver em baixo no aglomerado e não forem trazidos de volta, o cluster desliga-se automaticamente. 

Um nó de sementes não é saudável se o seu estado de nó for para baixo, removido ou desconhecido.
O relatório de aviso para o estado do nó de sementes indicará todos os nós de sementes pouco saudáveis com informações detalhadas.

* **SourceID**: System.FM
* **Propriedade**: SeedNodeStatus
* **Seguintes etapas**: Se este aviso aparecer no cluster, siga as instruções abaixo para fixá-lo: Para o cluster funcionando A versão 6.5 ou superior: Para o cluster de tecido de serviço em Azure, depois de o nó de sementes descer, o Tecido de Serviço tentará alterá-lo automaticamente para um nó de não semente. Para que isto aconteça, certifique-se de que o número de nós não-sementes no tipo de nó primário é maior ou igual ao número de nós de sementes para baixo. Se necessário, adicione mais nós ao tipo de nó primário para o conseguir.
Dependendo do estado do cluster, pode levar algum tempo para corrigir o problema. Uma vez feito isto, o relatório de aviso é automaticamente apurado.

Para o aglomerado autónomo do Tecido de Serviço, para limpar o relatório de alerta, todos os nós de sementes precisam de se tornar saudáveis. Dependendo do motivo pelo qual os nós de sementes não são saudáveis, devem ser tomadas diferentes ações: se o nó de sementes for para baixo, os utilizadores precisam de trazer esse nó de sementes para cima; se o nó de sementes for removido ou desconhecido, este nó de sementes [tem de ser removido do aglomerado](./service-fabric-cluster-windows-server-add-remove-nodes.md).
O relatório de aviso é automaticamente limpo quando todos os nós de sementes ficam saudáveis.

Para a versão de serviço de funcionamento do cluster com mais de 6.5: Neste caso, o relatório de aviso tem de ser limpo manualmente. **Os utilizadores devem certificar-se de que todos os nós de sementes ficam saudáveis antes de limpar o relatório**: se o nó de sementes estiver para baixo, os utilizadores têm de trazer esse nó de sementes para cima; se o nó de sementes for removido ou desconhecido, esse nó de sementes precisa de ser removido do cluster.
Depois de todos os nós de sementes ficarem saudáveis, use o comando de Powershell para limpar o relatório de [aviso:](/powershell/module/servicefabric/send-servicefabricclusterhealthreport)

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

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
**System.FabricNode** reporta um aviso quando os certificados utilizados pelo nó estão perto de expirar. Existem três certificados por nó: **Certificate_cluster,** **Certificate_server** e **Certificate_default_client.** Quando a expiração for de pelo menos duas semanas, o estado de saúde do relatório está ok. Quando a expiração é dentro de duas semanas, o tipo de relatório é um aviso. TTL destes eventos é infinito, e são removidos quando um nó sai do aglomerado.

* **SourceId**: System.FabricNode
* **Propriedade**: Começa com **Certificado** e contém mais informações sobre o tipo de certificado.
* **Próximos passos**: Atualizar os certificados se estiverem perto do termo.

### <a name="load-capacity-violation"></a>Violação da capacidade de carga
O Balancer de Carga de Tecido de Serviço informa um aviso quando deteta uma violação da capacidade do nó.

* **SourceId**: System.PLB
* **Propriedade**: Começa com **Capacidade**.
* **Passos seguintes**: Verifique as métricas fornecidas e veja a capacidade de corrente no nó.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Desajuste da capacidade do nó para as métricas de governação dos recursos
System.Hosting reporta um aviso se as capacidades definidas do nó no manifesto do cluster forem maiores do que as capacidades reais do nó para as métricas de governação de recursos (memória e núcleos de CPU). Um relatório de saúde aparece quando o primeiro pacote de serviço que utiliza registos [de governação de recursos](service-fabric-resource-governance.md) num nó especificado.

* **SourceId**: System.Hosting
* **Propriedade**: **ResourceGovernance**.
* **Próximos passos**: Esta questão pode ser um problema porque os pacotes de serviços que regem não são aplicados como esperado e [a governação dos recursos](service-fabric-resource-governance.md) não funciona corretamente. Atualize o manifesto do cluster com as capacidades de nó correto para estas métricas, ou não as especifique e deixe o Service Fabric detetar automaticamente os recursos disponíveis.

## <a name="application-system-health-reports"></a>Relatórios de saúde do sistema de aplicações
System.CM, que representa o serviço Cluster Manager, é a autoridade que gere a informação sobre uma aplicação.

### <a name="state"></a>Estado
System.CM reporta como OK quando a aplicação foi criada ou atualizada. Informa a loja de saúde quando a aplicação é eliminada para que possa ser removida da loja.

* **SourceId**: System.CM
* **Propriedade**: Estado.
* **Próximos passos**: Se a aplicação tiver sido criada ou atualizada, deve incluir o relatório de saúde do Cluster Manager. Caso contrário, verifique o estado do pedido através da emissão de uma consulta. Por exemplo, utilize o formulário Desaquichado De powerShell **Get-ServiceFabricApplication -Aplicação Nome de aplicaçãoName** *de aplicação*.

O exemplo a seguir mostra o evento estatal no **tecido:/Aplicação WordCount:**

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

## <a name="service-system-health-reports"></a>Relatórios de saúde do sistema de serviço
System.FM, que representa o serviço Failover Manager, é a autoridade que gere a informação sobre os serviços.

### <a name="state"></a>Estado
System.FM relata como OK quando o serviço foi criado. Elimina a entidade da loja de saúde quando o serviço é eliminado.

* **SourceId**: System.FM
* **Propriedade**: Estado.

O exemplo a seguir mostra o evento estatal no tecido de **serviço:/WordCount/WordCountWebService**:

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
**System.PLB** relata um erro quando deteta que atualizar um serviço está correlacionado com outro serviço que cria uma cadeia de afinidade. O relatório é apurado quando uma atualização bem sucedida acontece.

* **SourceId**: System.PLB
* **Propriedade**: **Atribuição de serviços.**
* **Próximos passos**: Verifique as descrições de serviço correlacionadas.

## <a name="partition-system-health-reports"></a>Relatórios de saúde do sistema de partição
System.FM, que representa o serviço Failover Manager, é a autoridade que gere a informação sobre divisórias de serviço.

### <a name="state"></a>Estado
System.FM relata como OK quando a partição foi criada e é saudável. Elimina a entidade da loja de saúde quando a partição é eliminada.

Se a partição estiver abaixo da contagem mínima de réplicas, reporta um erro. Se a partição não estiver abaixo da contagem mínima de réplicas, mas está abaixo da contagem de réplicas do alvo, reporta um aviso. Se a partição estiver em perda de quórum, System.FM reporta um erro.

Outros eventos notáveis incluem um aviso quando a reconfiguração demora mais tempo do que o esperado e quando a construção demora mais do que o esperado. Os tempos esperados para a construção e reconfiguração são configuráveis com base nos cenários de serviço. Por exemplo, se um serviço tem um terabyte de estado, como a Base de Dados Azure SQL, a construção demora mais tempo do que para um serviço com uma pequena quantidade de estado.

* **SourceId**: System.FM
* **Propriedade**: Estado.
* **Próximos passos**: Se o estado de saúde não estiver ok, é possível que algumas réplicas não tenham sido criadas, abertas ou promovidas para primária ou secundária corretamente. 

Se a descrição descreve a perda do quórum, então examinar o relatório de saúde detalhado para réplicas que estão em baixo e trazê-las de volta ajuda a repor a partição.

Se a descrição descreve uma partição presa na [reconfiguração,](service-fabric-concepts-reconfiguration.md)então o relatório de saúde sobre a réplica primária fornece informações adicionais.

Para outros relatórios de saúde System.FM, haveria relatórios sobre as réplicas ou a partição ou serviço de outros componentes do sistema. 

Os exemplos que se seguem descrevem alguns destes relatórios. 

O exemplo a seguir mostra uma divisória saudável:

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

O exemplo a seguir mostra a saúde de uma partição que está abaixo da contagem de réplicas do alvo. O próximo passo é obter a descrição da partição, que mostra como está configurada: **MinReplicaSetSize** é três e **TargetReplicaSetSize** é sete. Em seguida, obter o número de nós no aglomerado, que neste caso é cinco. Então, neste caso, duas réplicas não podem ser colocadas, porque o número alvo de réplicas é maior do que o número de nós disponíveis.

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

O exemplo a seguir mostra a saúde de uma partição que está presa na reconfiguração devido ao utilizador não honrar o símbolo de cancelamento no método **RunAsync.** Investigar o relatório de saúde de qualquer réplica marcada como primária (P) pode ajudar a aprofundar o problema.

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
Este relatório de saúde mostra o estado das réplicas da partição em fase de reconfiguração: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Para cada réplica, o relatório de saúde contém:
- Função de configuração anterior
- Função de configuração atual
- [Estado de réplica](service-fabric-concepts-replica-lifecycle.md)
- Nó no qual a réplica está em execução
- ID de réplica

Num caso como o exemplo, é necessária uma investigação mais aprofundada. Investigue a saúde de cada réplica individual a partir das réplicas marcadas como `Primary` `Secondary` e (131482789658160654 e 131482789688598467) no exemplo anterior.

### <a name="replica-constraint-violation"></a>Violação da restrição de réplica
**System.PLB** reporta um aviso se detetar uma violação de restrição de réplica e não conseguir colocar todas as réplicas de partição. Os detalhes do relatório mostram quais os constrangimentos e propriedades que impedem a colocação da réplica.

* **SourceId**: System.PLB
* **Propriedade**: Começa com **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Relatórios de saúde do sistema de réplica
**System.RA**, que representa o componente do agente de reconfiguração, é a autoridade para o estado de réplica.

### <a name="state"></a>Estado
System.RA reporta OK quando a réplica foi criada.

* **SourceId**: System.RA
* **Propriedade**: Estado.

O exemplo a seguir mostra uma réplica saudável:

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
Esta propriedade é usada para indicar avisos ou falhas ao tentar abrir uma réplica, fechar uma réplica ou transitar uma réplica de uma função para outra. Para mais informações, consulte [o ciclo de vida da Réplica.](service-fabric-concepts-replica-lifecycle.md) As falhas podem ser exceções lançadas das chamadas da API ou falhas do processo de anfitrião de serviço durante este tempo. Para falhas devido a chamadas API do código C#, o Service Fabric adiciona a exceção e empilha vestígios ao relatório de saúde.

Estes avisos sanitários são levantados após voltar a tentar a ação localmente algumas vezes (dependendo da política). O Tecido de Serviço retritiva a ação até um limiar máximo. Após a atingido o limiar máximo, poderá tentar agir para corrigir a situação. Esta tentativa pode fazer com que estes avisos fiquem esclarecidos à medida que desiste da ação neste nó. Por exemplo, se uma réplica não estiver a abrir num nó, o Service Fabric levanta um aviso sanitário. Se a réplica continuar a não abrir, o Service Fabric atua para se autorreparar. Esta ação pode envolver tentar a mesma operação noutro nó. Esta tentativa faz com que o aviso levantado para que esta réplica seja limpa. 

* **SourceId**: System.RA
* **Propriedade**: **ReplicaOpenStatus,** **ReplicaCloseStatus** e **ReplicaChangeRoleStatus**.
* **Seguintes etapas**: Investigue o código de serviço ou os depósitos de colisão para identificar por que a operação está a falhar.

O exemplo a seguir mostra a saúde de uma réplica que está a `TargetInvocationException` lançar do seu método aberto. A descrição contém o ponto de falha, **IStatefulServiceReplica.Open,** o tipo de exceção **TargetInvocationException**, e o traço de pilha.

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

O exemplo a seguir mostra uma réplica que está constantemente a bater durante o fecho:

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
Esta propriedade é usada para indicar quando uma réplica que executa uma [reconfiguração](service-fabric-concepts-reconfiguration.md) deteta que a reconfiguração está parada ou presa. Este relatório de saúde pode estar na réplica cujo papel atual é primário, exceto nos casos de uma reconfiguração primária de troca, onde pode estar na réplica que está sendo despromovada do primário para o secundário ativo.

A reconfiguração pode ser colada por uma das seguintes razões:

- Uma ação sobre a réplica local, a mesma réplica que a que está a fazer a reconfiguração, não está a completar. Neste caso, a investigação dos relatórios de saúde sobre esta réplica de outros componentes, System.RAP ou System.RE, poderá fornecer informações adicionais.

- Uma ação não está a ser completada numa réplica remota. As réplicas para as quais as ações estão pendentes estão listadas no relatório de saúde. Devem ser feitas mais investigações sobre relatórios de saúde para as réplicas remotas. Também pode haver problemas de comunicação entre este nó e o nó remoto.

Em casos raros, a reconfiguração pode ficar presa devido à comunicação ou a outros problemas entre este nó e o serviço Failover Manager.

* **SourceId**: System.RA
* **Propriedade**: Reconfiguração.
* **Próximos passos**: Investigue réplicas locais ou remotas dependendo da descrição do relatório de saúde.

O exemplo a seguir mostra um relatório de saúde onde uma reconfiguração está presa na réplica local. Nesta amostra, é devido a um serviço que não honra o sinal de cancelamento.

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

O exemplo a seguir mostra um relatório de saúde onde uma reconfiguração é presa à espera de uma resposta de duas réplicas remotas. Neste exemplo, há três réplicas na partição, incluindo a atual primária. 

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

Este relatório de saúde mostra que a reconfiguração está presa à espera de uma resposta de duas réplicas: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Para cada réplica, são dadas as seguintes informações:
- Função de configuração anterior
- Função de configuração atual
- [Estado de réplica](service-fabric-concepts-replica-lifecycle.md)
- ID do nó
- ID de réplica

Para desbloquear a reconfiguração:
- As réplicas **para baixo** devem ser trazidas para cima. 
- As réplicas **inbuild** devem completar a construção e transição para pronto.

### <a name="slow-service-api-call"></a>Chamada API de serviço lento
**System.RAP** e **System.Replicator** reportam um aviso se uma chamada para o código de serviço do utilizador demorar mais do que o tempo configurado. O aviso é apagado quando a chamada terminar.

* **SourceId**: System.RAP ou System.Replicator
* **Propriedade**: O nome da lenta API. A descrição fornece mais detalhes sobre o tempo que a API está pendente.
* **Próximos passos**: Investigue por que a chamada demora mais do que o esperado.

O exemplo a seguir mostra o evento de saúde do System.RAP para um serviço de confiança que não está a honrar o token de cancelamento em **RunAsync**:

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

A propriedade e o texto indicam qual a API ficou presa. Os próximos passos a dar para diferentes APIs presos são diferentes. Qualquer API no *IStatefulServiceReplica* ou *IStatelessServiceInstance* é geralmente um erro no código de serviço. A secção seguinte descreve como estes se traduzem para o [modelo Serviços Fiáveis:](service-fabric-reliable-services-lifecycle.md)

- **IStatefulServiceReplica.Open**: Este aviso indica que uma chamada para `CreateServiceInstanceListeners` , ou se `ICommunicationListener.OpenAsync` overridden , `OnOpenAsync` está presa.

- **IStatefulServiceReplica.Close** and **IStatefulServiceReplica.Abort**: O caso mais comum é um serviço que não honra o sinal de cancelamento passado para `RunAsync` . Também pode ser que `ICommunicationListener.CloseAsync` , ou se ultrapassado, esteja `OnCloseAsync` preso.

- **IStatefulServiceReplica.ChangeRole(S)** e **IStatefulServiceReplica.ChangeRole(N)**: O caso mais comum é um serviço que não honra o sinal de cancelamento passado para `RunAsync` . Neste cenário, a melhor solução é reiniciar a réplica.

- **IStatefulServiceReplica.ChangeRole(P)**: O caso mais comum é que o serviço não devolveu uma tarefa de `RunAsync` .

Outras chamadas da API que podem ficar presas estão na interface **IReplicator.** Por exemplo:

- **IReplicator.CatchupReplicaSet**: Este aviso indica uma de duas coisas. Não há réplicas suficientes. Para ver se é esse o caso, veja o estado de replicação das réplicas na partição ou o relatório de saúde System.FM para uma reconfiguração. Ou as réplicas não estão a reconhecer operações. O cmdlet PowerShell `Get-ServiceFabricDeployedReplicaDetail` pode ser usado para determinar o progresso de todas as réplicas. O problema reside nas réplicas cujo `LastAppliedReplicationSequenceNumber` valor está por detrás do valor `CommittedSequenceNumber` primário.

- **IReplicator.BuildReplica: \<Remote ReplicaId>** Este aviso indica um problema no processo de construção. Para mais informações, consulte [o ciclo de vida da Réplica.](service-fabric-concepts-replica-lifecycle.md) Pode ser devido a uma configuração errada do endereço do replicador. Para obter mais informações, consulte [Configure Stateful Reliable Services](service-fabric-reliable-services-configuration.md) e [Especifique recursos num manifesto de serviço.](service-fabric-service-manifest-resources.md) Também pode ser um problema no nó remoto.

### <a name="replicator-system-health-reports"></a>Relatórios de saúde do sistema de replicadores
**Fila de replicação cheia:** 
 **System.Replicator** reporta um aviso quando a fila de replicação está cheia. Na primária, a fila de replicação geralmente fica cheia porque uma ou mais réplicas secundárias são lentas a reconhecer operações. No secundário, isto geralmente acontece quando o serviço é lento para aplicar as operações. O aviso é apagado quando a fila já não está cheia.

* **SourceId**: System.Replicator
* **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus,** dependendo da função de réplica.
* **Próximos passos**: Se o relatório for sobre o primário, verifique a ligação entre os nós no aglomerado. Se todas as ligações forem saudáveis, pode haver pelo menos um secundário lento com uma alta latência do disco para aplicar operações. Se o relatório for secundário, verifique primeiro a utilização do disco e o desempenho no nó. Em seguida, verifique a ligação de saída do nó lento para o primário.

**RemoteReplicatorConnectionStatus:** 
 **System.Replicator** na réplica primária relata um aviso quando a ligação a um replicador secundário (remoto) não é saudável. O endereço do replicador remoto é mostrado na mensagem do relatório, o que torna mais conveniente detetar se a configuração errada foi transmitida ou se existem problemas de rede entre os replicadores.

* **SourceId**: System.Replicator
* **Propriedade**: **RemoteReplicatorConnectionStatus**.
* **Seguintes etapas**: Verifique a mensagem de erro e certifique-se de que o endereço do replicador remoto está configurado corretamente. Por exemplo, se o replicador remoto for aberto com o endereço de escuta "localhost", não é acessível a partir do exterior. Se o endereço parecer correto, verifique a ligação entre o nó primário e o endereço remoto para encontrar eventuais problemas de rede.

### <a name="replication-queue-full"></a>Fila de replicação cheia
**System.Replicator** reporta um aviso quando a fila de replicação está cheia. Na primária, a fila de replicação geralmente fica cheia porque uma ou mais réplicas secundárias são lentas a reconhecer operações. No secundário, isto geralmente acontece quando o serviço é lento para aplicar as operações. O aviso é apagado quando a fila já não está cheia.

* **SourceId**: System.Replicator
* **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus,** dependendo da função de réplica.

### <a name="slow-naming-operations"></a>Operações de nomeação lenta
**System.NamingService** informa a saúde na sua réplica primária quando uma operação de nomeação demora mais do que aceitável. Exemplos de operações de nomeação são [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) ou [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Mais métodos podem ser encontrados sob o FabricClient. Por exemplo, podem ser encontrados sob [métodos de gestão de serviços](/dotnet/api/system.fabric.fabricclient.servicemanagementclient) ou [métodos de gestão de propriedades.](/dotnet/api/system.fabric.fabricclient.propertymanagementclient)

> [!NOTE]
> O serviço Naming resolve nomes de serviço para uma localização no cluster. Os utilizadores podem usá-lo para gerir nomes de serviços e propriedades. É um serviço de tecido de serviço persistido. Uma das divisórias representa o Proprietário da *Autoridade,* que contém metadados sobre todos os nomes e serviços do Service Fabric. Os nomes do Tecido de Serviço são mapeados para diferentes divisórias, chamadas partições *name Owner,* de modo que o serviço é extensível. Leia mais sobre o [serviço Naming](service-fabric-architecture.md).
> 
> 

Quando uma operação de nomeação demora mais do que o esperado, a operação é sinalizada com um relatório de aviso sobre a réplica primária da partição de serviço de nomeação que serve a operação. Se a operação terminar com sucesso, o aviso será apagado. Se a operação estiver concluída com um erro, o relatório de saúde inclui detalhes sobre o erro.

* **SourceId**: System.NamingService
* **Propriedade**: Começa com o prefixo "**Duration_**" e identifica o funcionamento lento e o nome do Tecido de Serviço no qual a operação é aplicada. Por exemplo, se criar serviço no tecido de **nome:/MyApp/MyService** demora demasiado tempo, a propriedade é **Duration_AOCreateService.fabric:/MyApp/MyService**. "AO" aponta para o papel da partição de nomeação para este nome e operação.
* **Próximos passos**: Verifique se a operação de nomeação falha. Cada operação pode ter causas de raiz diferentes. Por exemplo, o serviço de eliminação pode ficar preso. O serviço pode estar preso porque o anfitrião da aplicação continua a falhar num nó devido a um erro do utilizador no código de serviço.

O exemplo a seguir mostra uma operação de serviço de criação. A operação demorou mais do que a duração configurada. "AO" recaí e envia trabalho para "NÃO". "NÃO" completou a última operação com o TIMEOUT. Neste caso, a mesma réplica é primária tanto para os papéis "AO" como para "NO".

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

## <a name="deployedapplication-system-health-reports"></a>Relatórios de saúde do sistema de aplicação implementadas
**System.Hosting** é a autoridade em entidades implantadas.

### <a name="activation"></a>Ativação
System.Hosting reporta como OK quando uma aplicação foi ativada com sucesso no nó. Caso contrário, relata um erro.

* **SourceId**: System.Hosting
* **Propriedade**: **Ativação,** incluindo a versão de lançamento.
* **Próximos passos**: Se o pedido não for saudável, investigue por que a ativação falhou.

O exemplo a seguir mostra uma ativação bem sucedida:

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

### <a name="download"></a>Download
System.Hosting reporta um erro se o download do pacote de aplicações falhar.

* **SourceId**: System.Hosting
* **Propriedade**: **Download**, incluindo a versão de lançamento.
* **Passos seguintes**: Investigue por que o download falhou no nó.

## <a name="deployedservicepackage-system-health-reports"></a>Relatórios de saúde do sistema Desembalage implementados
**System.Hosting** é a autoridade em entidades implantadas.

### <a name="service-package-activation"></a>Ativação do pacote de serviço
System.Hosting informa como OK se a ativação do pacote de serviço no nó for bem sucedida. Caso contrário, relata um erro.

* **SourceId**: System.Hosting
* **Propriedade**: Ativação.
* **Passos seguintes**: Investigar por que a ativação falhou.

### <a name="code-package-activation"></a>Ativação do pacote de código
System.Hosting reporta como OK para cada pacote de código se a ativação for bem sucedida. Se a ativação falhar, reporta um aviso como configurado. Se **o CodePackage** não ativar ou terminar com um erro maior do que o **CodePackageHealthErrorThreshold** configurado, o alojamento reporta um erro. Se um pacote de serviço contiver vários pacotes de código, é gerado um relatório de ativação para cada um.

* **SourceId**: System.Hosting
* **Propriedade**: Utiliza o prefixo **CodePackageActivation** e contém o nome do pacote de código e o ponto de entrada como *CodePackageActivation:CodePackageName:SetupEntryPoint/EntryPoint*. Por exemplo, **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Registo do tipo de serviço
System.Hosting reporta como OK se o tipo de serviço tiver sido registado com sucesso. Relata um erro se o registo não for feito a tempo, tal como configurado através da **utilização do ServiceTypeRegistrationTimeout**. Se o tempo de funcionamento estiver fechado, o tipo de serviço não está registado a partir do nó e o alojamento reporta um aviso.

* **SourceId**: System.Hosting
* **Propriedade**: Utiliza o prefixo **ServiceTypeRegistration** e contém o nome do tipo de serviço. Por exemplo, **ServiceTypeRegistration:FileStoreServiceType**.

O exemplo a seguir mostra um pacote de serviços implementado saudável:

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

### <a name="download"></a>Download
System.Hosting reporta um erro se o download do pacote de serviço falhar.

* **SourceId**: System.Hosting
* **Propriedade**: **Download**, incluindo a versão de lançamento.
* **Passos seguintes**: Investigue por que o download falhou no nó.

### <a name="upgrade-validation"></a>Validação de upgrade
System.Hosting reporta um erro se a validação durante a atualização falhar ou se a atualização falhar no nó.

* **SourceId**: System.Hosting
* **Propriedade**: Utiliza o prefixo **FabricUpgradeValidation** e contém a versão de upgrade.
* **Descrição**: Aponta para o erro encontrado.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Capacidade de nó indefinida para métricas de governação de recursos
System.Hosting informa um aviso se as capacidades do nó não estiverem definidas no manifesto do cluster e a configuração para deteção automática for desligada. O Service Fabric levanta um aviso sanitário sempre que o pacote de serviço que utiliza registos [de governação](service-fabric-resource-governance.md) de recursos num nó especificado.

* **SourceId**: System.Hosting
* **Propriedade**: **ResourceGovernance**.
* **Próximos passos**: A forma preferida de ultrapassar este problema é alterar o manifesto de cluster para permitir a deteção automática dos recursos disponíveis. Outra forma é atualizar o manifesto do cluster com capacidades de nó corretamente especificadas para estas métricas.

## <a name="next-steps"></a>Passos seguintes
* [Ver relatórios de saúde do Service Fabric](service-fabric-view-entities-aggregated-health.md)

* [Como reportar e verificar a saúde do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Atualização da aplicação do Tecido de Serviço](service-fabric-application-upgrade.md)
