---
title: Gerenciador de recursos de Cluster Service Fabric-políticas de posicionamento
description: Visão geral de políticas e regras de posicionamento adicionais para serviços de Service Fabric
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f9861faeaf4ab4049de7404a9e6f8b59a9445fe5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452191"
---
# <a name="placement-policies-for-service-fabric-services"></a>Políticas de posicionamento para serviços do Service Fabric
As políticas de posicionamento são regras adicionais que podem ser usadas para controlar o posicionamento do serviço em alguns cenários específicos e menos comuns. Alguns exemplos desses cenários são:

- Seu cluster de Service Fabric abrange distâncias geográficas, como vários data centers locais ou entre regiões do Azure
- Seu ambiente abrange várias áreas de geopolítica ou controle legal ou algum outro caso em que você tem limites de política que precisa impor
- Há considerações de desempenho ou latência de comunicação devido a grandes distâncias ou uso de links de rede mais lentos ou menos confiáveis
- Você precisa manter determinadas cargas de trabalho posicionadas como um melhor esforço, seja com outras cargas de trabalho ou de proximidade com os clientes

A maioria desses requisitos se alinha com o layout físico do cluster, representado como os domínios de falha do cluster. 

As políticas avançadas de posicionamento que ajudam a resolver esses cenários são:

1. Domínios inválidos
2. Domínios necessários
3. Domínios preferenciais
4. Desautorizando a compactação de réplica

A maioria dos seguintes controles pode ser configurada por meio de propriedades de nó e restrições de posicionamento, mas algumas são mais complicadas. Para tornar as coisas mais simples, o Service Fabric cluster Resource Manager fornece essas políticas de posicionamento adicionais. As políticas de posicionamento são configuradas em uma base de instância de serviço por nome. Eles também podem ser atualizados dinamicamente.

## <a name="specifying-invalid-domains"></a>Especificando domínios inválidos
A política de posicionamento **InvalidDomain** permite que você especifique que um domínio de falha específico é inválido para um serviço específico. Essa política garante que um serviço específico nunca seja executado em uma área específica, por exemplo, por motivos de geopolítica ou política corporativa. Vários domínios inválidos podem ser especificados por meio de políticas separadas.

<center>

![][Image1]
de exemplo de domínio inválido </center>

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Especificando domínios necessários
A política de posicionamento de domínio necessária requer que o serviço esteja presente apenas no domínio especificado. Vários domínios necessários podem ser especificados por meio de políticas separadas.

<center>

![exemplo de domínio necessário][Image2]
</center>

Código:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Especificando um domínio preferencial para as réplicas primárias de um serviço com estado
O domínio primário preferencial especifica o domínio de falha no qual inserir o primário. O primário termina nesse domínio quando tudo está íntegro. Se o domínio ou a réplica primária falhar ou desligar, o primário se moverá para outro local, idealmente no mesmo domínio. Se esse novo local não estiver no domínio preferencial, o Gerenciador de recursos de cluster o moverá de volta para o domínio preferencial assim que possível. Naturalmente, essa configuração faz sentido apenas para serviços com estado. Essa política é mais útil em clusters que são estendidos em regiões do Azure ou em vários data centers, mas têm serviços que preferem o posicionamento em um determinado local. Manter os primários próximos aos seus usuários ou outros serviços ajuda a fornecer latência mais baixa, especialmente para leituras, que são manipuladas por padrão para os primários.

<center>

![domínios primários preferenciais e][Image3]
de failover </center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Exigindo distribuição de réplica e desautorizando a embalagem
As réplicas _normalmente_ são distribuídas entre domínios de falha e de atualização quando o cluster está íntegro. No entanto, há casos em que mais de uma réplica para uma determinada partição pode acabar sendo empacotada temporariamente em um único domínio. Por exemplo, digamos que o cluster tenha nove nós em três domínios de falha, FD:/0, FD:/1 e FD:/2. Digamos também que o serviço tem três réplicas. Digamos que os nós que estavam sendo usados para essas réplicas em FD:/1 e FD:/2 ficaram inativos. Normalmente, o Gerenciador de recursos de cluster prefere outros nós nesses mesmos domínios de falha. Nesse caso, digamos que, devido a problemas de capacidade, nenhum dos outros nós nesses domínios era válido. Se o Gerenciador de recursos de cluster criar substituições para essas réplicas, ele teria que escolher nós no FD:/0. No entanto, _isso cria uma situação em que a_ restrição de domínio de falha é violada. As réplicas de empacotamento aumentam a chance de que todo o conjunto de réplicas possa ficar inativo ou perdido. 

> [!NOTE]
> Para obter mais informações sobre restrições e prioridades de restrição em geral, confira [Este tópico](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Se você já viu uma mensagem de integridade como "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", você atingiu essa condição ou algo parecido. Normalmente, apenas uma ou duas réplicas são reunidas em conjunto temporariamente. Contanto que haja menos do que um quorum de réplicas em um determinado domínio, você é seguro. A embalagem é rara, mas pode acontecer e, geralmente, essas situações são transitórias, já que os nós retornam. Se os nós permanecerem inativos e o Gerenciador de recursos de cluster precisar criar substituições, geralmente haverá outros nós disponíveis nos domínios de falha ideais.

Algumas cargas de trabalho preferem sempre ter o número de destino de réplicas, mesmo se elas estiverem incluídas em menos domínios. Essas cargas de trabalho estão se imtratando do total de falhas de domínio permanentes simultâneas e podem geralmente recuperar o estado local. Outras cargas de trabalho levarão o tempo de inatividade antes da exatidão dos riscos ou da perda de dados. A maioria das cargas de trabalho de produção é executada com mais de três réplicas, mais de três domínios de falha e muitos nós válidos por domínio de falha. Por isso, o comportamento padrão permite o empacotamento de domínio por padrão. O comportamento padrão permite o balanceamento normal e o failover para lidar com esses casos extremos, mesmo que isso signifique um empacotamento de domínio temporário.

Se desejar desabilitar tal empacotamento para uma determinada carga de trabalho, você poderá especificar a política de `RequireDomainDistribution` no serviço. Quando essa política é definida, o Gerenciador de recursos de cluster garante que duas réplicas da mesma partição sejam executadas no mesmo domínio de falha ou de atualização.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível usar essas configurações para serviços em um cluster que não foi distribuído geograficamente? Você poderia, mas também não há uma grande razão. As configurações de domínio necessárias, inválidas e preferenciais devem ser evitadas, a menos que os cenários as exijam. Não faz sentido tentar forçar uma determinada carga de trabalho a ser executada em um único rack ou prefere algum segmento de seu cluster local em relação a outra. Configurações de hardware diferentes devem ser distribuídas entre domínios de falha e manipuladas por meio de restrições de posicionamento normal e propriedades de nó.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como configurar serviços, [saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
