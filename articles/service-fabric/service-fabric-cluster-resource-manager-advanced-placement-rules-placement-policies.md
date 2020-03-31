---
title: Gestor de recursos de cluster de tecido de serviço - Políticas de colocação
description: Visão geral das políticas e regras adicionais de colocação para serviços de tecido
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9aea157d03f344e07a81f0588d3e0127f17ca75d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834426"
---
# <a name="placement-policies-for-service-fabric-services"></a>Políticas de colocação para serviços de tecido de serviço
As políticas de colocação são regras adicionais que podem ser usadas para governar a colocação de serviços em alguns cenários específicos e menos comuns. Alguns exemplos desses cenários são:

- O seu cluster De Serviço Fabric abrange distâncias geográficas, tais como múltiplos centros de dados no local ou em todas as regiões de Azure
- O seu ambiente abrange várias áreas de controlo geopolítico ou legal, ou qualquer outro caso em que tenha limites políticos que precisa impor
- Existem considerações de desempenho de comunicação ou latência devido a grandes distâncias ou utilização de ligações de rede mais lentas ou menos fiáveis
- Você precisa manter certas cargas de trabalho coadidas como um melhor esforço, seja com outras cargas de trabalho ou na proximidade dos clientes

A maioria destes requisitos alinham-se com o layout físico do cluster, representado como os domínios de falha do cluster. 

As políticas avançadas de colocação que ajudam a resolver estes cenários são:

1. Domínios inválidos
2. Domínios necessários
3. Domínios preferidos
4. Desautorizando a embalagem de réplica

A maioria dos seguintes controlos poderia ser configurado através de propriedades do nó e restrições de colocação, mas alguns são mais complicados. Para simplificar as coisas, o Gestor de Recursos de Cluster de Tecidos de Serviço fornece estas políticas adicionais de colocação. As políticas de colocação são configuradas numa base de instância de serviço por nome. Também podem ser atualizados dinamicamente.

## <a name="specifying-invalid-domains"></a>Especificação de domínios inválidos
A política de colocação **InvalidDomain** permite especificar que um determinado Domínio de Falha é inválido para um serviço específico. Esta política garante que um determinado serviço nunca funciona numa determinada área, por exemplo, por razões geopolíticas ou de política empresarial. Vários domínios inválidos podem ser especificados através de políticas separadas.

<center>

![Exemplo de domínio inválido][Image1]
</center>

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
## <a name="specifying-required-domains"></a>Especificação dos domínios necessários
A política de colocação de domínio exigida requer que o serviço esteja presente apenas no domínio especificado. Vários domínios necessários podem ser especificados através de políticas separadas.

<center>

![Exemplo de domínio necessário][Image2]
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

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Especificando um domínio preferido para as réplicas primárias de um serviço imponente
O Domínio Primário Preferido especifica o domínio da falha para colocar a Primária. A Primária acaba neste domínio quando tudo está saudável. Se o domínio ou a réplica primária falharem ou desligarem, a Primary desloca-se para outro local, idealmente no mesmo domínio. Se esta nova localização não estiver no domínio preferido, o Cluster Resource Manager transfere-o de volta para o domínio preferido o mais rapidamente possível. Naturalmente, este cenário só faz sentido para serviços estatais. Esta política é mais útil em clusters que são abrangedos por regiões de Azure ou múltiplos centros de dados, mas que têm serviços que preferem colocação em um determinado local. Manter as Primárias perto dos seus utilizadores ou outros serviços ajuda a fornecer menor latência, especialmente para leituras, que são tratadas por padrão pelas Primárias.

<center>

![Domínios Primários Preferidos e Failover][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Exigindo distribuição de réplicas e desautorizando a embalagem
As réplicas são _normalmente_ distribuídas por domínios de falha e atualização quando o cluster é saudável. No entanto, há casos em que mais de uma réplica para uma determinada partição pode acabar temporariamente embalada num único domínio. Por exemplo, digamos que o cluster tem nove nós em três domínios de falha, fd:/0, fd:/1, e fd:2. Digamos também que o seu serviço tem três réplicas. Digamos que os nós que estavam a ser usados para aquelas réplicas em fd:/1 e fd:2 caíram. Normalmente, o Cluster Resource Manager prefere outros nós nesses mesmos domínios de falha. Neste caso, digamos que devido a problemas de capacidade nenhum dos outros nós nesses domínios era válido. Se o Cluster Resource Manager construir substituições para essas réplicas, teria de escolher nós em fd:/0. No entanto, _fazê-lo_ cria uma situação em que a restrição do Domínio de Culpa é violada. As réplicas de embalagem aumentam a hipótese de todo o conjunto de réplicas poder cair ou perder-se. 

> [!NOTE]
> Para obter mais informações sobre constrangimentos e prioridades de restrição em geral, confira [este tópico.](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)
>

Se já viu uma mensagem de`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`saúde como " ", então você atingiu esta condição ou algo parecido. Normalmente, apenas uma ou duas réplicas são embaladas temporariamente. Desde que haja menos de um quórum de réplicas num dado domínio, estás a salvo. Embalar é raro, mas pode acontecer, e geralmente estas situações são transitórias uma vez que os nós voltam. Se os nós ficarem para baixo e o Cluster Resource Manager precisar de construir substituições, normalmente existem outros nós disponíveis nos domínios de falha ideais.

Algumas cargas de trabalho preferem ter sempre o número de réplicas alvo, mesmo que sejam embaladas em menos domínios. Estas cargas de trabalho estão a apostar contra falhas de domínio permanentes simultâneas e podem geralmente recuperar o estado local. Outras cargas de trabalho preferem reduzir o tempo de inatividade mais cedo do que a correção de riscos ou a perda de dados. A maioria das cargas de trabalho de produção funcionam com mais de três réplicas, mais de três domínios de falha, e muitos nós válidos por domínio de falha. Por isso, o comportamento padrão permite a embalagem de domínio por padrão. O comportamento padrão permite o equilíbrio normal e a falha para lidar com estes casos extremos, mesmo que isso signifique embalagem temporária de domínio.

Se pretender desativar essa embalagem para uma `RequireDomainDistribution` determinada carga de trabalho, pode especificar a política do serviço. Quando esta política é definida, o Cluster Resource Manager garante que não há duas réplicas da mesma divisão executadas no mesmo domínio de avaria ou atualização.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível utilizar estas configurações para serviços num cluster que não estava geograficamente agrupado? Podias, mas também não há uma boa razão. As configurações de domínio necessárias, inválidas e preferidas devem ser evitadas, a menos que os cenários as exijam. Não faz sentido tentar forçar uma carga de trabalho a funcionar numa única prateleira, ou preferir algum segmento do seu cluster local em vez de outro. Diferentes configurações de hardware devem ser distribuídas por domínios de falha e manuseadas através de restrições normais de colocação e propriedades do nó.

## <a name="next-steps"></a>Passos seguintes
- Para mais informações sobre a configuração de serviços, [Saiba sobre a configuração de Serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
