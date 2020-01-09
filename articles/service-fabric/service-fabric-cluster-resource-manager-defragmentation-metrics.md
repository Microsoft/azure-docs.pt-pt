---
title: Desfragmentação de métricas no Azure Service Fabric
description: Saiba como usar a desfragmentação, ou empacotamento, como uma estratégia para métricas em Service Fabric. Essa técnica é útil para serviços muito grandes.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bba459be4408f4a4bc438bb33b0570a91e84f2cd
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563365"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentação de métricas e carregamento em Service Fabric
A estratégia padrão do Service Fabric cluster Resource Manager para gerenciar métricas de carga no cluster é distribuir a carga. Garantir que os nós sejam utilizados uniformemente evita pontos quentes e frios que levam à contenção e aos recursos desperdiçados. A distribuição de cargas de trabalho no cluster também é a mais segura em termos de sobreviver falhas, pois garante que uma falha não retire um grande percentual de uma determinada carga de trabalho. 

O Service Fabric cluster Resource Manager dá suporte a uma estratégia diferente para gerenciar a carga, que é a desfragmentação. A desfragmentação significa que, em vez de tentar distribuir a utilização de uma métrica no cluster, ela é consolidada. A consolidação é apenas uma inversão da estratégia de balanceamento padrão – em vez de minimizar o desvio médio padrão de carga de métrica, o Gerenciador de recursos de cluster tenta aumentá-lo.

## <a name="when-to-use-defragmentation"></a>Quando usar a desfragmentação
A distribuição da carga no cluster consome alguns dos recursos em cada nó. Algumas cargas de trabalho criam serviços que são excepcionalmente grandes e consomem a maioria ou todos os nós. Nesses casos, é possível que, quando houver grandes cargas de trabalho sendo criadas, não haja espaço suficiente em nenhum nó para executá-las. Cargas de trabalho grandes não são um problema no Service Fabric; nesses casos, o Gerenciador de recursos de cluster determina que ele precisa reorganizar o cluster para liberar espaço para essa grande carga de trabalho. No entanto, enquanto isso, a carga de trabalho precisa aguardar para ser agendada no cluster.

Se houver muitos serviços e estado para mover, pode levar muito tempo para que a carga de trabalho grande seja colocada no cluster. Isso é mais provável se outras cargas de trabalho no cluster também forem grandes e demorarem mais para serem reorganizadas. A equipe de Service Fabric mediu os tempos de criação em simulações desse cenário. Descobrimos que a criação de serviços grandes demorou muito mais assim que a utilização do cluster ficou acima entre 30% e 50%. Para lidar com esse cenário, introduzimos a desfragmentação como uma estratégia de balanceamento. Descobrimos que, para grandes cargas de trabalho, especialmente aquelas em que o tempo de criação era importante, a desfragmentação realmente ajudou as novas cargas de trabalho a serem agendadas no cluster.

Você pode configurar as métricas de desfragmentação para que o Gerenciador de recursos de cluster tente condensar proativamente a carga dos serviços em menos nós. Isso ajuda a garantir que quase sempre haja espaço para serviços grandes sem reorganizar o cluster. Não ter de reorganizar o cluster permite a criação rápida de cargas de trabalho grandes.

A maioria das pessoas não precisa de desfragmentação. Normalmente, os serviços são pequenos e, portanto, não é difícil encontrar espaço para eles no cluster. Quando a reorganização é possível, ela passa rapidamente, novamente porque a maioria dos serviços é pequena e pode ser movida rapidamente e em paralelo. No entanto, se você tiver grandes serviços e precisar que eles sejam criados rapidamente, a estratégia de desfragmentação será para você. Abordaremos as compensações de usar a desfragmentação em seguida. 

## <a name="defragmentation-tradeoffs"></a>Compensações de desfragmentação
A desfragmentação pode aumentar a impacto de falhas, já que mais serviços estão sendo executados em nós que falham. A desfragmentação também pode aumentar os custos, já que os recursos no cluster devem ser mantidos em reserva, aguardando a criação de cargas de trabalho grandes.

O diagrama a seguir fornece uma representação visual de dois clusters, um que é desfragmentado e outro não. 

<center>

![comparar clusters balanceados e desfragmentados][Image1]
</center>

No caso equilibrado, considere o número de movimentos que seriam necessários para colocar um dos maiores objetos de serviço. No cluster desfragmentado, a carga de trabalho grande pode ser colocada em nós quatro ou cinco sem precisar aguardar a movimentação de nenhum outro serviço.

## <a name="defragmentation-pros-and-cons"></a>Prós e contras de desfragmentação
Então, quais são as outras vantagens conceituais? Aqui está uma tabela rápida de coisas a considerar:

| Profissionais de desfragmentação | Contras de desfragmentação |
| --- | --- |
| Permite a criação mais rápida de serviços grandes |Concentra a carga em menos nós, aumentando a contenção |
| Permite uma movimentação de dados mais baixa durante a criação |As falhas podem afetar mais serviços e causar mais rotatividade |
| Permite uma descrição avançada dos requisitos e a recuperação do espaço |Configuração de gerenciamento de recursos geral mais complexa |

Você pode misturar métricas desfragmentadas e normais no mesmo cluster. O Gerenciador de recursos de cluster tenta consolidar as métricas de desfragmentação o máximo possível e, ao mesmo tempo, distribuir as outras. Os resultados da combinação de estratégias de desfragmentação e balanceamento dependem de vários fatores, incluindo:
  - o número de métricas de balanceamento versus o número de métricas de desfragmentação
  - Se qualquer serviço usa os dois tipos de métricas 
  - os pesos de métrica
  - cargas de métrica atuais
  
A experimentação é necessária para determinar a configuração exata necessária. Recomendamos uma medição completa de suas cargas de trabalho antes de habilitar as métricas de desfragmentação na produção. Isso é especialmente verdadeiro ao misturar a desfragmentação e métricas balanceadas dentro do mesmo serviço. 

## <a name="configuring-defragmentation-metrics"></a>Configurando métricas de desfragmentação
A configuração de métricas de desfragmentação é uma decisão global no cluster, e métricas individuais podem ser selecionadas para desfragmentação. Os trechos de configuração a seguir mostram como configurar métricas para desfragmentação. Nesse caso, "Metric1" é configurado como uma métrica de desfragmentação, enquanto "Metric2" continuará a ser balanceado normalmente. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

via ClusterConfig. JSON para implantações autônomas ou template. JSON para clusters hospedados do Azure:

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
- O Gerenciador de recursos de cluster tem as opções de Man para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre como [descrever um cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- As métricas são como o Gerenciador de recursos de Cluster Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [Este artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
