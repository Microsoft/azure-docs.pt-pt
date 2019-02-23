---
title: Desfragmentação de métricas no Azure Service Fabric | Documentos da Microsoft
description: Uma descrição geral do uso de desfragmentação ou empacotar como uma estratégia para métricas no Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: a50d65940ec12f9883f8991307c3cc5692d96f06
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738084"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentação de métricas e de carga no Service Fabric
O Service Fabric Cluster Gestor de recursos da estratégia de padrão para o gerenciamento de métricas de carga no cluster é distribuir a carga. Garantir que nós são igualmente utilizados evita a pontos de acesso frequente e esporádica que levam a contenção e desperdício de recursos. Distribuição de cargas de trabalho no cluster também é mais segura em termos de sobreviverem à falhas, uma vez que ele garante que uma falha não experimentar uma grande porcentagem de uma determinada carga de trabalho. 

O Gestor de recursos de Cluster do Service Fabric suporta uma estratégia diferente para o gerenciamento de carga, o que é a desfragmentação. Desfragmentação significa que em vez de tentar distribuir a utilização de uma métrica no cluster, ele é consolidado. A consolidação é apenas uma inversão do padrão balanceamento estratégia – em vez de minimizar o desvio-padrão de média de métricas de carga, o Gestor de recursos de Cluster tenta aumentá-la.

## <a name="when-to-use-defragmentation"></a>Quando utilizar a desfragmentação
Distribuir a carga no cluster consome alguns dos recursos em cada nó. Algumas cargas de trabalho criar serviços que são excepcionalmente grandes e consumam a maior parte ou todo um nó. Nestes casos, é possível que quando existem grandes cargas de trabalho que são criadas que existem não espaço suficiente em qualquer nó para executá-los. Grandes cargas de trabalho não são um problema nos recursos de infraestrutura do serviço; Nestes casos, o Gestor de recursos de Cluster determina que ele precisa reorganize o cluster para disponibilizar espaço para esta carga de trabalho grandes. No entanto, enquanto isso essa carga de trabalho tem de esperar ser agendada no cluster.

Se existirem muitos serviços e o estado para mover-se, em seguida, pode demorar muito tempo para a carga de trabalho grande ser colocado no cluster. Isso é mais provável se outras cargas de trabalho no cluster também são grandes e então demoram mais tempo a reorganize. A equipe de recursos de infraestrutura do serviço medido tempos de criação em simulações deste cenário. Descobrimos que demorou muito mais tempo criando serviços grandes assim que a utilização do cluster tem acima entre 30% e 50%. Para lidar com este cenário, introduzimos a desfragmentação como uma estratégia de balanceamento. Descobrimos que para grandes cargas de trabalho, especialmente aqueles em que a hora de criação foi importante, a desfragmentação realmente ajudou a essas novas cargas de trabalho obterem agendadas no cluster.

Pode configurar as métricas de desfragmentação para que o Gestor de recursos de Cluster para proativamente tentar condensar a carga dos serviços em menos nós. Isto ajuda a garantir que quase sempre há espaço para serviços grandes sem a reorganização de cluster. Não ter de reorganize o cluster permite a criação de grandes cargas de trabalho rapidamente.

A maioria das pessoas não precisa de desfragmentação. Os serviços são, normalmente, ser pequeno, pelo que não é difícil encontrar espaço para eles no cluster. Quando é possível a reorganização, ele vai rapidamente, novamente porque a maioria dos serviços são pequenos e podem ser movidos rapidamente e em paralelo. No entanto, se tiver serviços de grandes e precisar de criar rapidamente, em seguida, a estratégia de desfragmentação é para si. Abordaremos as vantagens e desvantagens da utilização de desfragmentação em seguida. 

## <a name="defragmentation-tradeoffs"></a>Compromissos de desfragmentação
A desfragmentação pode aumentar impactfulness de falhas, uma vez que mais serviços estão em execução em nós que não obedeçam. A desfragmentação também pode aumentar os custos, uma vez que os recursos no cluster devem ser mantidos em reserva, a aguardar a criação de grandes cargas de trabalho.

O diagrama seguinte fornece uma representação visual de dois clusters, que é desfragmentados e outro que não é. 

<center>
![Comparar com balanceamento e desfragmentados Clusters][Image1]
</center>

No caso do equilibrada, considere o número de movimentos que seria necessário colocar um dos maiores objetos do serviço. No cluster desfragmentado, a carga de trabalho grandes poderia ser colocada em quatro ou cinco nós, sem ter de esperar por quaisquer outros serviços mover.

## <a name="defragmentation-pros-and-cons"></a>Desfragmentação prós e contras
Então, quais são esses outros compromissos conceituais? Aqui está uma tabela rápida de coisas a considerar:

| Profissionais de desfragmentação | Contras de desfragmentação |
| --- | --- |
| Permite a criação rápida dos serviços de grandes |Concentrates carregar em menos nós, aumentando a contenção |
| Permite reduzir o movimento de dados durante a criação |Falhas podem afetar mais serviços e fazer com que o volume de alterações mais |
| Permite Rica descrição dos requisitos e recuperação de espaço |Configuração de gestão de recursos global mais complexa |

Pode misturar as métricas desfragmentadas e normais no mesmo cluster. O Gestor de recursos de Cluster tenta consolidar as métricas de desfragmentação tanto quanto possíveis e dispersão os outros. Os resultados de misturar desfragmentação e balanceamento de estratégias depende de diversos fatores, incluindo:
  - o número de balanceamento de métricas vs. o número de métricas de desfragmentação
  - Se a qualquer serviço utiliza os dois tipos de métricas 
  - os pesos de métrica
  - cargas de métricas atuais
  
Experimentação é necessário para determinar a configuração exata necessária. Recomendamos que medição completa das cargas de trabalho antes de ativar as métricas de desfragmentação em produção. Isso é especialmente verdadeiro quando a mistura de desfragmentação e métricas equilibradas no mesmo serviço. 

## <a name="configuring-defragmentation-metrics"></a>Configuração de desfragmentação de métricas
Configurar métricas de desfragmentação é uma decisão global no cluster e métricas individuais podem ser selecionadas para a desfragmentação. Os fragmentos de configuração seguintes mostram como configurar as métricas de desfragmentação. Neste caso, "Metric1" está configurado como uma métrica de desfragmentação, enquanto "Metric2" irá continuar a ser balanceado normalmente. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

por meio de ClusterConfig.json das implementações autónomas ou Template para o Azure alojados clusters:

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


## <a name="next-steps"></a>Passos Seguintes
- O Gestor de recursos do Cluster tem opções de man para descrever o cluster. Para obter mais informações sobre ele, visite este artigo no [descrever um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- As métricas são como o Service Fabric Cluster Resource Manager gere o consumo e a capacidade do cluster. Para saber mais sobre métricas e como configurá-las, confira [neste artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
