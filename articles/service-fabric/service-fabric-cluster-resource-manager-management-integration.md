---
title: Gestor de recursos do Service Fabric Cluster - integração de gestão | Documentos da Microsoft
description: Uma visão geral dos pontos de integração entre o Resource Manager de Cluster e gestão de recursos de infraestrutura do serviço.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: a51593753cab8a6b07d99df46560808de5400047
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737931"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integração de Gestor de recursos de cluster com a gestão de cluster do Service Fabric
O Gestor de recursos de Cluster do Service Fabric não unidade atualizações nos recursos de infraestrutura do serviço, mas está envolvida. É a primeira maneira que o Gestor de recursos de Cluster ajuda com a gestão ao controlar o estado pretendido do cluster e os serviços no interior do mesmo. O Gestor de recursos de Cluster envia os relatórios de estado de funcionamento quando ele não é possível colocar o cluster para a configuração pretendida. Por exemplo, se existir capacidade insuficiente o Gestor de recursos de Cluster envia os avisos de estado de funcionamento e os erros que indicam o problema. Outra parte da integração tem a ver com como funcionam as atualizações. O Gestor de recursos de Cluster altera seu comportamento ligeiramente durante as atualizações.  

## <a name="health-integration"></a>Integração de estado de funcionamento
O Gestor de recursos de Cluster constantemente controla as regras definidas para colocar os seus serviços. Ele também controla a capacidade restante para cada métrica em nós e no cluster e do cluster como um todo. Se ele não é possível atender essas regras, ou se existir capacidade insuficiente, erros e avisos de estado de funcionamento são emitidos. Por exemplo, se um nó é acima da capacidade e o Gestor de recursos do Cluster irá tentar corrigir a situação, a transferência dos serviços. Se ele não é possível corrigir a situação emite um aviso do Estado de funcionamento que indica que o nó está acima da capacidade bem como para as métricas.

Outro exemplo de avisos de estado de funcionamento do Gestor de recursos é violações de restrições de posicionamento. Por exemplo, se definiu uma restrição de posicionamento (como `“NodeColor == Blue”`) e o Gestor de recursos Deteta uma violação de restrição, emite um aviso do Estado de funcionamento. Isso é verdadeiro para restrições personalizadas e as restrições de padrão (como as restrições de domínio de falha e o domínio de atualização).

Eis um exemplo de um desses relatórios de estado de funcionamento. Neste caso, o relatório de estado de funcionamento é uma das partições do serviço de sistema. A mensagem de estado de funcionamento indica que as réplicas dessa partição temporariamente são incluídas em poucos os domínios de atualização.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Eis o que esta mensagem de estado de funcionamento está dizendo é que:

1. Todas as réplicas próprios estão em bom Estadas: Cada um tem AggregatedHealthState: Ok
2. A restrição de distribuição de domínio de atualização é atualmente a ser violada. Isso significa que um determinado domínio de atualização tem mais réplicas desta partição do que devia.
3. Nó que contém a réplica que faz com que a violação. Neste caso é o nó com o nome "Node.8"
4. Se uma atualização está a acontecer para esta partição ("atualmente atualizar--false")
5. A política de distribuição para este serviço: "Política de distribuição, empacotar". Isso é governado pelos `RequireDomainDistribution` [política de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Empacotamento" indica que neste caso DomainDistribution era _não_ necessário, pelo que Sabemos que a política de colocação não foi especificada para este serviço. 
6. Quando o relatório Ocorreu um - 8/10/2015 7 13:02 PM

Informações sobre como este alertas de poderes que são acionados na produção para informá-lo, algo está errado e também é utilizado para detetar e parar atualizações mal-sucedidas. Neste caso, podemos desejar ver se conseguimos descobrir por que o Gestor de recursos tinha que empacotar as réplicas para o domínio de atualização. Normalmente, a empacotar é transitório porque os nós nos outros domínios de atualização foram-se para baixo, por exemplo.

Vamos supor que o Gestor de recursos de Cluster está a tentar colocar alguns serviços, mas não existem quaisquer soluções que funcionam. Quando não não possível colocar a serviços, destina-se, normalmente, um dos seguintes motivos:

1. Alguma condição transitória tem tornaram impossível colocar esta instância de serviço ou a réplica corretamente
2. Requisitos de posicionamento do serviço são unsatisfiable.

Nestes casos, relatórios de estado de funcionamento do Cluster do Resource Manager ajudá-lo a determinar por que não é possível colocar o serviço. Chamamos esse processo com a sequência de eliminação de restrição. Durante a ele, o sistema explica as restrições configuradas afetar o serviço e os registos que eliminam. Desta forma, quando serviços não podem ser colocados, pode ver que nós foram eliminados e por que.

## <a name="constraint-types"></a>Tipos de restrição
Vamos falar sobre cada um dos diferentes restrições nestes relatórios de estado de funcionamento. Poderá ver mensagens de estado de funcionamento relacionado com estas restrições quando não não possível colocar as réplicas.

* **ReplicaExclusionStatic** e **ReplicaExclusionDynamic**: Essas restrições indica que uma solução foi rejeitada porque dois objetos de serviço da mesma partição tinha que ser colocada no mesmo nó. Isso não é permitido porque, em seguida, falha desse nó excessivamente afetaria nessa partição. ReplicaExclusionStatic e ReplicaExclusionDynamic são quase a mesma regra e as diferenças não são muito importam. Se vir uma seqüência de eliminação de restrição contendo o ReplicaExclusionStatic ou ReplicaExclusionDynamic restrição, acha que o Gestor de recursos de Cluster que não existem são suficientes nós. Isso exige soluções restantes para utilizar estes posicionamentos inválidos que não são permitidos. Outras restrições na sequência normalmente nos irão dizer por que nós estão a ser eliminados em primeiro lugar.
* **PlacementConstraint**: Se vir esta mensagem, significa que alguns nós podemos eliminado porque não corresponde a restrições de posicionamento do serviço. Nós de rastreio horizontalmente as restrições de posicionamento atualmente configurada como parte desta mensagem. Isso é normal se tiver uma restrição de posicionamento definida. No entanto, se a restrição de posicionamento incorretamente está a causar muitos de nós ser eliminados é como deve observar.
* **NodeCapacity**: Esta restrição significa que o Gestor de recursos de Cluster não foi possível colocar as réplicas em nós indicado porque que as colocaria acima da capacidade.
* **Afinidade**: Esta restrição indica que não foi possível colocamos a réplica em nós afetados, uma vez que ele faria com que uma violação da restrição de afinidade. Obter mais informações sobre a afinidade são no [neste artigo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** e **UpgradeDomain**: Esta restrição elimina nós se colocar a réplica em nós indicado causaria a empacotar num determinado índice de falhas ou domínio de atualização. Vários exemplos discutindo esta restrição são apresentados no tópico na [restrições de domínio de atualização e de falha e o comportamento resultante](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: Normalmente, não deve ver essa restrição remover nós da solução, uma vez que ele é executado como uma otimização por predefinição. A restrição de localização preferencial também está presente durante as atualizações. Durante a atualização é utilizado para mover serviços para onde estavam quando a atualização iniciada.

## <a name="blocklisting-nodes"></a>Nós Blocklisting
Outra mensagem de estado de funcionamento os relatórios do Gestor de recursos do Cluster é quando nós estão blocklisted. Pode pensar blocklisting como uma restrição temporária que é aplicada automaticamente para. Nós obtém blocklisted ao se deparar com falhas repetidas ao iniciar instâncias desse tipo de serviço. Os nós são blocklisted numa base por--tipo de serviço. Um nó pode ser blocklisted para o tipo de um serviço e não em outro. 

Verá surgir, muitas vezes, durante o desenvolvimento de blocklisting: alguns bugs faz com que seu host de serviço de falhas no arranque. Service Fabric tenta criar o anfitrião do serviço algumas vezes e mantém a ocorrer a falha. Após algumas tentativas, o nó obtém blocklisted e o Gestor de recursos do Cluster irá tentar criar o serviço em outro lugar. Se essa falha mantém a acontecer em vários nós, é possível que todos os nós válido ao final de cluster se bloqueado. Blocklisting também pode remover tantos nós que não o bastante pode iniciar o serviço de acordo com a escala pretendida. Normalmente, irá ver erros ou avisos do Gestor de recursos de Cluster que indica que o serviço for inferior a réplica pretendida ou contagem de instâncias, bem como mensagens de estado de funcionamento que indica o que a falha é que está a originar o blocklisting na primeira adicionais coloca.

Blocklisting não é uma condição permanente. Após alguns minutos, o nó é removido da lista de bloqueios e recursos de infraestrutura do serviço pode ativar os serviços nesse nó novamente. Se os serviços continuam a falhar, o nó é blocklisted para esse tipo de serviço novamente. 

### <a name="constraint-priorities"></a>Prioridades de restrição

> [!WARNING]
> Alterar as prioridades de restrição não é recomendada e pode ter efeitos adversos significativos no seu cluster. As informações abaixo é fornecido para referência das prioridades de restrição predefinida e seu comportamento. 
>

Com todas essas restrições, pode ter sido pensando "EI, acho que as restrições de domínio de falhas são a coisa mais importante no meu sistema. Para garantir que a restrição de domínio de falhas não é violada, estou disposto a violar outras restrições. "

As restrições podem ser configuradas com níveis de prioridade diferente. Nomeadamente:

   - "fixas" (0)
   - "soft" (1)
   - "otimização" (2)
   - "desligado" (-1). 
   
Por predefinição, a maioria das restrições é configurada como restrições de disco rígidas.

Alterar a prioridade das restrições é incomum. Há vezes em que fosse necessário alterar, normalmente, para contornar a alguns outros bug ou comportamento que está a afetar o ambiente de prioridades de restrição. Em geral, a flexibilidade da infra-estrutura de prioridade de restrição tem trabalhado muito bem, mas ele não for necessário com frequência. Na maioria das vezes tudo encontra-se nas suas prioridades predefinidas. 

Os níveis de prioridade não significam que uma restrição de determinado _será_ ser violado, nem que ele sempre será atendido. Prioridades de restrição definem uma ordem na qual são aplicadas as restrições. Prioridades de definem as compensações quando não é possível satisfazer todas as restrições. Normalmente, podem ser satisfeitas a todas as restrições, a menos que há algo mais acontecendo no ambiente. Alguns exemplos de cenários que levará a violações de restrição são restrições em conflito, ou um grande número de falhas em simultâneo.

Em situações avançadas, pode alterar as prioridades de restrição. Por exemplo, digamos que quisesse garantir que afinidade sempre poderia ser violada quando necessário para resolver problemas de capacidade do nó. Para conseguir isso, poderia definir a prioridade da restrição de afinidade para "soft" (1) e deixe a restrição de capacidade definida para "fixas" (0).

Os valores de prioridade de predefinidos para as diferentes restrições são especificados na configuração do seguinte:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

por meio de ClusterConfig.json das implementações autónomas ou Template para o Azure alojados clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Restrições de domínio de atualização e de domínio de falhas
O Gestor de recursos de Cluster quer manter os serviços espalhada entre domínios de falha e atualização. Ele modela isto com uma restrição do motor do Gestor de recursos de Cluster. Para obter mais informações sobre como são utilizados e seu comportamento específico, consulte o artigo sobre [configuração do cluster](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

O Gestor de recursos de Cluster poderá ter de pacote de duas réplicas num domínio de atualização para lidarem com as atualizações, falhas ou outras violações de restrição. Empacotar em domínios de falha ou atualização normalmente ocorre apenas quando existem várias falhas ou outras alterações no sistema de impedir a colocação corretas. Se quiser impedir a remessa, mesmo durante essas situações, pode utilizar o `RequireDomainDistribution` [política de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Tenha em atenção que isto pode afetar a disponibilidade do serviço e a fiabilidade do que um efeito colateral, por isso considere-o com cuidado.

Se o ambiente está configurado corretamente, os todas as restrições, totalmente são respeitadas, até mesmo durante atualizações. O mais importante é que o Gestor de recursos de Cluster está observando as restrições. Quando Deteta uma violação-lo imediatamente relata e tenta corrigir o problema.

## <a name="the-preferred-location-constraint"></a>A restrição de localização preferencial
A restrição de PreferredLocation é um pouco diferente, porque tem dois usos diferentes. É um uso essa restrição durante as atualizações de aplicações. O Gestor de recursos de Cluster gere automaticamente essa restrição durante as atualizações. É utilizado para garantir que quando as atualizações estiverem concluídas-se de que as réplicas retornam para as respetivas localizações iniciais. Outro uso da restrição PreferredLocation é para o [ `PreferredPrimaryDomain` política de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Ambas as opções são otimizações e, por conseguinte, a restrição de PreferredLocation é a única restrição definida como "Otimização" por padrão.

## <a name="upgrades"></a>Atualizações
O Gestor de recursos de Cluster também ajuda a durante a aplicação e as atualizações de cluster, durante o qual tem duas tarefas:

* Certifique-se de que as regras do cluster não sejam comprometidas
* Tente ajudar a atualização ser suave

### <a name="keep-enforcing-the-rules"></a>Manter a impor as regras
O mais importante ter em consideração é que as regras – as restrições rígidas, como restrições de posicionamento e as capacidades - ainda são impostas durante as atualizações. Restrições de posicionamento Certifique-se de que as cargas de trabalho executam apenas em que estão autorizados a, até mesmo durante atualizações. Quando os serviços são altamente restrita, as atualizações podem demorar mais tempo. Quando o serviço ou o nó que está a ser executado no é transmitido para uma atualização poderão existir algumas opções para onde pode aceder.

### <a name="smart-replacements"></a>Substituições inteligentes
Quando uma atualização é iniciado, o Gestor de recursos tira um instantâneo da disposição atual do cluster. Como cada domínio de atualização estiver concluída, ele tenta devolver os serviços que estavam nesse domínio de atualização de mensagens em fila à sua disposição original. Dessa forma existem no máximo dois transições para um serviço durante a atualização. Há uma mudança fora do nó afetado e um mover de volta. Devolver o cluster ou o serviço ao como era antes da atualização também garante que a atualização não tem impacto no layout do cluster. 

### <a name="reduced-churn"></a>Alterações a dados reduzida
Outra coisa que acontece durante as atualizações que é a Resource Manager de Cluster desliga balanceamento. Impedir o balanceamento de impede que as reações desnecessárias para a atualização em si, como a transferência dos serviços em nós que foram retirados para a atualização. Se a atualização em questão for uma atualização do Cluster, todo o cluster não é balanceado durante a atualização. Verificações de restrição permanecem ativas, movimento apenas com base no balanceamento proativa de métricas está desabilitado.

### <a name="buffered-capacity--upgrade"></a>Capacidade em buffer e atualização
Em geral pretende que as atualizações para concluir, mesmo que o cluster é restrito ou próximo completo. Gerir a capacidade do cluster é ainda mais importante durante as atualizações que o habitual. Dependendo do número de domínios de atualização, entre 5 e 20% da capacidade tem de ser migrados como a atualização é efetuada através do cluster. Esse trabalho precisa entrar em algum lugar. É aí que a noção de [colocados em memória intermédia capacidades](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) é útil. Capacidade em buffer é respeitada durante o funcionamento normal. O Gestor de recursos de Cluster poderá preencher nós até sua capacidade total (consumir a memória intermédia) durante as atualizações, se necessário.

## <a name="next-steps"></a>Passos Seguintes
* Começar do início e [obtenha uma introdução para o Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
