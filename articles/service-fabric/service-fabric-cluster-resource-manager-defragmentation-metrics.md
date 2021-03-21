---
title: Desfragmentação de métricas em tecido de serviço Azure
description: Aprenda a usar desfragmentação, ou embalagem, como estratégia para métricas em Tecido de Serviço. Esta técnica é útil para serviços muito grandes.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6dee9a845df305b06153d99c2bafe6d74524a139
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449208"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentação de métricas e carga em Tecido de Serviço
A estratégia padrão do Service Fabric Cluster Resource Manager para gerir métricas de carga no cluster é distribuir a carga. Garantir que os nós são utilizados uniformemente evita pontos quentes e frios que levam tanto à contenção como aos recursos desperdiçados. Distribuir cargas de trabalho no cluster é também o mais seguro em termos de falhas de sobrevivência, uma vez que garante que uma falha não tira uma grande percentagem de uma determinada carga de trabalho. 

O Service Fabric Cluster Resource Manager suporta uma estratégia diferente de gestão da carga, que é desfragmentação. Defragmentação significa que em vez de tentar distribuir a utilização de uma métrica através do cluster, é consolidado. A consolidação é apenas uma inversão da estratégia de equilíbrio padrão – em vez de minimizar o desvio padrão médio da carga métrica, o Cluster Resource Manager tenta aumentá-la.

## <a name="when-to-use-defragmentation"></a>Quando usar desfragmentação
A distribuição da carga no aglomerado consome alguns dos recursos em cada nó. Algumas cargas de trabalho criam serviços excepcionalmente grandes e consomem a maior parte ou a maioria de um nó. Nestes casos, é possível que quando há grandes cargas de trabalho a serem criadas não há espaço suficiente em qualquer nó para executá-los. Grandes cargas de trabalho não são um problema no Tecido de Serviço; nestes casos, o Cluster Resource Manager determina que precisa de reorganizar o cluster para dar espaço a esta grande carga de trabalho. Entretanto, entretanto, essa carga de trabalho tem de esperar para ser programada no cluster.

Se há muitos serviços e estado para se deslocar, então pode levar muito tempo para que a grande carga de trabalho seja colocada no cluster. Isto é mais provável se outras cargas de trabalho no cluster também forem grandes e por isso demorar mais tempo a reorganizar-se. A equipa do Service Fabric mediu os tempos de criação em simulações deste cenário. Descobrimos que a criação de grandes serviços demorou muito mais tempo assim que a utilização do cluster ultrapassou os 30% e os 50%. Para lidar com este cenário, introduzimos a desfragmentação como estratégia de equilíbrio. Descobrimos que para grandes cargas de trabalho, especialmente aquelas em que o tempo de criação era importante, a desfragmentação realmente ajudou essas novas cargas de trabalho a ser programadas no cluster.

Pode configurar métricas de desfragmentação para que o Cluster Resource Manager tente condensar proativamente a carga dos serviços em menos nós. Isto ajuda a garantir que há quase sempre espaço para grandes serviços sem reorganizar o cluster. Não ter que reorganizar o cluster permite criar grandes cargas de trabalho rapidamente.

A maioria das pessoas não precisa de desfragmentação. Os serviços costumam ser pequenos, por isso não é difícil encontrar espaço para eles no aglomerado. Quando a reorganização é possível, vai rapidamente, mais uma vez porque a maioria dos serviços são pequenos e podem ser movidos rapidamente e em paralelo. No entanto, se tem grandes serviços e precisa deles criados rapidamente, então a estratégia de desfragmentação é para si. Discutiremos as trocas de usando a desfragmentação a seguir. 

## <a name="defragmentation-tradeoffs"></a>Compensações de desfragmentação
A desfragmentação pode aumentar o impacto das falhas, uma vez que mais serviços estão a funcionar em nós que falham. A desfragmentação também pode aumentar os custos, uma vez que os recursos no cluster devem ser mantidos em reserva, aguardando a criação de grandes cargas de trabalho.

O diagrama seguinte dá uma representação visual de dois aglomerados, um que é desfragmentado e outro que não é. 

<center>

![Comparar clusters equilibrados e defragmentedos][Image1]
</center>

No caso equilibrado, considere o número de movimentos que seriam necessários para colocar um dos maiores objetos de serviço. No aglomerado desfragmentado, a grande carga de trabalho poderia ser colocada em nós quatro ou cinco sem ter que esperar que quaisquer outros serviços se movimentassem.

## <a name="defragmentation-pros-and-cons"></a>Defragmentações prós e contras
Então, quais são as outras trocas conceptuais? Aqui está uma mesa rápida de coisas em que pensar:

| Defragmentation Pros | Cons de defragmentação |
| --- | --- |
| Permite uma criação mais rápida de grandes serviços |Concentra a carga em menos nóns, aumentando a contenção |
| Permite uma menor circulação de dados durante a criação |Falhas podem afetar mais serviços e causar mais agitação |
| Permite uma descrição rica dos requisitos e recuperação do espaço |Configuração geral de gestão de recursos mais complexa |

Pode misturar métricas desfragmentadas e normais no mesmo conjunto. O Cluster Resource Manager tenta consolidar ao máximo as métricas de desfragmentação, espalhando as outras. Os resultados da mistura de estratégias de desfragmentação e de equilíbrio dependem de vários fatores, incluindo:
  - o número de métricas de equilíbrio vs. número de métricas de desfragmentação
  - Se algum serviço utiliza ambos os tipos de métricas 
  - os pesos métricos
  - cargas métricas correntes
  
A experimentação é necessária para determinar a configuração exata necessária. Recomendamos uma medição completa das suas cargas de trabalho antes de permitir métricas de desfragmentação na produção. Isto é especialmente verdade quando se mistura desfragmentação e métricas equilibradas dentro do mesmo serviço. 

## <a name="configuring-defragmentation-metrics"></a>Configurar métricas de desfragmentação
Configurar métricas de desfragmentação é uma decisão global no cluster, e as métricas individuais podem ser selecionadas para desfragmentação. Os seguintes fragmentos de config mostram como configurar métricas para desfragmentação. Neste caso, a "Metric1" é configurada como uma métrica de desfragmentação, enquanto "Metric2" continuará a ser equilibrada normalmente. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

através ClusterConfig.jspara implantações autónomas ou Template.jspara aglomerados azure hospedados:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Passos seguintes
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](service-fabric-cluster-resource-manager-cluster-description.md)
- As métricas são como a Manjedoura de Recursos do Cluster de Tecidos de Serviço gere o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
