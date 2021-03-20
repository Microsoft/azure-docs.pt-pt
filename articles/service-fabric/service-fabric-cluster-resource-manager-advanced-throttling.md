---
title: Estrangulamento no gestor de recursos do cluster de tecido de serviço
description: Aprenda a configurar os aceleradores fornecidos pelo Service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75452168"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Throttling o Gestor de Recursos do Cluster de Tecido de Serviço
Mesmo que tenha configurado corretamente o Cluster Resource Manager, o cluster pode ser interrompido. Por exemplo, poderia haver falhas simultâneas de node e de domínio de falhas - o que aconteceria se isso ocorresse durante uma atualização? O Cluster Resource Manager tenta sempre corrigir tudo, consumindo os recursos do cluster tentando reorganizar e corrigir o cluster. Os aceleradores ajudam a fornecer um backstop para que o cluster possa usar recursos para estabilizar - os nós voltam, as divisórias de rede curam-se, os bits corrigidos são implantados.

Para ajudar neste tipo de situações, o Service Fabric Cluster Resource Manager inclui vários aceleradores. Estes aceleradores são todos martelos bastante grandes. Geralmente não devem ser alterados sem um planeamento e testes cuidadosos.

Se alterar os aceleradores do Cluster Resource Manager, deve ajustá-los à carga real esperada. Pode determinar que precisa de ter alguns aceleradores no lugar, mesmo que isso signifique que o cluster demora mais tempo a estabilizar em algumas situações. São necessários testes para determinar os valores corretos para os aceleradores. Os aceleradores precisam de ser suficientemente altos para permitir que o cluster responda a alterações num período de tempo razoável, e baixo o suficiente para realmente prevenir demasiado consumo de recursos. 

A maior parte do tempo que vimos os clientes usarem aceleradores tem sido porque já estavam num ambiente restrito de recursos. Alguns exemplos seriam largura de banda de rede limitada para nós individuais, ou discos que não são capazes de construir muitas réplicas imponentes em paralelo devido a limitações de produção. Sem acelerações, as operações poderiam sobrecarregar estes recursos, fazendo com que as operações falhassem ou fossem lentas. Nestas situações, os clientes usavam aceleradores e sabiam que estavam a prolongar o tempo que o cluster levaria para chegar a um estado estável. Os clientes também entenderam que poderiam acabar por funcionar com uma menor fiabilidade global enquanto eram estrangulados.


## <a name="configuring-the-throttles"></a>Configurar os aceleradores

O Tecido de Serviço tem dois mecanismos para estrangular o número de movimentos de réplica. O mecanismo predefinido que existia antes do Tecido de Serviço 5.7 representa o estrangulamento como um número absoluto de movimentos permitidos. Isto não funciona para aglomerados de todos os tamanhos. Em particular, para os grandes clusters, o valor padrão pode ser demasiado pequeno, abrandando significativamente o equilíbrio mesmo quando necessário, sem qualquer efeito em aglomerados menores. Este mecanismo anterior foi substituído por estrangulamentos percentuais, que escala melhor com clusters dinâmicos em que o número de serviços e nós muda regularmente.

Os aceleradores baseiam-se numa percentagem do número de réplicas nos clusters. Aceleradores percentuais permitem expressar a regra: "não mova mais de 10% das réplicas num intervalo de 10 minutos", por exemplo.

As definições de configuração para estrangulamento por percentagem são:

  - GlobalMovementThrottleThresholdPercentage - Número máximo de movimentos permitidos em cluster a qualquer momento, expressos em percentagem do número total de réplicas no cluster. 0 indica que não há limite. O valor predefinido é 0. Se esta definição e GlobalMovementThrottleThreshold forem especificados, então o limite mais conservador é usado.
  - GlobalMovementThrottleThresholdPercentageForPlacement - Número máximo de movimentos permitidos durante a fase de colocação, expressos em percentagem do número total de réplicas no cluster. 0 indica que não há limite. O valor predefinido é 0. Se esta definição e GlobalMovementThrottleThresholdForPlacement forem especificados, então o limite mais conservador é usado.
  - GlobalMovementThrottleThresholdPercentageForBalancing - Número máximo de movimentos permitidos durante a fase de equilíbrio, expressos em percentagem do número total de réplicas no cluster. 0 indica que não há limite. O valor predefinido é 0. Se esta definição e GlobalMovementThrottleThresholdForBalancing forem especificados, então o limite mais conservador é usado.

Ao especificar a percentagem do acelerador, especificaria 5% como 0,05. O intervalo em que estes aceleradores são governados é o GlobalMovementThrottleCountingInterval, que é especificado em segundos.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

através ClusterConfig.jspara implantações autónomas ou Template.jspara aglomerados azure hospedados:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Aceleradores baseados na contagem padrão
Estas informações são fornecidas no caso de ter clusters mais antigos ou ainda reter estas configurações em clusters que foram entretanto atualizados. Em geral, recomenda-se que estes sejam substituídos pelos aceleradores baseados em percentagens acima. Uma vez que o estrangulamento por percentagem é desativado por defeito, estes aceleradores continuam a ser os aceleradores predefinidos para um cluster até serem desativados e substituídos pelos aceleradores baseados em percentagens. 

  - GlobalMovementThrottleThreshold – esta definição controla o número total de movimentos no cluster ao longo de algum tempo. O tempo é especificado em segundos como GlobalMovementThrottleCountingInterval. O valor padrão para o GlobalMovementThrottleThreshold é de 1000 e o valor padrão para o GlobalMovementThrottleCountingInterval é de 600.
  - MovementPerPartitionThrottleThreshold – esta definição controla o número total de movimentos para qualquer divisória de serviço ao longo de algum tempo. O tempo é especificado em segundos como MovementPerPartitionThrottleCountingInterval. O valor predefinido para o MovementPerPartitionThrottleThreshold é de 50 e o valor padrão para o MovementPerPartitionThrottleCountingInterval é de 600.

A configuração para estes aceleradores segue o mesmo padrão que o estrangulamento baseado em percentagem.

## <a name="next-steps"></a>Passos seguintes
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre a [carga de equilíbrio](service-fabric-cluster-resource-manager-balancing.md)
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](service-fabric-cluster-resource-manager-cluster-description.md)
