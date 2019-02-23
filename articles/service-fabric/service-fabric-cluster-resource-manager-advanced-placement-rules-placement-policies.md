---
title: Gestor de recursos do Service Fabric Cluster - políticas de posicionamento | Documentos da Microsoft
description: Descrição geral das políticas de posicionamento adicionais e regras para serviços do Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 2858628874dc9955db5084ef5732d85acd6e7fc1
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729798"
---
# <a name="placement-policies-for-service-fabric-services"></a>Políticas de colocação para serviços do service fabric
As políticas de colocação são regras adicionais que podem ser utilizadas para regular o posicionamento do serviço em alguns cenários específicos e menos comuns. Alguns exemplos desses cenários são:

- Cluster do Service Fabric se estende por distâncias geográficas, por exemplo, vários datacenters no local ou em regiões do Azure
- Seu ambiente se estende por várias áreas do controle geopolítica ou legal, ou algum outro caso em que tiver a política de limites, é necessário impor
- Existem considerações de desempenho ou latência de comunicação devido a grandes distâncias ou a utilização de ligações de rede mais lento ou menos fiável
- Precisa ter determinadas cargas de trabalho colocalizada dentro dos melhores esforços, com outras cargas de trabalho ou em proximidade aos clientes

A maioria desses requisitos se alinham com o esquema físico do cluster, representado como os domínios de falha do cluster. 

As políticas de colocação avançada que ajudar a resolver esses cenários são:

1. Domínios inválidos
2. Domínios necessários
3. Domínios preferenciais
4. Desautorizar a remessa de réplica

A maioria dos controles seguintes poderia ser configurada por meio de propriedades de nó e restrições de posicionamento, mas algumas são mais complicadas. Para tornar as coisas mais simples, o Gestor de recursos de Cluster do Service Fabric fornece estas políticas de colocação adicionais. As políticas de colocação são configuradas de forma de instância de serviço por chamada. Eles também podem ser atualizados dinamicamente.

## <a name="specifying-invalid-domains"></a>Especificação de domínios inválidos
O **InvalidDomain** política de colocação permite-lhe especificar que um determinado domínio de falhas é inválido para um serviço específico. Esta política garante que um determinado serviço nunca é executado numa determinada área, por exemplo por motivos de política geopolíticas ou da empresa. Podem ser especificados vários domínios inválidos através de políticas separadas.

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
## <a name="specifying-required-domains"></a>Especificar domínios necessários
A política de colocação de domínio necessários requer que o serviço está presente apenas no domínio especificado. Podem ser especificados vários domínios necessários através de políticas separadas.

<center>
![Exemplo de domínio necessários][Image2]
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

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Especificar um domínio preferencial para as réplicas principal de serviço com estado
O domínio principal preferencial Especifica o domínio de falha para colocar o primário no. O principal acaba neste domínio quando tudo o que está em bom estado. Se o domínio ou a réplica primária falha ou desligado, o principal move-se em outro local, o ideal é que, no mesmo domínio. Se esta nova localização não estiver no domínio preferencial, o Gestor de recursos de Cluster move-o novamente para o domínio preferencial logo que possível. Naturalmente esta definição só faz sentido para serviços com estado. Esta política é mais útil em clusters que estão estendidos em regiões do Azure ou vários datacenters, mas tem os serviços que preferem a colocação num certo local. Manter as cores primárias perto dos seus utilizadores ou outros serviços ajuda a fornecer uma latência mais baixa, especialmente para leituras, que são processadas pela cores primárias, por predefinição.

<center>
![Domínios de primários preferenciais e ativação pós-falha][Image3]
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

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Exigir a distribuição de réplica e desautorizar a remessa
Réplicas estão _normalmente_ distribuídos por domínios de falha e atualização, quando o cluster está em bom estado. No entanto, há casos em que mais do que uma réplica de uma determinada partição pode acabar temporariamente compactada num único domínio. Por exemplo, digamos que o cluster tem nove nós em três domínios de falha, fd: 0, fd: / 1 e fd: / 2. Vamos também supor que o seu serviço tem três réplicas. Vamos supor que os nós que estavam sendo usados para essas réplicas no fd: / 1 e fd: / 2 foi desativado. Normalmente, o Gestor de recursos de Cluster preferia outros nós esses mesmos domínios de falha. Neste caso, vamos supor que devido a problemas de capacidade nenhum dos outros nós nesses domínios eram válidas. Se o Gestor de recursos do Cluster cria substituições para essas réplicas, seria necessário escolher nós no fd: 0. No entanto, fazendo _que_ cria uma situação em que a restrição de domínio de falha é violada. Empacotar os aumentos de réplicas a possibilidade de que a réplica toda definir poderia descer ou perder-se. 

> [!NOTE]
> Para obter mais informações sobre as restrições e restrição de prioridades em geral, consulte [neste tópico](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Se já viu uma mensagem de estado de funcionamento, tal como "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", em seguida, se deparou-se esta condição ou algo parecido. Normalmente, apenas uma ou duas réplicas são incluídas em conjunto temporariamente. Desde que haja menos de um quórum de réplicas num determinado domínio, está protegido. Remessa é rara, mas isso pode acontecer e, normalmente, essas situações são transitórias, uma vez que os nós voltam. Se os nós mantenha-se para baixo e o Gestor de recursos do Cluster tem de criar substituições, normalmente, existem outros nós disponíveis nos domínios de falhas ideal.

Algumas cargas de trabalho prefere ter sempre o número de destino de réplicas, mesmo que eles são incluídos em menos de domínios. Estas cargas de trabalho são a apostar contra falhas de domínio de permanente simultâneas total e, normalmente, podem recuperar o estado local. Outras cargas de trabalho em vez disso, seriam necessário o tempo de inatividade anterior a correção de risco ou perda de dados. Executam a maioria das cargas de trabalho de produção com mais de três réplicas, mais de três domínios de falha e muitos nós válido por domínio de falha. Por este motivo, o comportamento padrão permite remessa de domínio por predefinição. O comportamento padrão permite normal balanceamento e ativação pós-falha para processar estes casos extremos, mesmo que isso significa que a remessa de domínio temporário.

Se pretender desativar tal remessa para uma determinada carga de trabalho, pode especificar o `RequireDomainDistribution` política no serviço. Quando esta política estiver definida, o Gestor de recursos do Cluster garante que nenhuma duas réplicas da mesma partição executados no mesmo domínio de falha ou atualização.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível usar essas configurações para os serviços num cluster que não foi geograficamente abrangido? Poderia, mas não não há um grande motivo também. As configurações de domínio necessária, inválidos e preferencial devem ser evitadas, a menos que os cenários exigem-los. Não faz qualquer sentido para tentar forçar uma determinada carga de trabalho para executar num bastidor único, ou para dar preferência a algum segmento do seu cluster local em vez de outro. Diferentes configurações de hardware devem ser distribuídas por domínios de falha e manipuladas por meio de restrições de posicionamento normal e propriedades de nó.

## <a name="next-steps"></a>Passos Seguintes
- Para obter mais informações sobre como configurar os serviços, [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
