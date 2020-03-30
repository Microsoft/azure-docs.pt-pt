---
title: Desfragmentação de Métricas em Tecido de Serviço Azure
description: Aprenda a usar a desfragmentação, ou embalagem, como estratégia para métricas em Tecido de Serviço. Esta técnica é útil para serviços muito grandes.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bba459be4408f4a4bc438bb33b0570a91e84f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563365"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentação de métricas e carga em Tecido de Serviço
A estratégia padrão do Gestor de Recursos de Cluster de Tecidode Serviço para gerir métricas de carga no cluster é distribuir a carga. Garantir que os nós são uniformemente utilizados evita pontos quentes e frios que conduzem tanto à contenção como aos recursos desperdiçados. A distribuição de cargas de trabalho no cluster é também a mais segura em termos de falhas sobreviventes, uma vez que garante que uma falha não desconta uma grande percentagem de uma determinada carga de trabalho. 

O Gestor de Recursos de Cluster de Tecidos de Serviço suporta uma estratégia diferente para gerir a carga, que é a desfragmentação. A desfragmentação significa que, em vez de tentar distribuir a utilização de uma métrica através do cluster, está consolidada. A consolidação é apenas uma inversão da estratégia de equilíbrio padrão – em vez de minimizar o desvio padrão médio da carga métrica, o Cluster Resource Manager tenta aumentá-la.

## <a name="when-to-use-defragmentation"></a>Quando usar a desfragmentação
Distribuir carga no cluster consome alguns dos recursos em cada nó. Algumas cargas de trabalho criam serviços excepcionalmente grandes e consomem a maioria ou a toda a parte de um nó. Nestes casos, é possível que quando há grandes cargas de trabalho sendo criadas que não há espaço suficiente em qualquer nó para executá-los. Grandes cargas de trabalho não são um problema no Tecido de Serviço; nestes casos, o Cluster Resource Manager determina que precisa de reorganizar o cluster para dar espaço a esta grande carga de trabalho. No entanto, entretanto, essa carga de trabalho tem de esperar para ser programada no cluster.

Se houver muitos serviços e estado para se deslocar, então pode levar muito tempo para que a grande carga de trabalho seja colocada no cluster. Isto é mais provável se outras cargas de trabalho no cluster também forem grandes e, por isso, demorar mais tempo a reorganizar-se. A equipa de Tecido de Serviço mediu os tempos de criação em simulações deste cenário. Descobrimos que a criação de grandes serviços demorou muito mais tempo assim que a utilização do cluster foi superior entre 30% e 50%. Para lidar com este cenário, introduzimos a desfragmentação como estratégia de equilíbrio. Descobrimos que para grandes cargas de trabalho, especialmente aquelas em que o tempo de criação era importante, a desfragmentação realmente ajudou essas novas cargas de trabalho a ficarem programadas no cluster.

Pode configurar métricas de desfragmentação para que o Cluster Resource Manager tente condensar proativamente a carga dos serviços em menos nós. Isto ajuda a garantir que há quase sempre espaço para grandes serviços sem reorganizar o cluster. Não ter que reorganizar o cluster permite criar grandes cargas de trabalho rapidamente.

A maioria das pessoas não precisa de desfragmentação. Os serviços são geralmente pequenos, por isso não é difícil encontrar espaço para eles no aglomerado. Quando a reorganização é possível, vai rapidamente, novamente porque a maioria dos serviços são pequenos e podem ser movidos rapidamente e em paralelo. No entanto, se você tem grandes serviços e precisa deles criados rapidamente, então a estratégia de desfragmentação é para si. Discutiremos as trocas de usar a desfragmentação a seguir. 

## <a name="defragmentation-tradeoffs"></a>Compensações de desfragmentação
A desfragmentação pode aumentar o impacto das falhas, uma vez que mais serviços estão a funcionar em nós que falham. A desfragmentação também pode aumentar os custos, uma vez que os recursos do cluster devem ser mantidos em reserva, aguardando a criação de grandes cargas de trabalho.

O diagrama seguinte dá uma representação visual de dois clusters, um que é desfragmentado e outro que não é. 

<center>

![Comparar clusters equilibrados e desfragmentados][Image1]
</center>

No caso equilibrado, considere o número de movimentos que seriam necessários para colocar um dos maiores objetos de serviço. No aglomerado desfragmentado, a grande carga de trabalho poderia ser colocada nos nós quatro ou cinco sem ter que esperar que outros serviços se movimentassem.

## <a name="defragmentation-pros-and-cons"></a>Prós e contras de desfragmentação
Então, o que são as outras trocas conceptuais? Aqui está uma mesa rápida de coisas para pensar:

| Prós de desfragmentação | Cons de desfragmentação |
| --- | --- |
| Permite a criação mais rápida de grandes serviços |Concentra a carga em menos nós, aumentando a contenção |
| Permite um menor movimento de dados durante a criação |Falhas podem afetar mais serviços e causar mais agitação |
| Permite uma rica descrição dos requisitos e recuperação do espaço |Configuração geral de Gestão de Recursos mais complexa |

Pode misturar métricas desfragmentadas e normais no mesmo cluster. O Cluster Resource Manager tenta consolidar as métricas de desfragmentação tanto quanto possível, espalhando as outras. Os resultados da mistura de desfragmentação e de estratégias de equilíbrio dependem de vários fatores, incluindo:
  - o número de métricas de equilíbrio vs. o número de métricas de desfragmentação
  - Se qualquer serviço usa ambos os tipos de métricas 
  - os pesos métricos
  - cargas métricas atuais
  
A experimentação é necessária para determinar a configuração exata necessária. Recomendamos uma medição completa das suas cargas de trabalho antes de ativar as métricas de desfragmentação na produção. Isto é especialmente verdade quando se mistura desfragmentação e métricas equilibradas dentro do mesmo serviço. 

## <a name="configuring-defragmentation-metrics"></a>Configurar métricas de desfragmentação
Configurar métricas de desfragmentação é uma decisão global no cluster, e as métricas individuais podem ser selecionadas para desfragmentação. Os seguintes fragmentos de config mostram como configurar métricas para desfragmentação. Neste caso, "Metric1" é configurado como uma métrica de desfragmentação, enquanto "Metric2" continuará a ser equilibrado normalmente. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

via ClusterConfig.json para implantações autónomas ou template.json para clusters alojados em Azure:

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
- O Gestor de Recursos de Cluster tem opções de homem para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](service-fabric-cluster-resource-manager-cluster-description.md)
- As métricas são como o Manjedoura de Recursos de Cluster de Tecido de Serviço gere o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
