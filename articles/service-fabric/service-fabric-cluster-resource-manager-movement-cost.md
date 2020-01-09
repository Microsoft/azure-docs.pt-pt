---
title: 'Gerenciador de recursos de Cluster Service Fabric: custo de movimento'
description: Saiba mais sobre o custo de movimento para serviços de Service Fabric e como ele pode ser especificado para se adequar a qualquer necessidade de arquitetura, incluindo configuração dinâmica.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: af3e01d0d5a605c052be24eed8e14ee3449e2c79
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563348"
---
# <a name="service-movement-cost"></a>Custo de movimentação de serviço
Um fator que o Service Fabric Gerenciador de recursos de cluster considera ao tentar determinar quais alterações fazer em um cluster é o custo dessas alterações. A noção de "custo" é compensada em relação ao quanto o cluster pode ser melhorado. O custo é fatorado no ao mover serviços para balanceamento, desfragmentação e outros requisitos. O objetivo é atender aos requisitos da maneira menos prejudicial ou cara.

A movimentação de serviços custa tempo de CPU e largura de banda de rede no mínimo. Para serviços com estado, é necessário copiar o estado desses serviços, consumindo memória e disco adicionais. Minimizar o custo das soluções que o Gerenciador de recursos de Cluster Service Fabric do Azure apresenta ajuda a garantir que os recursos do cluster não sejam gastos desnecessariamente. No entanto, você também não quer ignorar soluções que melhoram significativamente a alocação de recursos no cluster.

O Gerenciador de recursos de cluster tem duas maneiras de calcular os custos e limitá-los enquanto tenta gerenciar o cluster. O primeiro mecanismo está simplesmente contando cada movimento que faria. Se duas soluções forem geradas com aproximadamente o mesmo saldo (Pontuação), o Gerenciador de recursos de cluster prefere a que com o menor custo (número total de movimentações).

Essa estratégia funciona bem. Mas, como acontece com cargas padrão ou estáticas, é improvável que todas as movimentações sejam iguais. É provável que alguns sejam muito mais caros.

## <a name="setting-move-costs"></a>Definindo custos de movimentação 
Você pode especificar o custo de movimentação padrão para um serviço quando ele é criado:

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

Você também pode especificar ou atualizar MoveCost dinamicamente para um serviço depois que o serviço tiver sido criado: 

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Especificando dinamicamente o custo de movimentação em uma base por réplica

Os trechos de código anteriores são todos para especificar MoveCost para um serviço inteiro de uma só vez de fora do próprio serviço. No entanto, o custo de movimentação é mais útil quando o custo de movimentação de um objeto de serviço específico é alterado durante seu ciclo de vida. Como os próprios serviços provavelmente têm a melhor ideia de quão dispendioso eles são mover um determinado momento, há uma API para que os serviços relatem seu próprio custo individual de movimentação durante o tempo de execução. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Impacto do custo de movimentação
MoveCost tem cinco níveis: zero, baixo, médio, alto e VeryHigh. As seguintes regras se aplicam:

* MoveCosts são relativos entre si, exceto para zero e VeryHigh. 
* O custo de movimentação zero significa que a movimentação é gratuita e não deve contar com a pontuação da solução.
* Definir o custo de movimentação como alto ou VeryHigh *não fornece uma* garantia de que a réplica *nunca* será movida.
* As réplicas com o custo de movimentação de VeryHigh serão movidas somente se houver uma violação de restrição no cluster que não possa ser corrigida de nenhuma outra maneira (mesmo que seja necessário mover muitas outras réplicas para corrigir a violação)



<center>

![o custo de movimentação como um fator na seleção de réplicas para][Image1]
de movimento </center>

O MoveCost ajuda você a encontrar as soluções que causam o mínimo de interrupções e que são mais fáceis de alcançar enquanto ainda chega ao saldo equivalente. A noção de custo de um serviço pode ser relativa a muitas coisas. Os fatores mais comuns no cálculo do custo de movimentação são:

- A quantidade de estado ou dados que o serviço tem a ser movido.
- O custo de desconexão de clientes. A movimentação de uma réplica primária geralmente é mais dispendiosa do que o custo da movimentação de uma réplica secundária.
- O custo da interrupção de uma operação em andamento. Algumas operações no nível de armazenamento de dados ou operações executadas em resposta a uma chamada de cliente são dispendiosas. Após um determinado ponto, você não desejará interrompê-los se não for necessário. Assim, enquanto a operação está em andamento, você aumenta o custo de movimentação desse objeto de serviço para reduzir a probabilidade de ele se mover. Quando a operação for concluída, você definirá o custo de volta como normal.

> [!IMPORTANT]
> Usar o custo de movimentação do VeryHigh deve ser considerado cuidadosamente, pois ele restringe significativamente a capacidade do Gerenciador de recursos de cluster de encontrar uma solução de posicionamento globalmente ideal no cluster. As réplicas com o custo de movimentação de VeryHigh serão movidas somente se houver uma violação de restrição no cluster que não possa ser corrigida de nenhuma outra maneira (mesmo que seja necessário mover muitas outras réplicas para corrigir a violação)

## <a name="enabling-move-cost-in-your-cluster"></a>Habilitando o custo de movimentação no cluster
Para que o MoveCosts mais granular seja levado em conta, MoveCost deve ser habilitado em seu cluster. Sem essa configuração, o modo padrão de contagem de movimentações é usado para calcular MoveCost e os relatórios MoveCost são ignorados.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

via ClusterConfig. JSON para implantações autônomas ou template. JSON para clusters hospedados do Azure:

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
- Service Fabric o Gerenciador de recursos de cluster usa métricas para gerenciar o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [Gerenciando o consumo de recursos e a carga em Service Fabric com métricas](service-fabric-cluster-resource-manager-metrics.md).
- Para saber mais sobre como o Gerenciador de recursos de cluster gerencia e equilibra a carga no cluster, confira [balanceamento do cluster de Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
