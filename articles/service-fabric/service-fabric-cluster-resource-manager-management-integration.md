---
title: Cluster Resource Manager - Integração de Gestão
description: Uma visão geral dos pontos de integração entre o Cluster Resource Manager e a Service Fabric Management.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ae80ac5833e90164fc4ff92010fd1830ae932cd2
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174049"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integração de gestor de recursos de cluster com gestão de clusters de tecido de serviço
O Service Fabric Cluster Resource Manager não impulsiona upgrades no Tecido de Serviço, mas está envolvido. A primeira forma de o Cluster Resource Manager ajudar na gestão é acompanhando o estado desejado do cluster e os serviços no seu interior. O Cluster Resource Manager envia relatórios de saúde quando não consegue colocar o cluster na configuração desejada. Por exemplo, se não houver capacidade suficiente, o Gestor de Recursos do Cluster envia avisos de saúde e erros que indiquem o problema. Outra peça de integração tem a ver com o funcionaamento das atualizações. O Cluster Resource Manager altera ligeiramente o seu comportamento durante as atualizações.  

## <a name="health-integration"></a>Integração na saúde
O Cluster Resource Manager acompanha constantemente as regras que definiu para a colocação dos seus serviços. Também rastreia a capacidade remanescente para cada métrica nos nós e no cluster e no cluster como um todo. Se não puder satisfazer essas regras ou se não houver capacidade suficiente, são emitidos avisos e erros de saúde. Por exemplo, se um nó estiver sobrecapacidade e o Cluster Resource Manager tentará corrigir a situação através de serviços de mudança. Se não conseguir corrigir a situação, emite um aviso sanitário indicando qual o nó que está sobre a capacidade e para que métricas.

Outro exemplo dos avisos de saúde do Gestor de Recursos são as violações dos constrangimentos de colocação. Por exemplo, se tiver definido um constrangimento de colocação `“NodeColor == Blue”` (como) e o Gestor de Recursos detetar uma violação desse constrangimento, emite um aviso sanitário. Isto é verdade para restrições personalizadas e as restrições padrão (como as restrições de domínio de falha e de upgrade do domínio).

Aqui está um exemplo de um relatório de saúde. Neste caso, o relatório de saúde é para uma das divisórias do serviço de sistema. A mensagem de saúde indica que as réplicas dessa partição são temporariamente embaladas em poucos domínios de atualização.

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

Eis o que esta mensagem de saúde nos está a dizer é:

1. Todas as réplicas em si são saudáveis: Cada um tem `AggregatedHealthState : Ok`
2. A restrição de distribuição do Domínio de Atualização está atualmente a ser violada. Isto significa que um determinado Domínio de Upgrade tem mais réplicas desta partição do que deveria.
3. Que nó contém a réplica que causa a violação. Neste caso é o nó com o nome *Nó.8*
4. Se está a acontecer uma atualização para esta partição ("Atualmente Upgrade -- falso")
5. A política de distribuição deste serviço: "Política de Distribuição -- Embalagem". Isto rege-se pela política de `RequireDomainDistribution` [colocação.](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing) *A embalagem* indica que, neste caso, a distribuição do Domínio _não_ foi necessária, pelo que sabemos que a política de colocação não foi especificada para este serviço. 
6. Quando o relatório aconteceu - 8/10/2015 19:13:02

Informações como esta alertam que o fogo na produção para que saiba que algo correu mal e também é usado para detetar e travar maus upgrades. Neste caso, gostaríamos de ver se conseguimos descobrir porque é que o Gestor de Recursos teve de embalar as réplicas para o Domínio de Atualização. Normalmente, a embalagem é transitória porque os nós nos outros domínios de upgrade estavam em baixo, por exemplo.

Digamos que o Cluster Resource Manager está a tentar colocar alguns serviços, mas não há soluções que funcionem. Quando os serviços não podem ser colocados, é geralmente por uma das seguintes razões:

1. Alguma condição transitória tornou impossível colocar esta instância de serviço ou réplica corretamente
2. Os requisitos de colocação do serviço são insatisfaíveis.

Nestes casos, os relatórios de saúde do Cluster Resource Manager ajudam-no a determinar por que razão o serviço não pode ser colocado. Chamamos a este processo a sequência de eliminação de restrições. Durante o mesmo, o sistema percorre os constrangimentos configurados que afetam o serviço e regista o que eliminam. Desta forma, quando os serviços não são capazes de ser colocados, você pode ver quais nós foram eliminados e porquê.

## <a name="constraint-types"></a>Tipos de restrição
Vamos falar sobre cada um dos diferentes constrangimentos nestes relatórios de saúde. Verá mensagens de saúde relacionadas com estes constrangimentos quando as réplicas não podem ser colocadas.

* **ReplicaExclusionStatic** and **ReplicaExclusionDynamic**: Estes constrangimentos indicam que uma solução foi rejeitada porque dois objetos de serviço da mesma partição teriam de ser colocados no mesmo nó. Isto não é permitido porque então o fracasso desse nó teria um impacto excessivo nessa partição. ReplicaExclusionStatic e ReplicaExclusionDynamic são quase a mesma regra e as diferenças realmente não importam. Se estiver a ver uma sequência de eliminação de restrição que contenha a restrição ReplicaExclusionStatic ou replicaExclusionDynamic, o Cluster Resource Manager acha que não existem nós suficientes. Isto requer soluções restantes para utilizar estas colocações inválidas, que são proibidas. Os outros constrangimentos na sequência geralmente nos dirão por que os nós estão sendo eliminados em primeiro lugar.
* **ColocaçãoConstrato**: Se vir esta mensagem, significa que eliminámos alguns nós porque não correspondiam às restrições de colocação do serviço. Rastreamos os constrangimentos de colocação atualmente configurados como parte desta mensagem. Isto é normal se tiver uma restrição de colocação definida. No entanto, se a restrição de colocação estiver incorretamente a causar a eliminação de demasiados nós, é assim que se nota.
* **NodeCapacity**: Esta restrição significa que o Cluster Resource Manager não conseguiu colocar as réplicas nos nós indicados porque isso os colocaria sobre a capacidade.
* **Afinidade**: Este constrangimento indica que não conseguimos colocar a réplica nos nós afetados, uma vez que causaria uma violação da restrição de afinidade. Mais informações sobre afinidade está [neste artigo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** e **UpgradeDomain**: Esta restrição elimina os nós se a colocação da réplica nos nós indicados causaria a embalagem num determinado domínio de avaria ou de atualização. Vários exemplos que discutem esta restrição são apresentados no tópico sobre [falhas e atualização de restrições de domínio e comportamento resultante](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: Normalmente não deve ver este constrangimento removendo os nós da solução, uma vez que funciona como uma otimização por defeito. A restrição de localização preferida também está presente durante as atualizações. Durante a atualização, é usado para mover os serviços de volta para onde estavam quando a atualização começou.

## <a name="blocklisting-nodes"></a>Nól de Bloqueio
Outra mensagem de saúde que o Cluster Resource Manager relata é quando os nós são bloqueados. Pode pensar na lista de bloqueios como uma restrição temporária que é automaticamente aplicada a si. Os nós ficam bloqueados quando experimentam falhas repetidas ao lançar casos desse tipo de serviço. Os nós são bloqueados por serviço. Um nó pode ser bloqueado para um tipo de serviço, mas não para outro. 

Você verá o bloqueio de entrada em vigor muitas vezes durante o desenvolvimento: Alguns bugs fazem com que o seu anfitrião de serviço caia no arranque, o Service Fabric tenta criar o anfitrião do serviço algumas vezes, e a falha continua a ocorrer. Após algumas tentativas, o nó fica bloqueado, e o Cluster Resource Manager tentará criar o serviço em outro lugar. Se essa falha continuar a acontecer em múltiplos nós, é possível que todos os nós válidos no cluster acabem bloqueados. A lista de bloqueio também pode remover tantos nós que não o suficiente pode lançar o serviço com sucesso para atender à escala desejada. Normalmente, verá erros ou avisos adicionais do Cluster Resource Manager indicando que o serviço está abaixo da réplica ou contagem de exemplos pretendidos, bem como mensagens de saúde que indicam qual é a falha que está a levar à lista de bloqueios em primeiro lugar.

Bloquear não é uma condição permanente. Após alguns minutos, o nó é removido da lista de bloqueio e o Tecido de Serviço pode voltar a ativar os serviços nesse nó. Se os serviços continuarem a falhar, o nó está bloqueado para esse tipo de serviço novamente. 

### <a name="constraint-priorities"></a>Prioridades de restrição

> [!WARNING]
> Alterar as prioridades de restrição não é recomendado e pode ter efeitos adversos significativos no seu cluster. A informação abaixo é fornecida para referência das prioridades de restrição padrão e seu comportamento. 
>

Com todos estes constrangimentos, pode ter pensado "Hey – acho que as restrições de domínio de falhas são a coisa mais importante no meu sistema. Para garantir que a restrição de domínio de falhas não seja violada, estou disposto a violar outros constrangimentos."

Os constrangimentos podem ser configurados com diferentes níveis de prioridade. Esses avisos são:

   - "duro" (0)
   - "macio" (1)
   - "otimização" (2)
   - "off" (-1). 
   
A maioria dos constrangimentos são configurados como restrições difíceis por defeito.

Mudar a prioridade dos constrangimentos é incomum. Houve momentos em que as prioridades de restrição precisavam de mudar, geralmente para contornar algum outro bug ou comportamento que estava a afetar o ambiente. Geralmente, a flexibilidade da infraestrutura prioritária de restrição tem funcionado muito bem, mas não é necessária muitas vezes. Na maior parte do tempo, tudo está nas suas prioridades. 

Os níveis prioritários não significam que um dado constrangimento _será_ violado, nem que será sempre cumprido. As prioridades de restrição definem uma ordem em que os constrangimentos são aplicados. As prioridades definem as compensações quando é impossível satisfazer todos os constrangimentos. Normalmente, todos os constrangimentos podem ser satisfeitos a menos que haja algo mais acontecendo no ambiente. Alguns exemplos de cenários que levarão a violações de restrições são restrições contraditórias, ou um grande número de falhas simultâneas.

Em situações avançadas, pode alterar as prioridades de restrição. Por exemplo, digamos que queria garantir que a afinidade seria sempre violada quando necessário para resolver problemas de capacidade do nó. Para isso, pode definir a prioridade da restrição de afinidade para "soft" (1) e deixar a restrição de capacidade definida como "dura" (0).

Os valores prioritários predefinidos para as diferentes restrições são especificados no seguinte config:

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

através ClusterConfig.jspara implantações autónomas ou Template.jspara aglomerados azure hospedados:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Domínio de avarias e restrições de domínio de upgrade
O Cluster Resource Manager quer manter os serviços espalhados entre domínios de avaria e atualização. Modela isto como uma restrição dentro do motor do Cluster Resource Manager. Para obter mais informações sobre como são usados e o seu comportamento específico, consulte o artigo sobre a [configuração do cluster.](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior)

O Cluster Resource Manager poderá ter de embalar algumas réplicas num domínio de upgrade para lidar com atualizações, falhas ou outras violações de restrições. A embalagem em domínios de avaria ou de atualização normalmente só acontece quando há várias falhas ou outros churns no sistema que impedem a colocação correta. Se desejar evitar a embalagem mesmo durante estas situações, pode utilizar a `RequireDomainDistribution` [política de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Note que fazê-lo pode afetar a disponibilidade e a fiabilidade do serviço como um efeito colateral, por isso considere-o cuidadosamente.

Se o ambiente estiver configurado corretamente, todos os constrangimentos são totalmente respeitados, mesmo durante as atualizações. O importante é que o Gestor de Recursos do Cluster está atento aos seus constrangimentos. Quando deteta uma violação, reporta-a imediatamente e tenta corrigir o problema.

## <a name="the-preferred-location-constraint"></a>A restrição de localização preferida
A restrição deLocalização Preferida é um pouco diferente, uma vez que tem dois usos diferentes. Um dos usos desta restrição é durante as atualizações de aplicações. O Cluster Resource Manager gere automaticamente esta restrição durante as atualizações. É utilizado para garantir que, quando as atualizações estiverem completas, as réplicas regressem às suas localizações iniciais. A outra utilização da restrição delocalização preferencial é para a [ `PreferredPrimaryDomain` política de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Ambas são otimizações, e por isso a restrição deLocalização Preferida é a única restrição definida para "Otimização" por padrão.

## <a name="upgrades"></a>Atualizações
O Cluster Resource Manager também ajuda durante as atualizações de aplicações e clusters, durante as quais tem dois empregos:

* garantir que as regras do cluster não são comprometidas
* tentar ajudar a atualização a correr suavemente

### <a name="keep-enforcing-the-rules"></a>Continua a impor as regras.
O principal a ter em conta é que as regras – os constrangimentos estritos como as restrições de colocação e as capacidades – continuam a ser aplicadas durante as atualizações. As restrições de colocação garantem que as suas cargas de trabalho só funcionam onde podem, mesmo durante as atualizações. Quando os serviços são altamente limitados, as atualizações podem demorar mais tempo. Quando o serviço ou o nó é reduzido para uma atualização, pode haver poucas opções para onde pode ir.

### <a name="smart-replacements"></a>Substituições inteligentes
Quando uma atualização começa, o Gestor de Recursos tira uma foto do arranjo atual do cluster. À medida que cada Domínio de Atualização termina, tenta devolver os serviços que estavam nesse Domínio de Upgrade ao seu arranjo original. Desta forma, há no máximo duas transições para um serviço durante a atualização. Há um movimento para fora do nó afetado e um voltar para dentro. Devolver o cluster ou o serviço à forma como era antes da atualização também garante que a atualização não afeta o layout do cluster. 

### <a name="reduced-churn"></a>Churn reduzido
Outra coisa que acontece durante as atualizações é que o Cluster Resource Manager desliga o equilíbrio. Evitar o equilíbrio evita reações desnecessárias à própria atualização, como a deslocação de serviços para nós que foram esvaziados para a atualização. Se a atualização em questão for uma atualização do Cluster, então todo o cluster não é equilibrado durante a atualização. Os controlos de restrição permanecem ativos, apenas o movimento baseado no equilíbrio proactivo das métricas é desativado.

### <a name="buffered-capacity--upgrade"></a>Atualização de & de capacidade tamponada
Geralmente deseja que a atualização esteja completa, mesmo que o cluster esteja limitado ou perto de cheio. Gerir a capacidade do cluster é ainda mais importante durante as atualizações do que o habitual. Dependendo do número de domínios de atualização, entre 5 e 20 por cento da capacidade deve ser migrada à medida que a atualização passa pelo cluster. Aquele trabalho tem que ir a algum lugar. É aqui que a noção de [capacidades tamponadas](service-fabric-cluster-resource-manager-cluster-description.md#node-buffer-and-overbooking-capacity) é útil. A capacidade tamponada é respeitada durante o funcionamento normal. O Cluster Resource Manager pode preencher nós até à sua capacidade total (consumindo o tampão) durante as atualizações, se necessário.

## <a name="next-steps"></a>Passos seguintes
* Comece do início e [obtenha uma introdução ao Gestor](service-fabric-cluster-resource-manager-introduction.md) de Recursos do Cluster de Tecido de Serviço
