---
title: 'Gestor de recursos do Service Fabric Cluster: O custo de movimento | Documentos da Microsoft'
description: Descrição geral do custo de movimento para serviços do Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 58147f5c0c3c7203aa6978e284cf5bdc90fa6971
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737786"
---
# <a name="service-movement-cost"></a>Custo de movimento de serviço
Um fator que o Gestor de recursos de Cluster do Service Fabric tem em conta quando tentar determinar o que é alterado para fazer a um cluster é o custo dessas alterações. A noção de "Custo" prol contra quanto o cluster pode ser melhorado. Custo é fatorado quando a transferência dos serviços de balanceamento de desfragmentação e outros requisitos. O objetivo é cumprir os requisitos da forma menos interrupções ou dispendiosa. 

Mover o tempo de CPU de custos de serviços e de rede de largura de banda no mínimo. Para serviços com estado, é necessário copiar o estado desses serviços, consumindo mais memória e disco. Minimizar o custo de soluções que o Gestor de recursos de Cluster do Azure Service Fabric é exibido com ajuda a garantir que os recursos do cluster não são gasto desnecessariamente. No entanto, também não deseja ignorar as soluções que iriam melhorar significativamente a alocação de recursos do cluster.

O Gestor de recursos do Cluster tem duas formas dos custos de computação e limitá-los enquanto ele tenta gerir o cluster. O primeiro mecanismo é simplesmente contar cada movimento que ele faria. Se duas soluções são geradas com o mesmo equilibrar (classificação), em seguida, o Gestor de recursos de Cluster prefere um com o custo mais baixo (número total de movimentações).

Esta estratégia funciona bem. Mas, tal como acontece com predefinido ou cargas estáticas, é improvável em qualquer sistema complexo que move todos os é igual. Algumas são provavelmente será muito mais cara.

## <a name="setting-move-costs"></a>Custos de movimentação de definição 
Pode especificar a predefinição mover o custo de um serviço, quando é criado:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Também pode especificar ou atualizar MoveCost dinamicamente para um serviço após o serviço de criação: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Especificar dinamicamente o custo de movimentação numa base por réplica

Os trechos de código anteriores são todos os sobre como especificar MoveCost para um serviço todo ao mesmo tempo fora do próprio serviço. No entanto, mover o custo é mais útil é quando o custo de movimentação de um objeto de serviço específico altera ao longo do respetivo tempo de vida. Uma vez que os serviços têm provavelmente a melhor idéia de como dispendiosa são mover um determinado momento, há uma API para os serviços a seus próprios indivíduo mover custo durante o tempo de execução do relatório. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Impacto do custo de movimentação
MoveCost tem quatro níveis: Zero, baixa, média e alta. MoveCosts são relativas a si, exceto para Zero. Sem custos de mover significa que o movimento é gratuito e não deve contar com a pontuação da solução. Definir a sua mudança custo para o alto faz *não* garantia de que a réplica permanece num único local.

<center>
![Custo de movimentação como um fator na seleção de réplicas para movimento][Image1]
</center>

MoveCost ajuda para encontrar as soluções que fazer com que a interrupção, pelo menos, geral e são mais fácil de obter ao ainda a chegar ao saldo equivalente. Noção de um serviço de custo pode ser relativo de muitas coisas. Os fatores mais comuns no seu custo de movimentação de cálculo são:

- A quantidade de estado ou dados que o serviço tem de mover.
- O custo de desativação de clientes. Mover uma réplica primária é normalmente mais dispendioso do que o custo da movimentação de uma réplica secundária.
- O custo de interromper uma operação em andamento. Algumas operações de dados ao nível de armazenar ou operações executadas em resposta a uma chamada de cliente são dispendiosas. Depois de um certo ponto, não quer pará-los, se não precisa. Então, enquanto a operação está em curso, aumentar o custo de movimentação deste objeto de serviço para reduzir a probabilidade de que ela se move. Quando a operação estiver concluída, definir o custo ao normal.

## <a name="enabling-move-cost-in-your-cluster"></a>Ativar o custo de movimentação no seu cluster
Por ordem para a MoveCosts mais granular ser levados em consideração, MoveCost tem de estar ativada no seu cluster. Sem esta definição, o modo predefinido da contagem de movimentações é usado para calcular MoveCost e relatórios de MoveCost são ignorados.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

por meio de ClusterConfig.json das implementações autónomas ou Template para o Azure alojados clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Passos Seguintes
- Service Fabric Cluster Resource Manager utiliza as métricas para gerir o consumo e a capacidade do cluster. Para saber mais sobre métricas e como configurá-las, confira [consumo de recursos de gerenciamento e a carga no Service Fabric com a métrica](service-fabric-cluster-resource-manager-metrics.md).
- Para saber mais sobre como o Gestor de recursos de Cluster gere e faz o balanceamento de carga no cluster, confira [balanceamento de cluster do Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
