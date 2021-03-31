---
title: Equilíbrio das métricas subclusas
description: O efeito das restrições de colocação no equilíbrio e como lidar com isso
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "82183126"
---
# <a name="balancing-of-subclustered-metrics"></a>Equilíbrio das métricas subclusas

## <a name="what-is-subclustering"></a>O que é subcluster

A subclustering acontece quando os serviços com diferentes restrições de colocação têm uma métrica comum e ambos reportam carga para o mesmo. Se a carga comunicada pelos serviços diferir significativamente, a carga total nos nós terá um grande desvio padrão e pareceria que o cluster está desequilibrado, mesmo quando tem o melhor equilíbrio possível.

## <a name="how-subclustering-affects-load-balancing"></a>Como o subclustering afeta o equilíbrio da carga

Se a carga reportada pelos serviços em diferentes nós diferir significativamente, pode parecer que há um grande desequilíbrio onde não há nenhum. Além disso, se o falso desequilíbrio causado pelo subclustering for maior do que o desequilíbrio real, tem o potencial de confundir o algoritmo de equilíbrio do Gestor de Recursos e produzir equilíbrio subóptocal no cluster.

Por exemplo, digamos que temos quatro serviços e todos reportam uma carga para a métrica Métrica1:

* Serviço A – tem uma restrição de colocação "NodeType==Frontend", reporta uma carga de 10
* Serviço B – tem uma restrição de colocação "NodeType==Frontend", reporta uma carga de 10
* Serviço C – tem uma restrição de colocação "NodeType==Backend", reporta uma carga de 100
* Serviço D – tem uma restrição de colocação "NodeType==Backend", reporta uma carga de 100
* E temos quatro nós. Dois deles têm NodeType definido como "Frontend" e os outros dois são "Backend"

E temos a seguinte colocação:

<center>

![Exemplo de colocação subclusa][Image1]
</center>

O cluster pode parecer desequilibrado, temos uma grande carga nos nós 3 e 4, mas esta colocação cria o melhor equilíbrio possível nesta situação.

O Gestor de Recursos pode reconhecer situações de subclustering e em quase todos os casos pode produzir o equilíbrio ideal para a situação dada.

Para algumas situações excecionais quando o Gestor de Recursos não é capaz de equilibrar o melhor possível uma métrica subclusa, ainda detetará subclustering e gerará um relatório de saúde para o aconselhar a corrigir o problema.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Tipos de subclustering e como são tratados

As situações de subclustering podem ser classificadas em três categorias diferentes. A categoria de uma situação específica de subclustering determina como será tratada pelo Gestor de Recursos.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Primeira categoria - subclustering plano com grupos de nó desmedida

Esta categoria tem a forma mais simples de subclustering onde os nós podem ser separados em diferentes grupos e cada serviço só pode ser colocado em nós num desses grupos. Cada nó pertence a um grupo e apenas a um grupo. A situação acima descrita pertence a esta categoria, tal como a maioria das situações de subcluster. 

Para as situações nesta categoria, o Gestor de Recursos pode produzir o equilíbrio ideal e não é necessária mais intervenção.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Segunda categoria - subclustering com grupos hierárquicos de nó

Esta situação acontece quando um grupo de nós permitidos para um serviço é um subconjunto do grupo de nós permitido para outro serviço. O exemplo mais comum desta situação é quando algum serviço tem um constrangimento de colocação definido e outro serviço não tem restrição de colocação e pode ser colocado em qualquer nó.

Exemplo:

* Serviço A: sem restrição de colocação
* Serviço B: restrição de colocação "NodeType==Frontend"
* Serviço C: restrição de colocação "NodeType=Backend"

Esta configuração cria uma relação subconjunto-superconjunto entre grupos de nós para diferentes serviços.

<center>

![Subclusters subconjuntos subconjuntos][Image2]
</center>

Nesta situação, existe a possibilidade de se fazer um equilíbrio sub-ideal.

O Gestor de Recursos reconhecerá esta situação e elaborará um relatório de saúde aconselhando-o a dividir o Serviço A em dois serviços – Serviço A1 que pode ser colocado nos nós frontend e no serviço A2 que pode ser colocado em nós backend. Isto levar-nos-á de volta à situação de primeira categoria que pode ser equilibrada da melhor forma.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Terceira categoria - subclustering com sobreposição parcial entre conjuntos de nós

Esta situação acontece quando há uma sobreposição parcial entre conjuntos de nós sobre os quais alguns serviços podem ser colocados.

Por exemplo, se tivermos uma propriedade de nó chamado NodeColor e tivermos três nós:

* Nó 1: NodeColor=Vermelho
* Nó 2: NodeColor=Azul
* Nó 2: NodeColor=Verde

E temos dois serviços:

* Serviço A: com restrição de colocação "Color==Red || Cor==Azul"
* Serviço B: com restrição de colocação "Color==Blue || Cor==Verde"

Por isso, o serviço A pode ser colocado nos nós 1 e 2 e o serviço B pode ser colocado nos nós 2 e 3.

Nesta situação, existe a possibilidade de se fazer um equilíbrio sub-ideal.

O Gestor de Recursos reconhecerá esta situação e elaborará um relatório de saúde aconselhando-o a dividir alguns dos serviços.

Para esta situação, o Gestor de Recursos não é capaz de dar uma proposta de como dividir os serviços, uma vez que podem ser feitas várias divisões e não há forma de estimar qual seria o caminho ideal para dividir os serviços.

## <a name="configuring-subclustering"></a>Subclusão de configuração

O comportamento do Gestor de Recursos sobre a subclustering pode ser modificado modificando os seguintes parâmetros de configuração:
* SubclusteringEnabled - o parâmetro determina se o Gestor de Recursos terá em conta a subclustering ao equilibrar a carga. Se este parâmetro for desligado, o Gestor de Recursos ignorará a subclustering e tentará alcançar o equilíbrio ideal a nível global. O valor predefinido deste parâmetro é falso.
* SubclusteringReportingPolicy - determina como o Gestor de Recursos emitirá relatórios de saúde para subclustering hierárquico e parcial- sobreposição parcial. Um valor de zero significa que os relatórios de saúde sobre subclustering são desligados, "1" significa que os relatórios de saúde de advertência serão produzidos para situações sub-aglomerantes subopítimas e um valor de "2" produzirá relatórios de saúde "OK". O valor predefinido para este parâmetro é "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

através ClusterConfig.jspara implantações autónomas ou Template.jspara aglomerados azure hospedados:

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
* Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre a [carga de equilíbrio](service-fabric-cluster-resource-manager-balancing.md)
* Para saber como os seus serviços podem ser limitados apenas para serem colocados em certos nos acenos ver [propriedades de nó e restrições de colocação](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
