---
title: Cluster Resource Manager - Integração de Gestão
description: Uma visão geral dos pontos de integração entre o Cluster Resource Manager e a Service Fabric Management.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 50751c7d23797a597dc5e2d209c1e3eecf6f7a40
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386260"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integração de gestor de recursos cluster com gestão de cluster de tecido de serviço
O Gestor de Recursos de Cluster de Tecidos de Serviço não conduz upgrades em Tecido de Serviço, mas está envolvido. A primeira forma que o Cluster Resource Manager ajuda na gestão é rastreando o estado desejado do cluster e os serviços no seu interior. O Cluster Resource Manager envia relatórios de saúde quando não consegue colocar o cluster na configuração desejada. Por exemplo, se não houver capacidade suficiente, o Gestor de Recursos de Cluster envia avisos de saúde e erros que indiquem o problema. Outra parte da integração tem a ver com o funcionar das atualizações. O Cluster Resource Manager altera ligeiramente o seu comportamento durante as atualizações.  

## <a name="health-integration"></a>Integração da saúde
O Cluster Resource Manager segue constantemente as regras que definiu para a colocação dos seus serviços. Também rastreia a capacidade restante para cada métrica nos nós e no cluster e no cluster como um todo. Se não conseguir cumprir essas regras ou se não houver capacidade suficiente, são emitidos avisos de saúde e erros. Por exemplo, se um nó tiver excesso de capacidade e o Gestor de Recursos de Cluster tentará corrigir a situação através da mudança de serviços. Se não conseguir corrigir a situação, emite um aviso sanitário indicando qual o nó acima da capacidade e para que métricas.

Outro exemplo dos avisos de saúde do Gestor de Recursos é a violação dos constrangimentos de colocação. Por exemplo, se definiu uma restrição de colocação (como `“NodeColor == Blue”`) e o Gestor de Recursos detetar uma violação desse constrangimento, emite um aviso de saúde. Isto é verdade para restrições personalizadas e as restrições predefinidas (como os constrangimentos do Domínio de Falha e do Domínio de Atualização).

Aqui está um exemplo de um relatório de saúde. Neste caso, o relatório de saúde é para uma das divisórias do serviço de sistema. A mensagem de saúde indica que as réplicas dessa divisória são temporariamente embaladas em poucos Domínios de Upgrade.

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

Eis o que esta mensagem de saúde nos está a dizer:

1. Todas as réplicas em si são saudáveis: Cada um tem AgregadoHealthState : Ok
2. A restrição de distribuição do Domínio de Atualização está atualmente a ser violada. Isto significa que um domínio de upgrade particular tem mais réplicas desta divisória do que deveria.
3. Que nó contém a réplica causando a violação. Neste caso é o nó com o nome "Nó.8"
4. Se está a acontecer uma atualização para esta partição ("Atualmente A atualizar -- falso")
5. A política de distribuição deste serviço: "Política de Distribuição -- Embalagem". Isto rege-se pela política de [colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)`RequireDomainDistribution`. A "embalagem" indica que, neste caso, a DomainDistribution _não_ era necessária, pelo que sabemos que a política de colocação não foi especificada para este serviço. 
6. Quando o relatório aconteceu - 8/10/2015 19:13:02

Informações como esta alertam que o fogo na produção para que saiba que algo correu mal e também é usado para detetar e travar maus upgrades. Neste caso, queremos ver se conseguimos descobrir porque é que o Gestor de Recursos teve de embalar as réplicas para o Domínio de Atualização. Normalmente, a embalagem é transitória porque os nós nos outros Domínios de Upgrade estavam em baixo, por exemplo.

Digamos que o Gestor de Recursos do Cluster está a tentar colocar alguns serviços, mas não há soluções que funcionem. Quando os serviços não podem ser colocados, é geralmente por uma das seguintes razões:

1. Alguma condição transitória tornou impossível colocar esta instância de serviço ou replicar corretamente
2. Os requisitos de colocação do serviço são inatisáveis.

Nestes casos, relatórios de saúde do Cluster Resource Manager ajudam-no a determinar por que o serviço não pode ser colocado. Chamamos a este processo a sequência de eliminação de restrições. Durante o mesmo, o sistema percorre os constrangimentos configurados que afetam o serviço e regista o que elimina. Desta forma, quando os serviços não são capazes de ser colocados, você pode ver quais nós foram eliminados e porquê.

## <a name="constraint-types"></a>Tipos de restrição
Vamos falar de cada um dos diferentes constrangimentos nestes relatórios de saúde. Verá mensagens de saúde relacionadas com estes constrangimentos quando as réplicas não podem ser colocadas.

* **ReplicaExclusionStatic** e **ReplicaExclusionDynamic**: Estes constrangimentos indicam que uma solução foi rejeitada porque dois objetos de serviço da mesma divisória teriam de ser colocados no mesmo nó. Isto não é permitido porque então a falha do nó teria um impacto excessivo na partição. ReplicaExclusionStatic e ReplicaExclusionDynamic são quase a mesma regra e as diferenças realmente não importam. Se estiver a ver uma sequência de eliminação de restrições contendo a restrição ReplicaExclusionStatic ou ReplicaExclusionDynamic, o Gestor de Recursos de Cluster acha que não existem nós suficientes. Isto requer soluções restantes para utilizar estas colocações inválidas que são proibidas. Os outros constrangimentos na sequência costumam dizer-nos porque é que os nós estão a ser eliminados.
* **PlacementRestri**: Se vir esta mensagem, significa que eliminámos alguns nós porque não correspondiam às restrições de colocação do serviço. Traçamos os constrangimentos de colocação configurados atualmente como parte desta mensagem. Isto é normal se tiver uma restrição de colocação definida. No entanto, se a restrição de colocação estiver a causar incorretamente a eliminação de demasiados nós, é assim que notaria.
* **NodeCapacidade**: Esta restrição significa que o Gestor de Recursos de Cluster não poderia colocar as réplicas nos nós indicados porque isso as colocaria acima da capacidade.
* **Afinidade**: Esta restrição indica que não conseguimos colocar a réplica nos nós afetados, uma vez que causaria uma violação da restrição de afinidade. Mais informações sobre afinidade estão [neste artigo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** e **UpgradeDomain**: Esta restrição elimina os nós se a colocação da réplica nos nós indicado causar a embalagem num determinado domínio de avaria ou atualização. Vários exemplos que discutem esta restrição são apresentados no tópico sobre [falhas e upgrade de restrições](service-fabric-cluster-resource-manager-cluster-description.md) de domínio e comportamento resultante
* **PreferredLocation**: Normalmente não se deve ver esta restrição a remover os nós da solução, uma vez que funciona como uma otimização por padrão. A restrição de localização preferida também está presente durante as atualizações. Durante a atualização é usado para transferir os serviços de volta para onde estavam quando a atualização começou.

## <a name="blocklisting-nodes"></a>Nódosos blocklisting
Outra mensagem de saúde que o Gestor de Recursos de Cluster relata é quando os nós estão bloqueados. Pode pensar na listagem de bloqueiocomo uma restrição temporária que é automaticamente aplicada para si. Os nódosos são bloqueados quando experimentam falhas repetidas ao lançar casos desse tipo de serviço. Os nódosos estão bloqueados por tipo de serviço. Um nó pode ser bloqueado para um tipo de serviço, mas não outro. 

Verá o blocklisting fazer efeito frequentemente durante o desenvolvimento: algum bug faz com que o seu anfitrião de serviço se despenhe no arranque. O Serviço Fabric tenta criar o anfitrião do serviço algumas vezes, e a falha continua a ocorrer. Após algumas tentativas, o nó fica bloqueado, e o Gestor de Recursos do Cluster tentará criar o serviço em outro lugar. Se essa falha continuar a acontecer em vários nós, é possível que todos os nós válidos do cluster acabem bloqueados. A blocklisting também pode remover tantos nódosos que não podem lançar com sucesso o serviço para cumprir a escala desejada. Normalmente, verá erros ou avisos adicionais do Cluster Resource Manager indicando que o serviço está abaixo da réplica ou contagem de instâncias desejada, bem como mensagens de saúde que indicam qual é a falha que está a levar à listagem de bloqueios no primeiro lugar.

Blocklisting não é uma condição permanente. Após alguns minutos, o nó é removido da lista de bloqueios e o Tecido de Serviço pode voltar a ativar os serviços nesse nó. Se os serviços continuarem a falhar, o nó está novamente bloqueado para esse tipo de serviço. 

### <a name="constraint-priorities"></a>Prioridades de restrição

> [!WARNING]
> A alteração das prioridades de restrição não é recomendada e pode ter efeitos adversos significativos no seu cluster. As informações abaixo são fornecidas para referência das prioridades de restrição padrão e do seu comportamento. 
>

Com todos estes constrangimentos, pode ter pensado "Hey – Acho que as restrições de domínio de falha são a coisa mais importante no meu sistema. Para garantir que a restrição de domínio de avaria não seja violada, estou disposto a violar outros constrangimentos."

Os constrangimentos podem ser configurados com diferentes níveis prioritários. Nomeadamente:

   - "duro" (0)
   - "macio" (1)
   - "otimização" (2)
   - "off" (-1). 
   
A maioria dos constrangimentos são configurados como restrições duras por padrão.

Mudar a prioridade dos constrangimentos é incomum. Houve alturas em que as prioridades de restrição precisavam de mudar, normalmente para contornar algum outro inseto ou comportamento que estava a afetar o ambiente. Geralmente, a flexibilidade da infraestrutura prioritária de restrição funcionou muito bem, mas não é necessária com frequência. A maior parte do tempo tudo está nas suas prioridades padrão. 

Os níveis prioritários não significam que um dado constrangimento _seja_ violado, nem que seja sempre cumprido. As prioridades de restrição definem uma ordem em que os constrangimentos são aplicados. As prioridades definem as compensações quando é impossível satisfazer todos os constrangimentos. Normalmente todos os constrangimentos podem ser satisfeitos a menos que haja algo mais acontecendo no ambiente. Alguns exemplos de cenários que levarão a violações de restrições são constrangimentos contraditórios, ou um grande número de falhas simultâneas.

Em situações avançadas, pode alterar as prioridades de restrição. Por exemplo, diga que queria garantir que a afinidade seria sempre violada quando necessário para resolver problemas de capacidade do nó. Para tal, pode definir a prioridade da restrição de afinidade a "soft" (1) e deixar a restrição de capacidade definida para "difícil" (0).

Os valores prioritários por defeito para os diferentes constrangimentos são especificados no seguinte config:

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

via ClusterConfig.json para implantações autónomas ou template.json para clusters alojados em Azure:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Restrições de domínio de falha e atualização de domínio
O Cluster Resource Manager quer manter os serviços distribuídos entre domínios de falha e atualização. Modela isto como uma restrição dentro do motor do Cluster Resource Manager. Para obter mais informações sobre como são usados e o seu comportamento específico, consulte o artigo sobre a [configuração](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior)do cluster .

O Cluster Resource Manager pode ter de embalar algumas réplicas num domínio de upgrade para lidar com upgrades, falhas ou outras violações de restrições. A embalagem em domínios de avaria ou de atualização normalmente só acontece quando há várias falhas ou outras falhas no sistema que impedem a correta colocação. Se pretender evitar a embalagem mesmo durante estas situações, pode utilizar a política de [colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)`RequireDomainDistribution`. Note que isto pode afetar a disponibilidade e a fiabilidade do serviço como um efeito colateral, por isso considere-o cuidadosamente.

Se o ambiente estiver configurado corretamente, todos os constrangimentos são totalmente respeitados, mesmo durante as atualizações. O importante é que o Gestor de Recursos de Cluster está atento aos seus constrangimentos. Quando deteta uma violação, reporta-a imediatamente e tenta corrigir o problema.

## <a name="the-preferred-location-constraint"></a>A restrição de localização preferida
A restrição PreferredLocation é um pouco diferente, uma vez que tem dois usos diferentes. Uma utilização desta restrição é durante as atualizações da aplicação. O Cluster Resource Manager gere automaticamente esta restrição durante as atualizações. É utilizado para garantir que, quando as atualizações estiverem concluídas, as réplicas regressem aos seus locais iniciais. A outra utilização da restrição PreferredLocation [destina-se à política de colocação`PreferredPrimaryDomain`.](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) Ambas são otimizações, e por isso a restrição PreferredLocation é o único constrangimento definido para "Otimização" por padrão.

## <a name="upgrades"></a>Atualizações
O Cluster Resource Manager também ajuda durante as atualizações de aplicações e clusters, durante as quais tem dois empregos:

* garantir que as regras do cluster não são comprometidas
* tentar ajudar a atualização a ir sem problemas

### <a name="keep-enforcing-the-rules"></a>Continua a impor as regras.
O principal a ter em conta é que as regras – os restrições estritas como restrições de colocação e capacidades – continuam a ser aplicadas durante as atualizações. Os constrangimentos de colocação asseguram que as suas cargas de trabalho só funcionam onde são permitidas, mesmo durante as atualizações. Quando os serviços estão altamente limitados, as atualizações podem demorar mais tempo. Quando o serviço ou o nó em funcionamento são trazidos para baixo para uma atualização, podem existir poucas opções para onde pode ir.

### <a name="smart-replacements"></a>Substituições inteligentes
Quando uma atualização começa, o Gestor de Recursos tira uma foto do atual arranjo do cluster. À medida que cada Domínio de Upgrade completa, tenta devolver os serviços que estavam nesse Domínio de Upgrade ao seu arranjo original. Desta forma existem, no máximo, duas transições para um serviço durante a atualização. Há uma mudança para fora do nó afetado e um voltar para dentro. Devolver o cluster ou serviço à forma como era antes da atualização também garante que a atualização não afeta o layout do cluster. 

### <a name="reduced-churn"></a>Churn reduzido
Outra coisa que acontece durante as atualizações é que o Gestor de Recursos de Cluster desliga o equilíbrio. Evitar o equilíbrio evita reações desnecessárias à própria atualização, como a deslocação de serviços para nós que foram esvaziados para a atualização. Se a atualização em questão for uma atualização do Cluster, todo o cluster não é equilibrado durante a atualização. As verificações de restrição mantêm-se ativas, apenas o movimento com base no equilíbrio proactivo das métricas é desativado.

### <a name="buffered-capacity--upgrade"></a>Capacidade e atualização tamponadas
Geralmente, pretende que a atualização esteja concluída mesmo que o cluster esteja limitado ou perto do seu estado. Gerir a capacidade do cluster é ainda mais importante durante as atualizações do que o habitual. Dependendo do número de domínios de atualização, entre 5 e 20 por cento da capacidade deve ser migrada à medida que a atualização passa pelo cluster. O trabalho tem que ir para algum lugar. É aqui que a noção de [capacidades tamponadas](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) é útil. A capacidade tamponada é respeitada durante o funcionamento normal. O Gestor de Recursos de Cluster pode preencher nós até à sua capacidade total (consumindo o tampão) durante as atualizações, se necessário.

## <a name="next-steps"></a>Passos seguintes
* Comece do início e obtenha uma introdução ao Gestor de Recursos de Cluster de [Tecidos de Serviço](service-fabric-cluster-resource-manager-introduction.md)
