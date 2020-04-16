---
title: Equilíbrio das métricas subagrupadas
description: O efeito das restrições de colocação no equilíbrio e como lidar com ele
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430646"
---
# <a name="balancing-of-subclustered-metrics"></a>Equilíbrio das métricas subagrupadas

## <a name="what-is-subclustering"></a>O que é o subagrupamento

O subagrupamento acontece quando os serviços com diferentes restrições de colocação têm uma métrica comum e ambos reportam carga para isso. Se a carga reportada pelos serviços diferir significativamente, a carga total nos nós terá um grande desvio padrão e parecerá que o cluster está desequilibrado, mesmo quando tem o melhor equilíbrio possível.

## <a name="how-subclustering-affects-load-balancing"></a>Como o subagrupamento afeta o equilíbrio da carga

Se a carga reportada pelos serviços em nós diferentes difere significativamente, pode parecer que há um grande desequilíbrio onde não há nenhuma. Além disso, se o falso desequilíbrio causado pelo subagrupamento for maior do que o desequilíbrio real, tem o potencial de confundir o algoritmo de equilíbrio do Gestor de Recursos e produzir um equilíbrio sub-ideal no cluster.

Por exemplo, digamos que temos quatro serviços e todos reportam uma carga para métrica1:

* Serviço A – tem uma restrição de colocação "NodeType==Type1", reporta uma carga de 10
* Serviço B – tem uma restrição de colocação "NodeType==Type1", reporta uma carga de 10
* Serviço C – tem uma restrição de colocação "NodeType==Type2", reporta uma carga de 100
* Serviço D – tem uma restrição de colocação "NodeType==Type2", reporta uma carga de 100
* E temos quatro nós. Dois deles têm nodeType definido como "Type1" e os outros dois são "Type2"

E temos a seguinte colocação:

<center>

![Exemplo de colocação subagrupada][Image1]
</center>

O cluster pode parecer desequilibrado, temos uma grande carga nos nós 3 e 4, mas esta colocação cria o melhor equilíbrio possível nesta situação.

O Gestor de Recursos pode reconhecer situações de subagrupamento e em quase todos os casos pode produzir o equilíbrio ideal para a situação dada.

Para algumas situações excecionais em que o Gestor de Recursos não é capaz de equilibrar uma métrica subagrupada, ainda detetará subagrupamentos e gerará um relatório de saúde para o aconselhar a corrigir o problema.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Tipos de subagrupamento e como são tratados

As situações de subagrupamento podem ser classificadas em três categorias diferentes. A categoria de uma situação específica de subagrupamento determina como será tratada pelo Gestor de Recursos.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Primeira categoria - subagrupamento plano com grupos de nó disarticulados

Esta categoria tem a forma mais simples de subagrupamento onde os nós podem ser separados em diferentes grupos e cada serviço só pode ser colocado em nós num desses grupos. Cada nó pertence a um grupo e apenas a um grupo. A situação acima descrita pertence a esta categoria, tal como a maioria das situações de subagrupamento. 

Para as situações nesta categoria, o Gestor de Recursos pode produzir o equilíbrio ideal e não é necessária mais nenhuma intervenção.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Segunda categoria - subagrupamento com grupos hierárquicos

Esta situação acontece quando um grupo de nós permitido para um serviço é um subconjunto do grupo de nós permitido para outro serviço. O exemplo mais comum desta situação é quando algum serviço tem uma restrição de colocação definida e outro serviço não tem restrição de colocação e pode ser colocado em qualquer nó.

Exemplo:

* Serviço A: sem restrição de colocação
* Serviço B: restrição de colocação "NodeType==Type1"
* Serviço C: restrição de colocação "NodeType==Type2"

Esta configuração cria uma relação subset-superset entre grupos de nó para diferentes serviços.

<center>

![Subconjuntos de superconjuntos][Image2]
</center>

Nesta situação, existe a possibilidade de se fazer um equilíbrio sub-óptimo.

O Gestor de Recursos reconhecerá esta situação e produzirá um relatório de saúde aconselhando-o a dividir o Serviço A em dois serviços – Serviço A1 que pode ser colocado em nós tipo1 e serviço A2 que podem ser colocados em nós do Tipo2. Isto vai trazer-nos de volta a uma situação de primeira categoria que pode ser equilibrada da melhor forma.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Terceira categoria - subagrupamento com sobreposição parcial entre conjuntos de nó

Esta situação acontece quando há uma sobreposição parcial entre conjuntos de nós em que alguns serviços podem ser colocados.

Por exemplo, se tivermos uma propriedade de nó chamada NodeColor e tivermos três nós:

* Nó 1: NodeColor=Vermelho
* Nó 2: NodeColor=Azul
* Nó 2: NodeColor=Verde

E temos dois serviços:

* Serviço A: com restrição de colocação "Color=Vermelho [Vermelho] Cor==Azul"
* Serviço B: com restrição de colocação "Color==Azul [Azul] Cor==Verde"

Por isso, o serviço A pode ser colocado nos nós 1 e 2 e o serviço B pode ser colocado nos pontos 2 e 3.

Nesta situação, existe a possibilidade de se fazer um equilíbrio sub-óptimo.

O Gestor de Recursos reconhecerá esta situação e produzirá um relatório de saúde aconselhando-o a dividir alguns dos serviços.

Para esta situação, o Gestor de Recursos não pode dar uma proposta sobre como dividir os serviços, uma vez que várias divisões podem ser feitas e não há forma de estimar qual seria o caminho ideal para dividir os serviços.

## <a name="configuring-subclustering"></a>Configuração do subagrupamento

O comportamento do Gestor de Recursos sobre o subagrupamento pode ser modificado modificando os seguintes parâmetros de configuração:
* SubclusteringEnabled - o parâmetro determina se o Gestor de Recursos terá em conta o subagrupamento ao fazer o equilíbrio de carga. Se este parâmetro for desligado, o Gestor de Recursos ignorará o subagrupamento e tentará alcançar o equilíbrio ideal a nível global. O valor padrão deste parâmetro é falso.
* SubclusteringReportingPolicy - determina como o Gestor de Recursos emitirá relatórios de saúde para subagrupamento hierárquico e parcial. Um valor nulo significa que os relatórios de saúde sobre o subagrupamento são desligados, "1" significa que serão produzidos relatórios de saúde para situações de subagrupamento sub-óptimas e um valor de "2" produzirá relatórios de saúde "OK". O valor padrão para este parâmetro é "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

via ClusterConfig.json para implantações autónomas ou template.json para clusters alojados em Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Passos seguintes
* Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre [a carga](service-fabric-cluster-resource-manager-balancing.md) de equilíbrio
* Para saber como os seus serviços podem ser limitados a apenas ser colocados em certos nódosos ver [propriedades do Nó e restrições](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) de colocação

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
