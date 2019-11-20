---
title: Service Fabric o Gerenciador de recursos de cluster – integração de gerenciamento | Microsoft Docs
description: Uma visão geral dos pontos de integração entre o Gerenciador de recursos de cluster e o gerenciamento de Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 2b3ccf16aca04ebd398e2f97007b817cc0a6ef8d
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196501"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integração do Gerenciador de recursos de cluster com o gerenciamento de Cluster Service Fabric
O Service Fabric cluster Resource Manager não impulsiona as atualizações no Service Fabric, mas está envolvido. A primeira maneira que o Gerenciador de recursos de cluster ajuda com o gerenciamento do é rastrear o estado desejado do cluster e os serviços dentro dele. O Gerenciador de recursos de cluster envia relatórios de integridade quando não pode colocar o cluster na configuração desejada. Por exemplo, se não houver capacidade suficiente, o Gerenciador de recursos de cluster enviará avisos de integridade e erros indicando o problema. Outra parte da integração tem a ver com o funcionamento das atualizações. O Gerenciador de recursos de cluster altera seu comportamento um pouco durante as atualizações.  

## <a name="health-integration"></a>Integração de integridade
O Gerenciador de recursos de cluster rastreia constantemente as regras que você definiu para colocar seus serviços. Ele também controla a capacidade restante de cada métrica nos nós e no cluster e no cluster como um todo. Se ele não puder atender a essas regras ou se não houver capacidade suficiente, avisos de integridade e erros serão emitidos. Por exemplo, se um nó estiver acima da capacidade e o Gerenciador de recursos de cluster tentará corrigir a situação movendo os serviços. Se não for possível corrigir a situação, ele emitirá um aviso de integridade indicando qual nó está acima da capacidade e para quais métricas.

Outro exemplo de avisos de integridade do Gerenciador de recursos são violações de restrições de posicionamento. Por exemplo, se você tiver definido uma restrição de posicionamento (como `“NodeColor == Blue”`) e o Gerenciador de recursos detectar uma violação dessa restrição, ele emitirá um aviso de integridade. Isso é verdadeiro para restrições personalizadas e as restrições padrão (como o domínio de falha e as restrições de domínio de atualização).

Aqui está um exemplo de um relatório de integridade. Nesse caso, o relatório de integridade é para uma das partições do serviço do sistema. A mensagem de integridade indica que as réplicas dessa partição são empacotadas temporariamente em poucos domínios de atualização.

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

Veja o que essa mensagem de integridade está informando é:

1. Todas as réplicas em si estão íntegras: cada uma tem AggregatedHealthState: Ok
2. A restrição de distribuição de domínio de atualização está sendo violada no momento. Isso significa que um domínio de atualização específico tem mais réplicas dessa partição do que deveria.
3. Qual nó contém a réplica que está causando a violação. Nesse caso, é o nó com o nome "node. 8"
4. Se uma atualização está acontecendo no momento para esta partição ("Atualizando no momento--false")
5. A política de distribuição para este serviço: "política de distribuição--empacotamento". Isso é regido pela [política de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)de `RequireDomainDistribution`. "Empacotamento" indica que, nesse caso, DomainDistribution _não_ era necessário, portanto, sabemos que a política de posicionamento não foi especificada para esse serviço. 
6. Quando o relatório ocorreu-8/10/2015 7:13:02 PM

Informações como essa habilitam alertas que são acionados em produção para que você saiba que algo deu errado e também é usado para detectar e parar atualizações incorretas. Nesse caso, gostaríamos de ver se podemos descobrir por que o Gerenciador de recursos tinha que empacotar as réplicas no domínio de atualização. Normalmente, o empacotamento é transitório porque os nós nos outros domínios de atualização estavam inativos, por exemplo.

Digamos que o Gerenciador de recursos de cluster esteja tentando posicionar alguns serviços, mas não há soluções que funcionem. Quando os serviços não podem ser colocados, geralmente é por um dos seguintes motivos:

1. Algumas condições transitórias tornaram impossível a colocação dessa instância de serviço ou réplica corretamente
2. Os requisitos de posicionamento do serviço são insatisfatórios.

Nesses casos, os relatórios de integridade do Gerenciador de recursos de cluster ajudam a determinar por que o serviço não pode ser colocado. Chamamos esse processo de sequência de eliminação de restrição. Durante a ti, o sistema percorre as restrições configuradas que afetam o serviço e registra o que elas eliminam. Dessa forma, quando os serviços não podem ser colocados, você pode ver quais nós foram eliminados e por quê.

## <a name="constraint-types"></a>Tipos de restrição
Vamos falar sobre cada uma das diferentes restrições nesses relatórios de integridade. Você verá mensagens de integridade relacionadas a essas restrições quando as réplicas não puderem ser colocadas.

* **ReplicaExclusionStatic** e **ReplicaExclusionDynamic**: essas restrições indicam que uma solução foi rejeitada porque dois objetos de serviço da mesma partição teriam que ser colocados no mesmo nó. Isso não é permitido porque, em seguida, a falha desse nó afetaria essa partição. ReplicaExclusionStatic e ReplicaExclusionDynamic são quase a mesma regra e as diferenças não são realmente importantes. Se você estiver vendo uma sequência de eliminação de restrição contendo a restrição ReplicaExclusionStatic ou ReplicaExclusionDynamic, o Gerenciador de recursos de cluster pensará que não há nós suficientes. Isso exige que as soluções restantes usem esses posicionamentos inválidos que não são permitidos. Em geral, as outras restrições na sequência nos informarão por que os nós estão sendo eliminados em primeiro lugar.
* **PlacementConstraint**: se você vir essa mensagem, isso significa que eliminamos alguns nós porque eles não corresponderam às restrições de posicionamento do serviço. Rastreamos as restrições de posicionamento configuradas no momento como parte desta mensagem. Isso é normal se você tiver uma restrição de posicionamento definida. No entanto, se a restrição de posicionamento estiver incorretamente fazendo com que muitos nós sejam eliminados, isso será como você observaria.
* **NodeCapacity**: essa restrição significa que o Gerenciador de recursos de cluster não pôde colocar as réplicas nos nós indicados, pois isso os colocaria sobre a capacidade.
* **Afinidade**: essa restrição indica que não conseguimos colocar a réplica nos nós afetados, pois isso causaria uma violação da restrição de afinidade. Mais informações sobre afinidade estão neste [artigo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** e **UpgradeDomain**: essa restrição elimina os nós se colocar a réplica nos nós indicados causaria o empacotamento em um domínio de atualização ou de falha específico. Vários exemplos que abordam essa restrição são apresentados no tópico sobre [restrições de domínio de falha e de atualização e comportamento resultante](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: você normalmente não deveria ver essa restrição removendo nós da solução, uma vez que ele é executado como uma otimização por padrão. A restrição de local preferencial também está presente durante as atualizações. Durante a atualização, ela é usada para mover os serviços de volta para onde eles estavam quando a atualização foi iniciada.

## <a name="blocklisting-nodes"></a>Nós inclusão na lista
Outra mensagem de integridade que o Gerenciador de recursos de cluster relata é quando os nós são incluídos. Você pode considerar o inclusão na lista como uma restrição temporária que é aplicada automaticamente para você. Os nós recebem incluídos quando experimentam falhas repetidas ao iniciar instâncias desse tipo de serviço. Os nós são incluídosdos em uma base por tipo de serviço. Um nó pode ser incluídos para um tipo de serviço, mas não para outro. 

Você verá inclusão na lista entrar com frequência durante o desenvolvimento: algum bug faz com que o host de serviço falhe na inicialização. Service Fabric tenta criar o host de serviço algumas vezes e a falha continua ocorrendo. Após algumas tentativas, o nó Obtém incluídos e o Gerenciador de recursos de cluster tentará criar o serviço em outro lugar. Se essa falha continuar acontecendo em vários nós, é possível que todos os nós válidos no cluster acabem bloqueados. O inclusão na lista também pode remover tantos nós que não suficientes podem iniciar o serviço com êxito para atender à escala desejada. Normalmente, você verá erros ou avisos adicionais do Gerenciador de recursos de cluster indicando que o serviço está abaixo da contagem desejada de réplicas ou de instâncias, bem como mensagens de integridade que indicam qual é a falha que está levando ao inclusão na lista na primeira espaço.

Inclusão na lista não é uma condição permanente. Depois de alguns minutos, o nó é removido da barra de bloqueio e Service Fabric pode ativar os serviços nesse nó novamente. Se os serviços continuarem falhando, o nó será incluídos para esse tipo de serviço novamente. 

### <a name="constraint-priorities"></a>Prioridades de restrição

> [!WARNING]
> A alteração das prioridades de restrição não é recomendada e pode ter efeitos adversos significativos no cluster. As informações abaixo são fornecidas para referência das prioridades de restrição padrão e seu comportamento. 
>

Com todas essas restrições, você deve estar pensando: "Ei, acho que restrições de domínio de falha são a coisa mais importante no meu sistema. Para garantir que a restrição de domínio de falha não seja violada, estou disposto a violar outras restrições. "

As restrições podem ser configuradas com diferentes níveis de prioridade. Nomeadamente:

   - "Hard" (0)
   - "soft" (1)
   - "otimização" (2)
   - "off" (-1). 
   
A maioria das restrições é configurada como restrições rígidas por padrão.

A alteração da prioridade das restrições é incomum. Houve ocasiões em que as prioridades de restrição precisavam ser alteradas, geralmente para solucionar algum outro bug ou comportamento que estava afetando o ambiente. Geralmente, a flexibilidade da infra-estrutura de prioridade de restrição funcionou muito bem, mas ela não é necessária com frequência. Na maioria das vezes, tudo reside em suas prioridades padrão. 

Os níveis de prioridade não significam que uma determinada restrição _será_ violada, nem que sempre será atendida. As prioridades de restrição definem uma ordem na qual as restrições são impostas. As prioridades definem as compensações quando é impossível atender a todas as restrições. Normalmente, todas as restrições podem ser satisfeitas, a menos que haja alguma outra coisa acontecendo no ambiente. Alguns exemplos de cenários que resultarão em violações de restrição são restrições conflitantes ou grandes quantidades de falhas simultâneas.

Em situações avançadas, você pode alterar as prioridades de restrição. Por exemplo, digamos que você queria garantir que a afinidade sempre seria violada quando necessário para resolver problemas de capacidade de nó. Para conseguir isso, você pode definir a prioridade da restrição de afinidade como "soft" (1) e deixar a restrição de capacidade definida como "Hard" (0).

Os valores de prioridade padrão para as diferentes restrições são especificados na configuração a seguir:

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

via ClusterConfig. JSON para implantações autônomas ou template. JSON para clusters hospedados do Azure:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Restrições de domínio de falha e de atualização
O Gerenciador de recursos de cluster deseja manter os serviços distribuídos entre domínios de falha e de atualização. Ele modela isso como uma restrição dentro do mecanismo do Gerenciador de recursos de cluster. Para obter mais informações sobre como elas são usadas e seu comportamento específico, confira o artigo sobre [configuração de cluster](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

O Gerenciador de recursos de cluster pode precisar empacotar duas réplicas em um domínio de atualização para lidar com atualizações, falhas ou outras violações de restrição. O empacotamento em domínios de falha ou de atualização normalmente ocorre apenas quando há várias falhas ou outras variações no sistema que impedem o posicionamento correto. Se desejar impedir a compactação mesmo durante essas situações, você poderá utilizar a [política de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)`RequireDomainDistribution`. Observe que isso pode afetar a disponibilidade e a confiabilidade do serviço como um efeito colateral, portanto, considere com cuidado.

Se o ambiente estiver configurado corretamente, todas as restrições serão totalmente respeitadas, mesmo durante as atualizações. O importante é que o Gerenciador de recursos de cluster está observando suas restrições. Quando detecta uma violação, ela a relata imediatamente e tenta corrigir o problema.

## <a name="the-preferred-location-constraint"></a>A restrição de local preferencial
A restrição PreferredLocation é um pouco diferente, pois ela tem dois usos diferentes. Um uso dessa restrição é durante as atualizações do aplicativo. O Gerenciador de recursos de cluster gerencia automaticamente essa restrição durante as atualizações. Ele é usado para garantir que, quando as atualizações forem concluídas, as réplicas retornarão aos seus locais iniciais. O outro uso da restrição PreferredLocation é para a [política de posicionamento`PreferredPrimaryDomain`](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Ambos são otimizações e, portanto, a restrição PreferredLocation é a única restrição definida como "otimização" por padrão.

## <a name="upgrades"></a>Actualizações
O Gerenciador de recursos de cluster também ajuda durante atualizações de aplicativo e cluster, durante as quais ele tem dois trabalhos:

* Verifique se as regras do cluster não estão comprometidas
* Tente ajudar a atualização a seguir sem problemas

### <a name="keep-enforcing-the-rules"></a>Manter a imposição das regras
O principal a ser considerado é que as regras – as restrições estritas, como restrições de posicionamento e capacidades, ainda são impostas durante as atualizações. As restrições de posicionamento garantem que suas cargas de trabalho sejam executadas somente onde elas são permitidas, mesmo durante as atualizações. Quando os serviços são altamente restritos, as atualizações podem levar mais tempo. Quando o serviço ou o nó em que ele está sendo executado é desativado para uma atualização, pode haver poucas opções para onde ele possa ir.

### <a name="smart-replacements"></a>Substituições inteligentes
Quando uma atualização é iniciada, o Gerenciador de recursos tira um instantâneo da organização atual do cluster. À medida que cada domínio de atualização é concluído, ele tenta retornar os serviços que estavam nesse domínio de atualização para sua organização original. Dessa forma, há no máximo duas transições para um serviço durante a atualização. Há uma movimentação do nó afetado e uma volta no. Retornar o cluster ou serviço para a forma como ele estava antes da atualização também garante que a atualização não afete o layout do cluster. 

### <a name="reduced-churn"></a>Rotatividade reduzida
Outra coisa que acontece durante as atualizações é que o Gerenciador de recursos de cluster desativa o balanceamento. Impedir o balanceamento impede reações desnecessárias para a atualização em si, como mover serviços para nós que foram esvaziados para a atualização. Se a atualização em questão for uma atualização de cluster, o cluster inteiro não será balanceado durante a atualização. As verificações de restrição permanecem ativas, apenas a movimentação com base no balanceamento proativo de métricas é desabilitada.

### <a name="buffered-capacity--upgrade"></a>Capacidade em buffer & atualização
Geralmente, você deseja que a atualização seja concluída mesmo que o cluster seja restrito ou esteja perto de cheio. Gerenciar a capacidade do cluster é ainda mais importante durante atualizações do que o normal. Dependendo do número de domínios de atualização, entre 5 e 20 por cento da capacidade devem ser migrados à medida que a atualização passa pelo cluster. Esse trabalho precisa ir em algum lugar. É aí que a noção de [capacidades em buffer](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) é útil. A capacidade em buffer é respeitada durante a operação normal. O Gerenciador de recursos de cluster pode preencher os nós até a capacidade total (consumindo o buffer) durante as atualizações, se necessário.

## <a name="next-steps"></a>Passos seguintes
* Comece desde o início e [obtenha uma introdução ao Gerenciador de recursos de Cluster Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
