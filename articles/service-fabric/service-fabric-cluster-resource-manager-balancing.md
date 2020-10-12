---
title: Equilibre o seu cluster de tecido de serviço Azure
description: Uma introdução para equilibrar o seu cluster com o Service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b6df25b525975f2d4fe6a02064e81f359a804c58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81416257"
---
# <a name="balancing-your-service-fabric-cluster"></a>Equilibrar o seu cluster de tecido de serviço
O Service Fabric Cluster Resource Manager suporta alterações dinâmicas de carga, reagindo a adições ou remoção de nós ou serviços. Também corrige automaticamente as violações de restrições e reequilibra proativamente o cluster. Mas com que frequência estas ações são tomadas, e o que as despoleta?

Existem três categorias diferentes de trabalho que o Cluster Resource Manager realiza. A saber:

1. Colocação – esta fase trata da colocação de quaisquer réplicas imponentes ou casos apátridas que estejam em falta. A colocação inclui tanto novos serviços como o manuseamento de réplicas declaradas ou casos apátridas que falharam. Apagar e largar réplicas ou casos são tratados aqui.
2. Verificação de Restrições – esta fase verifica e corrige violações das diferentes restrições de colocação (regras) dentro do sistema. Exemplos de regras são coisas como garantir que os nós não estão sobrecapacidade e que os constrangimentos de colocação de um serviço são cumpridos.
3. Equilíbrio – esta fase verifica para ver se o reequilíbrio é necessário com base no nível de equilíbrio configurado desejado para diferentes métricas. Em caso afirmativo, tenta encontrar um acordo no aglomerado mais equilibrado.

## <a name="configuring-cluster-resource-manager-timers"></a>Configurar temporizadores de gestor de recursos de cluster
O primeiro conjunto de controlos em torno do equilíbrio são um conjunto de temporizadores. Estes temporizadores regem a frequência com que o Cluster Resource Manager examina o cluster e toma ações corretivas.

Cada um destes diferentes tipos de correções que o Cluster Resource Manager pode fazer é controlado por um temporizador diferente que rege a sua frequência. Quando cada temporizador dispara, a tarefa está programada. Por predefinição, o Gestor de Recursos:

* digitaliza o seu estado e aplica atualizações (como registar que um nó está baixo) a cada 1/10 de segundo
* define a bandeira de verificação de colocação a cada segundo
* define a bandeira de verificação de restrição a cada segundo
* define a bandeira de equilíbrio a cada cinco segundos

Exemplos da configuração que rege estes temporizadores estão abaixo:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

através ClusterConfig.jspara implantações autónomas ou Template.jspara aglomerados azure hospedados:

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

Hoje, o Cluster Resource Manager só realiza uma destas ações de cada vez, sequencialmente. É por isso que nos referimos a estes temporizadores como "intervalos mínimos" e as ações que são tomadas quando os temporizadores saem como "sinalização de bandeiras". Por exemplo, o Cluster Resource Manager cuida dos pedidos pendentes para criar serviços antes de equilibrar o cluster. Como pode ver pelos intervalos de tempo especificados, o Cluster Resource Manager verifica tudo o que precisa de fazer com frequência. Normalmente, isto significa que o conjunto de alterações efetuadas durante cada passo é pequeno. Fazer pequenas alterações frequentemente permite que o Cluster Resource Manager seja responsivo quando as coisas acontecem no cluster. Os temporizadores predefinidos fornecem alguns lotes, uma vez que muitos dos mesmos tipos de eventos tendem a ocorrer simultaneamente. 

Por exemplo, quando os nós falham, podem fazê-lo domínios inteiros de falhas de cada vez. Todas estas falhas são capturadas durante a próxima atualização do estado após o *PLBRefreshGap*. As correções são determinadas durante a colocação seguinte, verificação de restrições e corridas de equilíbrio. Por predefinição, o Cluster Resource Manager não está a analisar horas de alterações no cluster e a tentar resolver todas as alterações de uma só vez. Fazê-lo levaria a explosões de agitação.

O Cluster Resource Manager também precisa de algumas informações adicionais para determinar se o cluster está desequilibrado. Para isso temos duas outras peças de configuração: *BalanceThresholds* e *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Limiares de equilíbrio
Um limiar de equilíbrio é o principal controlo para desencadear o reequilíbrio. O Limiar de Equilíbrio para uma métrica é uma _razão_. Se a carga para uma métrica no nó mais carregado dividido pela quantidade de carga no nó menos carregado exceder o *BalanceThreshold*da métrica, então o cluster é desequilibrado. Como resultado, o equilíbrio é desencadeado da próxima vez que o Cluster Resource Manager verificar. O *temporizador MinLoadBalancingInterval* define com que frequência o Gestor de Recursos do Cluster deve verificar se é necessário reequilibrada. Verificar não significa que algo aconteça. 

Os limiares de equilíbrio são definidos numa base métrica como parte da definição do cluster. Para mais informações sobre métricas, confira [este artigo.](service-fabric-cluster-resource-manager-metrics.md)

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

através ClusterConfig.jspara implantações autónomas ou Template.jspara aglomerados azure hospedados:

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

![Exemplo do limiar de equilíbrio][Image1]
</center>

Neste exemplo, cada serviço está a consumir uma unidade de alguma métrica. No exemplo superior, a carga máxima num nó é de cinco e a mínima é de dois. Digamos que o limiar de equilíbrio para esta métrica é três. Uma vez que o rácio no cluster é 5/2 = 2,5 e que é inferior ao limiar de equilíbrio especificado de três, o cluster é equilibrado. Não é acionado qualquer equilíbrio quando o Gestor de Recursos do Cluster verifica.

No exemplo inferior, a carga máxima num nó é de 10, enquanto a mínima é de dois, resultando numa relação de cinco. Cinco é maior do que o limiar de equilíbrio designado de três para esta métrica. Como resultado, será agendada uma corrida de reequilíbrio da próxima vez que o temporizador de equilíbrio disparar. Numa situação como esta, alguma carga é geralmente distribuída ao Nó3. Como o Service Fabric Cluster Resource Manager não utiliza uma abordagem gananciosa, alguma carga também pode ser distribuída para o Node2. 

<center>

![Ações de exemplo de limiar de equilíbrio][Image2]
</center>

> [!NOTE]
> "Balanceamento" lida com duas estratégias diferentes para gerir a carga no seu cluster. A estratégia padrão que o Cluster Resource Manager utiliza é distribuir carga pelos nós do cluster. A outra estratégia é [a desfragmentação.](service-fabric-cluster-resource-manager-defragmentation-metrics.md) A desfragmentação é realizada durante o mesmo balanço. As estratégias de equilíbrio e desfragmentação podem ser usadas para diferentes métricas dentro do mesmo cluster. Um serviço pode ter métricas de equilíbrio e desfragmentação. Para as métricas de desfragmentação, a razão das cargas no cluster provoca o reequilíbrio quando está _abaixo_ do limiar de equilíbrio. 
>

Ficar abaixo do limiar de equilíbrio não é um objetivo explícito. Os limiares de equilíbrio são apenas um *gatilho.* Ao equilibrar os resultados, o Cluster Resource Manager determina quais as melhorias que pode fazer, se houver. Só porque uma busca de equilíbrio é iniciada não significa que nada se mexa. Às vezes, o aglomerado é desequilibrado, mas demasiado limitado para corrigir. Em alternativa, as melhorias requerem movimentos demasiado [dispendiosos).](service-fabric-cluster-resource-manager-movement-cost.md)

## <a name="activity-thresholds"></a>Limiares de atividade
Às vezes, embora os nós sejam relativamente desequilibrados, a quantidade *total* de carga no cluster é baixa. A falta de carga pode ser um mergulho transitório, ou porque o cluster é novo e está apenas a ser preso às botas. Em qualquer dos casos, pode não querer passar o tempo a equilibrar o aglomerado porque há pouco a ganhar. Se o cluster se equilibrasse, gastarias recursos de rede e de cálculo para movimentar as coisas sem fazer nenhuma grande diferença *absoluta.* Para evitar movimentos desnecessários, há outro controlo conhecido como Limiares de Atividade. Os Limiares de Atividade permitem especificar algum limite absolutamente inferior para a atividade. Se nenhum nó estiver acima deste limiar, o equilíbrio não é desencadeado mesmo que o Limiar de Equilíbrio seja atingido.

Digamos que conservamos o nosso limiar de equilíbrio de três para esta métrica. Digamos também que temos um limiar de atividade de 1536. No primeiro caso, enquanto o cluster é desequilibrado por baixo do limiar de equilíbrio, nenhum nó cumpre o Limiar de Atividade, então nada acontece. No exemplo inferior, o Nó1 está acima do Limiar de Atividade. Uma vez que tanto o Limiar de Equilíbrio como o Limiar de Atividade para a métrica são ultrapassados, o equilíbrio está agendado. Como exemplo, vejamos o seguinte diagrama: 

<center>

![Exemplo do limiar de atividade][Image3]
</center>

Tal como os limiares de equilíbrio, os limiares de atividade são definidos por métrica através da definição de cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

através ClusterConfig.jspara implantações autónomas ou Template.jspara aglomerados azure hospedados:

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
> Quando não especificado, o Limiar de Equilíbrio para uma métrica é 1 e o Limiar de Atividade é 0. Isto significa que o Cluster Resource Manager tentará manter esta métrica perfeitamente equilibrada para qualquer carga dada. Se estiver a utilizar métricas personalizadas, recomenda-se que defina explicitamente os seus próprios limiares de equilíbrio e atividade para as suas métricas. 
>

## <a name="balancing-services-together"></a>Serviços de equilíbrio em conjunto
Se o cluster está desequilibrado ou não é uma decisão a nível do cluster. No entanto, a forma como o corrigimos é mover réplicas de serviço individuais e instâncias ao redor. Isto faz sentido, certo? Se a memória estiver empilhada num nó, várias réplicas ou instâncias podem estar a contribuir para isso. Corrigir o desequilíbrio pode exigir mover qualquer uma das réplicas imponentes ou instâncias apátridas que usam a métrica desequilibrada.

Ocasionalmente, porém, um serviço que não foi por si só desequilibrado é movido (lembre-se da discussão de pesos locais e globais mais cedo). Por que um serviço seria movido quando todas as métricas do serviço estavam equilibradas? Vejamos um exemplo:

- Digamos que existem quatro serviços, Serviço1, Serviço2, Serviço3 e Serviço4. 
- O Serviço1 reporta métricas métricas Métrica1 e Métrica2. 
- O serviço2 reporta métricas métricas métricas métricas métricas métricas e métricas. 
- O serviço3 reporta métricas métricas Métrica3 e Métrica4.
- O serviço4 reporta a métrica Metric99. 

Certamente pode ver onde vamos aqui: há uma corrente! Não temos realmente quatro serviços independentes, temos três serviços relacionados e um que está fora por si só.

<center>

![Serviços de equilíbrio juntos][Image4]
</center>

Por causa desta cadeia, é possível que um desequilíbrio nas métricas 1-4 possa causar réplicas ou casos pertencentes aos serviços 1-3 para se deslocarem. Também sabemos que um desequilíbrio nas Métricas 1, 2 ou 3 não pode causar movimentos no Serviço4. Não faria sentido, uma vez que mover as réplicas ou instâncias pertencentes ao Service4 não pode fazer absolutamente nada para impactar o equilíbrio das Métricas 1-3.

O Cluster Resource Manager descobre automaticamente quais os serviços relacionados. Adicionar, remover ou alterar as métricas dos serviços pode ter impacto nas suas relações. Por exemplo, entre duas execuções de balanceamento o Serviço2 pode ter sido atualizado para remover a Metric2. Isto quebra a cadeia entre o Serviço1 e o Serviço2. Agora, em vez de dois grupos de serviços relacionados, há três:

<center>

![Serviços de equilíbrio juntos][Image5]
</center>

## <a name="next-steps"></a>Passos seguintes
* As métricas são como a Manjedoura de Recursos do Cluster de Tecidos de Serviço gere o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
* O Custo de Movimento é uma forma de sinalizar ao Cluster Resource Manager que certos serviços são mais caros de movimentar do que outros. Para saber mais sobre o custo de movimento, consulte [este artigo](service-fabric-cluster-resource-manager-movement-cost.md)
* O Cluster Resource Manager tem vários aceleradores que pode configurar para abrandar o impacto no cluster. Normalmente não são necessários, mas se precisares deles, podes aprender sobre eles [aqui.](service-fabric-cluster-resource-manager-advanced-throttling.md)
* O Cluster Resource Manager pode reconhecer e lidar com subclustering (uma situação que por vezes surge quando utiliza restrições de colocação e equilíbrio). Para saber como o subclustering pode afetar o equilíbrio e como pode lidar com isso, veja [aqui](cluster-resource-manager-subclustering.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
