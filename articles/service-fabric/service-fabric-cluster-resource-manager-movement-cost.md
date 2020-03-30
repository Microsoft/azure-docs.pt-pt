---
title: 'Gestor de recursos de cluster de tecido de serviço: Custo de movimento'
description: Saiba mais sobre o custo de movimento dos serviços de Tecido de Serviço, e como pode ser especificado para se adaptar a qualquer necessidade arquitetónica, incluindo configuração dinâmica.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: af3e01d0d5a605c052be24eed8e14ee3449e2c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563348"
---
# <a name="service-movement-cost"></a>Custo de movimento de serviço
Um fator que o Gestor de Recursos de Cluster de Tecidos de Serviço considera ao tentar determinar que alterações a fazer a um cluster é o custo dessas alterações. A noção de "custo" é trocada contra o quanto o cluster pode ser melhorado. O custo é contabilizado na mudança de serviços para o equilíbrio, desfragmentação e outros requisitos. O objetivo é satisfazer os requisitos da forma menos disruptiva ou cara.

Os serviços de deslocação custam o tempo de CPU e a largura de banda da rede no mínimo. Para serviços estatais, requer copiar o estado desses serviços, consumindo memória e disco adicionais. Minimizar o custo das soluções que o Gestor de Recursos de Cluster de Tecidos de Serviço Azure surge ajuda a garantir que os recursos do cluster não são gastos desnecessariamente. No entanto, também não quer ignorar soluções que melhorem significativamente a alocação de recursos no cluster.

O Cluster Resource Manager tem duas formas de calcular os custos e limitá-los enquanto tenta gerir o cluster. O primeiro mecanismo é simplesmente contar todos os movimentos que faria. Se duas soluções forem geradas com o mesmo saldo (pontuação), então o Cluster Resource Manager prefere a que tem o menor custo (número total de movimentos).

Esta estratégia funciona bem. Mas como com cargas estáticas ou padrão, é improvável em qualquer sistema complexo que todos os movimentos sejam iguais. Alguns são provavelmente muito mais caros.

## <a name="setting-move-costs"></a>Custos de mudança de configuração 
Pode especificar o custo de mudança predefinido de um serviço quando este for criado:

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

Também pode especificar ou atualizar o MoveCost dinamicamente para um serviço após a criação do serviço: 

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Especificando dinamicamente o custo de movimento numa base por réplica

Os cortes anteriores destinam-se a especificar o MoveCost para um serviço inteiro de fora do próprio serviço. No entanto, o custo de movimento é mais útil é quando o custo de mudança de um objeto de serviço específico muda ao longo do seu tempo de vida. Uma vez que os próprios serviços provavelmente têm a melhor ideia de como são dispendiosos para mover um dado tempo, há uma API para os serviços reportarem o seu próprio custo de movimentação individual durante o tempo de funcionação. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Impacto do custo de movimento
MoveCost tem cinco níveis: Zero, Baixo, Médio, Alto e Muito Alto. Aplicam-se as seguintes regras:

* MoveCosts são relativos uns aos outros, exceto zero e muito alto. 
* Custo de movimento zero significa que o movimento é livre e não deve contar com a pontuação da solução.
* Definir o seu custo de mudança para High ou VeryHigh *não* garante que a réplica *nunca* será movida.
* As réplicas com o custo de movimento VeryHigh só serão movidas se houver uma violação de restrição no cluster que não pode ser corrigida de outra forma (mesmo que exija mover muitas outras réplicas para corrigir a violação)



<center>

![Mova o custo como um fator na seleção de réplicas para movimento][Image1]
</center>

O MoveCost ajuda-o a encontrar as soluções que causam menos perturbações em geral e são mais fáceis de conseguir ao mesmo tempo que chegam a um equilíbrio equivalente. A noção de custo de um serviço pode ser relativa a muitas coisas. Os fatores mais comuns no cálculo do seu custo de mudança são:

- A quantidade de estado ou dados que o serviço tem de mover.
- O custo da desconexão dos clientes. Mover uma réplica primária é geralmente mais dispendioso do que o custo de mover uma réplica secundária.
- O custo de interromper uma operação a bordo. Algumas operações ao nível da loja de dados ou operações realizadas em resposta a uma chamada de cliente são dispendiosas. Depois de um certo ponto, não os queres deter se não for preciso. Assim, enquanto a operação está em curso, você aumenta o custo de movimento deste objeto de serviço para reduzir a probabilidade de que se move. Quando a operação estiver pronta, volta a colocar o custo no normal.

> [!IMPORTANT]
> A utilização do custo de movimento VeryHigh deve ser cuidadosamente considerada, uma vez que restringe significativamente a capacidade do Cluster Resource Manager de encontrar uma solução de colocação globalmente ideal no cluster. As réplicas com o custo de movimento VeryHigh só serão movidas se houver uma violação de restrição no cluster que não pode ser corrigida de outra forma (mesmo que exija mover muitas outras réplicas para corrigir a violação)

## <a name="enabling-move-cost-in-your-cluster"></a>Habilitar o custo de movimento no seu cluster
Para que os MoveCosts mais granulares sejam tidos em conta, o MoveCost deve ser ativado no seu cluster. Sem esta definição, o modo padrão de contagem de movimentos é usado para calcular moveCost, e os relatórios MoveCost são ignorados.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

via ClusterConfig.json para implantações autónomas ou template.json para clusters alojados em Azure:

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
- Service Fabric Cluster Resource Manger usa métricas para gerir o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-las, confira [a Gestão do consumo de recursos e a carga em Tecido de Serviço com métricas](service-fabric-cluster-resource-manager-metrics.md).
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira [Balanceing your Service Fabric cluster](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
