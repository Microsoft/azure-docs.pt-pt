---
title: Gestor de Recursos do Cluster de Tecido de Serviço - Políticas de Colocação
description: Visão geral das políticas e regras adicionais de colocação para serviços de tecido de serviço
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 263e45928642aa74d682fc490e424a24deeb8076
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790686"
---
# <a name="placement-policies-for-service-fabric-services"></a>Políticas de colocação para serviços de tecidos de serviço
As políticas de colocação são regras adicionais que podem ser usadas para governar a colocação de serviços em alguns cenários específicos e menos comuns. Alguns exemplos desses cenários são:

- O seu cluster de tecido de serviço abrange distâncias geográficas, tais como múltiplos centros de dados no local ou em regiões de Azure
- O seu ambiente abrange várias áreas de controlo geopolítico ou legal, ou qualquer outro caso em que tenha limites políticos que precisa para impor
- Existem considerações de desempenho de comunicação ou latência devido a grandes distâncias ou utilização de ligações de rede mais lentas ou menos fiáveis
- Você precisa manter certas cargas de trabalho collocadas como um melhor esforço, seja com outras cargas de trabalho ou na proximidade dos clientes
- Você precisa de vários casos apátridas de uma partição em um único nó

A maioria destes requisitos alinha-se com a disposição física do cluster, representada como os domínios de falha do cluster. 

As políticas avançadas de colocação que ajudam a resolver estes cenários são:

1. Domínios inválidos
2. Domínios necessários
3. Domínios preferenciais
4. Embalagem de réplica desativação
5. Permitir múltiplas instâncias apátridas no nó

A maioria dos seguintes controlos poderia ser configurado através de propriedades de nó e restrições de colocação, mas alguns são mais complicados. Para simplificar as coisas, o Service Fabric Cluster Resource Manager fornece estas políticas adicionais de colocação. As políticas de colocação são configuradas numa base de instância de serviço por nome. Também podem ser atualizados dinamicamente.

## <a name="specifying-invalid-domains"></a>Especificar domínios inválidos
A política de colocação **de InvalidDomain** permite especificar que um determinado Domínio de Avaria é inválido para um serviço específico. Esta política garante que um determinado serviço nunca funciona numa determinada área, por exemplo, por razões geopolíticas ou de política corporativa. Vários domínios inválidos podem ser especificados através de políticas separadas.

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
## <a name="specifying-required-domains"></a>Especificar os domínios necessários
A política de colocação de domínio requer que o serviço esteja presente apenas no domínio especificado. Vários domínios necessários podem ser especificados através de políticas separadas.

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
O Domínio Primário Preferido especifica o domínio da falha para colocar o Primário. A Primária acaba neste domínio quando tudo é saudável. Se o domínio ou a réplica primária falharem ou desligarem, a Primária move-se para outro local, idealmente no mesmo domínio. Se esta nova localização não estiver no domínio preferido, o Cluster Resource Manager transfere-o para o domínio preferido o mais rapidamente possível. Naturalmente, este cenário só faz sentido para serviços estatais. Esta política é mais útil em clusters que são espalhados por regiões de Azure ou vários centros de dados, mas têm serviços que preferem a colocação em um determinado local. Manter as Primárias perto dos seus utilizadores ou outros serviços ajuda a fornecer uma menor latência, especialmente para leituras, que são tratadas por padrão pelas Primárias.

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

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Exigir distribuição de réplicas e desconsedição da embalagem
As réplicas são _normalmente_ distribuídas por domínios de falha e atualização quando o cluster é saudável. No entanto, há casos em que mais de uma réplica para uma determinada partição pode acabar temporariamente embalada num único domínio. Por exemplo, digamos que o cluster tem nove nós em três domínios de avaria, fd:/0, fd:/1 e fd:/2. Digamos também que o seu serviço tem três réplicas. Digamos que os nós que estavam sendo usados para as réplicas em fd:/1 e fd:/2 caíram. Normalmente, o Cluster Resource Manager preferiria outros nós nesses mesmos domínios de avaria. Neste caso, digamos que devido a problemas de capacidade nenhum dos outros nós nesses domínios era válido. Se o Cluster Resource Manager constrói substituições para essas réplicas, terá de escolher nós em fd:/0. No entanto, isso cria uma situação em _que_ a restrição do Domínio da Falha é violada. As réplicas de embalagem aumentam a hipótese de todo o conjunto de réplicas poder descer ou perder-se. 

> [!NOTE]
> Para obter mais informações sobre constrangimentos e prioridades de restrição em geral, confira [este tópico.](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)
>

Se já viu uma mensagem de saúde como " `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain` " então você atingiu esta condição ou algo parecido. Normalmente, apenas uma ou duas réplicas são embaladas temporariamente. Desde que haja menos do que um quórum de réplicas num dado domínio, estás a salvo. A embalagem é rara, mas pode acontecer, e normalmente estas situações são transitórias desde que os nós voltam. Se os nós ficarem para baixo e o Cluster Resource Manager precisar de construir substituições, normalmente existem outros nós disponíveis nos domínios de avaria ideal.

Algumas cargas de trabalho preferem ter sempre o número-alvo de réplicas, mesmo que sejam embaladas em menos domínios. Estas cargas de trabalho estão a apostar contra falhas de domínio permanentes simultâneas e podem geralmente recuperar o estado local. Outras cargas de trabalho preferem tirar o tempo de inatividade mais cedo do que arriscar a correção ou a perda de dados. A maioria das cargas de trabalho de produção funcionam com mais de três réplicas, mais de três domínios de falha, e muitos nós válidos por domínio de avaria. Por isso, o comportamento predefinido permite a embalagem de domínio por padrão. O comportamento padrão permite o equilíbrio normal e o failover para lidar com estes casos extremos, mesmo que isso signifique embalagem temporária de domínio.

Se quiser desativar essa embalagem para uma determinada carga de trabalho, pode especificar a `RequireDomainDistribution` política do serviço. Quando esta política é definida, o Cluster Resource Manager garante que não há duas réplicas da mesma partição executadas no mesmo domínio de falha ou atualização.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível utilizar estas configurações para serviços num cluster que não foi geograficamente abrangedo? Podias, mas não há uma boa razão também. As configurações de domínio necessárias, inválidas e preferenciais devem ser evitadas, a menos que os cenários as exijam. Não faz sentido tentar forçar uma determinada carga de trabalho a funcionar numa única prateleira, ou preferir algum segmento do seu cluster local em vez de outro. Diferentes configurações de hardware devem ser espalhadas por domínios de avaria e manuseadas através de restrições normais de colocação e propriedades de nó.

## <a name="placement-of-multiple-stateless-instances-of-a-partition-on-single-node"></a>Colocação de múltiplos casos apátridas de uma partição em nó único
A política de colocação **AllowMultipleStatelessInstancesOnNode** permite a colocação de múltiplos casos apátridas de uma partição num único nó. Por predefinição, várias instâncias de uma única partição não podem ser colocadas num nó. Mesmo com um serviço -1, não é possível escalar o número de casos para além do número de nós no cluster, para um serviço nomeado. Esta política de colocação remove esta restrição e permite que o InstanceCount seja especificado acima da contagem de nós.

Se já viu uma mensagem de saúde como " `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: ReplicaExclusion` " então você atingiu esta condição ou algo parecido. 

Ao especificar a `AllowMultipleStatelessInstancesOnNode` política do serviço, o InstanceCount pode ser definido para além do número de nós no cluster.

Código:

```csharp
ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription allowMultipleInstances = new ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription();
serviceDescription.PlacementPolicies.Add(allowMultipleInstances);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless –PartitionSchemeSingleton –PlacementPolicy @(“AllowMultipleStatelessInstancesOnNode”) -InstanceCount 10 -ServicePackageActivationMode ExclusiveProcess 
```

> [!NOTE]
> A política de colocação está atualmente em pré-visualização e por trás da definição do `EnableUnsupportedPreviewFeatures` cluster. Uma vez que esta é uma funcionalidade de pré-visualização por enquanto, definir o config de pré-visualização impede que o cluster seja atualizado de/para. Por outras palavras, terá de criar um novo cluster para experimentar a funcionalidade.
>

> [!NOTE]
> Atualmente, a política só é suportada para serviços apátridas com [modo de ativação de pacote de serviços](/dotnet/api/system.fabric.description.servicepackageactivationmode)ExclusiveProcess .
>

> [!WARNING]
> A política não é suportada quando utilizada com pontos finais estáticos. A utilização de ambos em conjunto pode levar a um aglomerado pouco saudável, uma vez que múltiplas instâncias no mesmo nó tentam ligar-se à mesma porta, e não podem surgir. 
>

> [!NOTE]
> A utilização de um valor elevado de [MinInstanceCount](/dotnet/api/system.fabric.description.statelessservicedescription.mininstancecount) com esta política de colocação pode levar a atualizações de aplicações presas. Por exemplo, se tiver um cluster de cinco nós e definir InstanceCount=10, terá duas instâncias em cada nó. Se definir MinInstanceCount=9, uma tentativa de atualização de aplicações pode ficar presa; com MinInstanceCount=8, isto pode ser evitado.
>

## <a name="next-steps"></a>Próximos passos
- Para obter mais informações sobre serviços de configuração, [Saiba mais sobre a configuração dos serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
