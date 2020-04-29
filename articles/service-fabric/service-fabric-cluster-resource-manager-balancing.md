---
title: Equilibre o seu cluster de tecido de serviço Azure
description: Uma introdução para equilibrar o seu cluster com o Gestor de Recursos de Cluster de Tecidos de Serviço.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b6df25b525975f2d4fe6a02064e81f359a804c58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416257"
---
# <a name="balancing-your-service-fabric-cluster"></a>Equilibrando o seu cluster de tecido de serviço
O Gestor de Recursos de Cluster de Tecidos de Serviço suporta alterações dinâmicas de carga, reagindo a adições ou remoção de nós ou serviços. Também corrige automaticamente as violações de restrições e reequilibra proactivamente o cluster. Mas quantas vezes estas ações são tomadas, e o que as desencadeia?

Existem três categorias diferentes de trabalho que o Cluster Resource Manager realiza. São:

1. Colocação – esta fase trata da colocação de quaisquer réplicas apátridas ou instâncias apátridas que faltam. A colocação inclui novos serviços e manuseamento de réplicas apátridas ou instâncias apátridas que falharam. A pagando e largando réplicas ou instâncias são manuseadas aqui.
2. Verificações de Restrições – esta fase verifica e corrige violações dos diferentes constrangimentos de colocação (regras) dentro do sistema. Exemplos de regras são coisas como garantir que os nódosos não excedam a capacidade e que os constrangimentos de colocação de um serviço são cumpridos.
3. Equilíbrio – esta fase verifica se o reequilíbrio é necessário com base no nível de equilíbrio pretendido configurado para diferentes métricas. Em caso afirmativo, tenta encontrar um acordo no aglomerado mais equilibrado.

## <a name="configuring-cluster-resource-manager-timers"></a>Configurar os temporizadores do gestor de recursos de cluster
O primeiro conjunto de controlos em torno do equilíbrio são um conjunto de tempos. Estes temporizadores regem a frequência com que o Cluster Resource Manager examina o cluster e toma medidas corretivas.

Cada um destes diferentes tipos de correções que o Cluster Resource Manager pode fazer é controlado por um temporizador diferente que rege a sua frequência. Quando cada temporizador dispara, a tarefa está programada. Por defeito, o Gestor de Recursos:

* digitaliza o seu estado e aplica atualizações (como a gravação de que um nó está em baixo) a cada 1/10 de segundo
* define a bandeira de verificação de colocação a cada segundo
* define a bandeira de verificação de restrição a cada segundo
* define a bandeira de equilíbrio a cada cinco segundos

Exemplos da configuração que rege estes temporizadores são abaixo:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

via ClusterConfig.json para implantações autónomas ou template.json para clusters alojados em Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Hoje o Cluster Resource Manager apenas realiza uma destas ações de cada vez, sequencialmente. É por isso que nos referimos a estes tempos como "intervalos mínimos" e às ações que são tomadas quando os temporizadores saem como "sinalização". Por exemplo, o Cluster Resource Manager cuida de pedidos pendentes para criar serviços antes de equilibrar o cluster. Como pode ver pelos intervalos de tempo predefinidos especificados, o Cluster Resource Manager procura qualquer coisa que precise de fazer com frequência. Normalmente, isto significa que o conjunto de alterações efetuadas durante cada passo é pequeno. Fazer pequenas alterações frequentemente permite que o Cluster Resource Manager responda quando as coisas acontecem no cluster. Os tempoizadores predefinidos fornecem alguns lotes, uma vez que muitos dos mesmos tipos de eventos tendem a ocorrer simultaneamente. 

Por exemplo, quando os nós falham, podem fazê-lo domínios inteiros de falha de cada vez. Todas estas falhas são capturadas durante a próxima atualização do estado após o *PLBRefreshGap*. As correções são determinadas durante as seguintes colocações, verificação de restrições e corridas de equilíbrio. Por defeito, o Cluster Resource Manager não está a analisar horas de alterações no cluster e a tentar resolver todas as alterações de uma só vez. Fazê-lo levaria a explosões de agitação.

O Cluster Resource Manager também precisa de algumas informações adicionais para determinar se o cluster desequilibrou. Para isso temos duas outras peças de configuração: *BalanceingThresholds* e *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Limiares de equilíbrio
Um limiar de equilíbrio é o principal controlo para desencadear o reequilíbrio. O Limiar de Equilíbrio para uma métrica é uma _razão_. Se a carga para uma métrica no nó mais carregado dividida pela quantidade de carga no nó menos carregado exceder o *Balancethreshold*da métrica, então o cluster é desequilibrado. Como resultado, o equilíbrio é desencadeado da próxima vez que o Gestor de Recursos do Cluster verificar. O temporizador *MinLoadBalancingInterval* define com que frequência o Gestor de Recursos do Cluster deve verificar se o reequilíbrio é necessário. Verificar não significa que aconteça alguma coisa. 

Os limiares de equilíbrio são definidos numa base métrica como parte da definição de cluster. Para mais informações sobre métricas, consulte [este artigo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

via ClusterConfig.json para implantações autónomas ou template.json para clusters alojados em Azure:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![Exemplo de limiar de equilíbrio][Image1]
</center>

Neste exemplo, cada serviço está a consumir uma unidade de alguma métrica. No exemplo superior, a carga máxima num nó é de cinco e o mínimo é dois. Digamos que o limiar de equilíbrio para esta métrica é três. Uma vez que o rácio no cluster é de 5/2 = 2,5 e que é inferior ao limiar de equilíbrio especificado de três, o cluster é equilibrado. Não é acionado qualquer equilíbrio quando o Gestor de Recursos de Cluster verificar.

No exemplo inferior, a carga máxima num nó é de 10, enquanto o mínimo é dois, resultando numa relação de cinco. Cinco é maior do que o limiar de equilíbrio designado de três para esta métrica. Como resultado, será agendada uma corrida de reequilíbrio da próxima vez que o temporizador de equilíbrio disparar. Numa situação como esta, alguma carga é normalmente distribuída para o Nó3. Como o Gestor de Recursos de Cluster de Tecidos de Serviço não usa uma abordagem gananciosa, alguma carga também pode ser distribuída para o Node2. 

<center>

![Equilibrar ações de exemplo de limiar][Image2]
</center>

> [!NOTE]
> "Equilibrar" lida com duas estratégias diferentes para gerir a carga no seu cluster. A estratégia padrão que o Cluster Resource Manager usa é distribuir carga pelos nós do cluster. A outra estratégia é a [desfragmentação.](service-fabric-cluster-resource-manager-defragmentation-metrics.md) A desfragmentação é realizada durante a mesma corrida de equilíbrio. As estratégias de equilíbrio e desfragmentação podem ser usadas para métricas diferentes dentro do mesmo cluster. Um serviço pode ter métricas de equilíbrio e desfragmentação. Para as métricas de desfragmentação, a relação das cargas no cluster desencadeia o reequilíbrio quando está _abaixo_ do limiar de equilíbrio. 
>

Ficar abaixo do limiar de equilíbrio não é um objetivo explícito. Os limiares de equilíbrio são apenas um *gatilho.* Quando o equilíbrio corre, o Gestor de Recursos de Cluster determina quais as melhorias que pode fazer, se houver. Só porque uma busca de equilíbrio é iniciada não significa nada se mexer. Às vezes, o cluster é desequilibrado, mas demasiado limitado para corrigir. Em alternativa, as melhorias requerem movimentos demasiado [dispendiosos.](service-fabric-cluster-resource-manager-movement-cost.md)

## <a name="activity-thresholds"></a>Limiares de atividade
Às vezes, embora os nós sejam relativamente desequilibrados, a quantidade *total* de carga no cluster é baixa. A falta de carga pode ser um mergulho transitório, ou porque o cluster é novo e apenas ficar com botas presas. Em qualquer dos casos, pode não querer passar o tempo equilibrando o cluster porque há pouco a ganhar. Se o cluster sofresse equilíbrio, gastaria rede e calcularia recursos para mover as coisas sem fazer qualquer grande diferença *absoluta.* Para evitar movimentos desnecessários, há outro controlo conhecido como Limiares de Atividade. Os Limiares de Atividade permitem especificar algum limite absoluto mais baixo para a atividade. Se nenhum nó for acima deste limiar, o equilíbrio não é desencadeado mesmo que o Limiar de Equilíbrio seja cumprido.

Digamos que mantemos o nosso Limiar de Equilíbrio de três para esta métrica. Digamos também que temos um Limiar de Atividade de 1536. No primeiro caso, enquanto o cluster é desequilibrado de acordo com o Limiar de Equilíbrio, não há nó que cumpra o Limiar de Atividade, por isso nada acontece. No exemplo inferior, o Node1 está acima do Limiar de Atividade. Uma vez que tanto o Limiar de Equilíbrio como o Limiar de Atividade para a métrica são ultrapassados, o equilíbrio está programado. Como exemplo, vamos olhar para o seguinte diagrama: 

<center>

![Exemplo de limiar de atividade][Image3]
</center>

Tal como os limiares de equilíbrio, os limiares de atividade são definidos por métrica através da definição de cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

via ClusterConfig.json para implantações autónomas ou template.json para clusters alojados em Azure:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Os limiares de equilíbrio e de atividade estão ambos ligados a uma métrica específica - o equilíbrio só é desencadeado se o limiar de equilíbrio e o limiar de atividade forem ultrapassados para a mesma métrica.

> [!NOTE]
> Quando não especificado, o limiar de equilíbrio para uma métrica é 1, e o Limiar de Atividade é 0. Isto significa que o Cluster Resource Manager tentará manter essa métrica perfeitamente equilibrada para qualquer carga. Se estiver a usar métricas personalizadas, recomenda-se que defina explicitamente os seus próprios limites de equilíbrio e atividade para as suas métricas. 
>

## <a name="balancing-services-together"></a>Serviços de equilíbrio em conjunto
Se o cluster está desequilibrado ou não é uma decisão em todo o cluster. No entanto, a forma como o corrigimos é a movimentação de réplicas e instâncias individuais de serviço. Isto faz sentido, certo? Se a memória estiver empilhada num nó, várias réplicas ou instâncias podem estar a contribuir para isso. A fixação do desequilíbrio pode exigir a deslocação de qualquer uma das réplicas apátridas ou instâncias apátridas que utilizam a métrica desequilibrada.

Ocasionalmente, porém, um serviço que não estava desequilibrado move-se (lembre-se da discussão de pesos locais e globais mais cedo). Por que um serviço seria movido quando todas as métricas desse serviço estavam equilibradas? Vamos ver um exemplo:

- Digamos que há quatro serviços, Serviço1, Serviço2, Serviço3 e Serviço4. 
- Serviço1 reporta métricas Métricas 1 e Métrica2. 
- Serviço2 reporta métricas Métrica2 e Métrica3. 
- Serviço3 reporta métricas Métricas 3 e Metric4.
- Serviço4 reporta métrica métrica99. 

Certamente pode ver para onde vamos aqui: há uma corrente! Não temos realmente quatro serviços independentes, temos três serviços que estão relacionados e um que está fora por si só.

<center>

![Serviços de Equilíbrio Juntos][Image4]
</center>

Por causa desta cadeia, é possível que um desequilíbrio nas métricas 1-4 possa fazer com que réplicas ou instâncias pertencentes aos serviços 1-3 se movam. Também sabemos que um desequilíbrio nas Métricas 1, 2 ou 3 não pode causar movimentos no Serviço4. Não faria sentido, uma vez que mover as réplicas ou instâncias pertencentes ao Service4 não pode fazer absolutamente nada para impactar o equilíbrio das Métricas 1-3.

O Cluster Resource Manager descobre automaticamente quais os serviços relacionados. Adicionar, remover ou alterar as métricas dos serviços pode ter impacto nas suas relações. Por exemplo, entre duas corridas de balanceamento Service2 pode ter sido atualizada para remover O Métrico2. Isto quebra a cadeia entre o Service1 e o Service2. Agora, em vez de dois grupos de serviços relacionados, há três:

<center>

![Serviços de Equilíbrio Juntos][Image5]
</center>

## <a name="next-steps"></a>Passos seguintes
* As métricas são como o Manjedoura de Recursos de Cluster de Tecido de Serviço gere o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
* O Custo de Movimento é uma forma de sinalizar ao Gestor de Recursos cluster que certos serviços são mais caros para se movimentardo do que outros. Para mais informações sobre o custo de movimento, consulte [este artigo](service-fabric-cluster-resource-manager-movement-cost.md)
* O Cluster Resource Manager tem vários aceleradores que pode configurar para abrandar o ressurgimento do cluster. Normalmente não são necessários, mas se precisares deles podes aprender sobre eles [aqui.](service-fabric-cluster-resource-manager-advanced-throttling.md)
* O Cluster Resource Manager pode reconhecer e lidar com o subagrupamento (uma situação que por vezes surge quando se utilizam restrições de colocação e equilíbrio). Para aprender como o subagrupamento pode afetar o equilíbrio e como pode lidar com isso, consulte [aqui](cluster-resource-manager-subclustering.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
