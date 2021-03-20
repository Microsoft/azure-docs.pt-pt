---
title: 'Gestor de Recursos do Cluster de Tecido de Serviço: Custo de movimento'
description: Conheça o custo de movimento dos serviços de Service Fabric e como pode ser especificado para se adaptar a qualquer necessidade arquitetónica, incluindo configuração dinâmica.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fdcfb02851d56ed996ae4bf32671ab545782733
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89005348"
---
# <a name="service-movement-cost"></a>Custo de movimento do serviço
Um fator que o Service Fabric Cluster Resource Manager considera ao tentar determinar que alterações fazer a um cluster é o custo dessas alterações. A noção de "custo" é trocada contra quanto o cluster pode ser melhorado. O custo é contabilizado na mudança de serviços para equilibrar, desfragmentação e outros requisitos. O objetivo é cumprir os requisitos da forma menos disruptiva ou dispendiosa.

Os serviços de mudança custam, no mínimo, tempo de CPU e largura de banda de rede. Para serviços estatais, requer a cópia do estado desses serviços, consumindo memória adicional e disco. Minimizar o custo das soluções que o Azure Service Fabric Cluster Resource Manager cria ajuda a garantir que os recursos do cluster não são gastos desnecessariamente. No entanto, também não quer ignorar soluções que melhorem significativamente a alocação de recursos no cluster.

O Cluster Resource Manager tem duas formas de calcular os custos e limitá-los enquanto tenta gerir o cluster. O primeiro mecanismo é simplesmente contar cada movimento que faria. Se duas soluções forem geradas com cerca do mesmo saldo (pontuação), então o Cluster Resource Manager prefere a que tem o custo mais baixo (número total de movimentos).

Esta estratégia funciona bem. Mas como com cargas padrão ou estáticas, é improvável em qualquer sistema complexo que todos os movimentos são iguais. Alguns são provavelmente muito mais caros.

## <a name="setting-move-costs"></a>Definir custos de movimento 
Pode especificar o custo de movimento padrão de um serviço quando este é criado:

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

Também pode especificar ou atualizar o MoveCost dinamicamente para um serviço depois de o serviço ter sido criado: 

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Especificando dinamicamente o custo do movimento numa base por réplica

Os cortes anteriores são todos para especificar o MoveCost para um serviço completo de uma só vez de fora do próprio serviço. No entanto, o custo de deslocação é mais útil é quando o custo de movimento de um objeto de serviço específico muda ao longo do seu tempo de vida. Uma vez que os próprios serviços provavelmente têm a melhor ideia de como são dispendiosos para mover um determinado tempo, há uma API para serviços para reportar o seu próprio custo de movimento individual durante o tempo de funcionação. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Impacto do custo da mudança
MoveCost tem cinco níveis: Zero, Baixo, Médio, Alto e Muito Alto. Aplicam-se as seguintes regras:

* Os MoveCosts são relativos uns aos outros, exceto zero e VeryHigh. 
* O custo zero do movimento significa que a circulação é livre e não deve contar com a pontuação da solução.
* Definir o seu custo de mudança para High ou VeryHigh *não* fornece uma garantia de que a réplica *nunca* será movida.
* As réplicas com o custo de movimento VeryHigh só serão movidas se houver uma violação de restrição no cluster que não possa ser corrigida de outra forma (mesmo que exija mover muitas outras réplicas para corrigir a violação)



<center>

![Mover o custo como fator na seleção de réplicas para movimento][Image1]
</center>

O MoveCost ajuda-o a encontrar as soluções que causam menos perturbações em geral e são mais fáceis de conseguir enquanto ainda chegam ao equilíbrio equivalente. A noção de custo de um serviço pode ser relativa a muitas coisas. Os fatores mais comuns no cálculo do seu custo de movimento são:

- A quantidade de estado ou dados que o serviço tem de mover.
- O custo da desconexão dos clientes. Mover uma réplica primária é geralmente mais dispendioso do que o custo de mover uma réplica secundária.
- O custo de interromper uma operação a bordo. Algumas operações ao nível da loja de dados ou operações realizadas em resposta a uma chamada de cliente são dispendiosas. Depois de um certo ponto, não vai querer detê-los se não for preciso. Por isso, enquanto a operação está a decorrer, aumenta-se o custo de movimento deste objeto de serviço para reduzir a probabilidade de se mover. Quando a operação estiver terminada, voltas a normalizar o custo.

> [!IMPORTANT]
> A utilização do custo de movimento VeryHigh deve ser cuidadosamente considerada, uma vez que restringe significativamente a capacidade do Cluster Resource Manager de encontrar uma solução de colocação globalmente ideal no cluster. As réplicas com o custo de movimento VeryHigh só serão movidas se houver uma violação de restrição no cluster que não possa ser corrigida de outra forma (mesmo que exija mover muitas outras réplicas para corrigir a violação)

## <a name="enabling-move-cost-in-your-cluster"></a>Habilitando o custo de movimento no seu cluster
Para que os MoveCosts mais granulares sejam tidos em conta, o MoveCost deve ser ativado no seu cluster. Sem esta definição, o modo padrão de contagem de movimentos é usado para calcular o MoveCost, e os relatórios moveCost são ignorados.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

através ClusterConfig.jspara implantações autónomas ou Template.jspara aglomerados azure hospedados:

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

## <a name="next-steps"></a>Passos seguintes
- Service Fabric Cluster Resource Manger utiliza métricas para gerir o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-las, consulte [gerir o consumo de recursos e carregar em Tecido de Serviço com métricas.](service-fabric-cluster-resource-manager-metrics.md)
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira [Balanceing your Service Fabric cluster](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
