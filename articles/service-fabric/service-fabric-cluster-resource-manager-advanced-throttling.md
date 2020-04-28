---
title: Estrangulamento no gestor de recursos de cluster de tecido de serviço
description: Aprenda a configurar os aceleradores fornecidos pelo Gestor de Recursos de Cluster de Tecidos de Serviço.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75452168"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Estrangular o Gestor de Recursos de Cluster de Tecidos de Serviço
Mesmo que tenha configurado corretamente o Cluster Resource Manager, o cluster pode ser interrompido. Por exemplo, poderia haver falhas simultâneas de nó e de domínio de avaria - o que aconteceria se isso acontecesse durante uma atualização? O Cluster Resource Manager tenta sempre corrigir tudo, consumindo os recursos do cluster tentando reorganizar e corrigir o cluster. Os aceleradores ajudam a fornecer um backstop para que o cluster possa usar recursos para estabilizar - os nós voltam, as divisórias da rede curam, as partes corrigidas são implantadas.

Para ajudar neste tipo de situações, o Gestor de Recursos de Cluster de Tecidos de Serviço inclui vários aceleradores. Estes aceleradores são todos martelos bastante grandes. Geralmente não devem ser mudados sem um planeamento e testes cuidadosos.

Se alterar os aceleradores do Cluster Resource Manager, deve ajustá-los à carga real esperada. Pode determinar que precisa de alguns aceleradores no lugar, mesmo que isso signifique que o cluster demora mais tempo a estabilizar em algumas situações. É necessário testar para determinar os valores corretos para os aceleradores. Os aceleradores têm de ser altos o suficiente para permitir que o cluster responda a alterações num período de tempo razoável, e suficientemente baixo para evitar demasiado consumo de recursos. 

A maior parte do tempo vimos os clientes usarem aceleradores, tem sido porque já estavam num ambiente restrito de recursos. Alguns exemplos seriam largura de banda de rede limitada para nós individuais, ou discos que não são capazes de construir muitas réplicas estatais em paralelo devido a limitações de entrada. Sem acelerações, as operações poderiam sobrecarregar estes recursos, fazendo com que as operações falhassem ou fossem lentas. Nestas situações, os clientes usavam aceleradores e sabiam que estavam a prolongar o tempo que o cluster levaria a atingir um estado estável. Os clientes também entenderam que poderiam acabar por funcionar com uma menor fiabilidade global enquanto eram estrangulados.


## <a name="configuring-the-throttles"></a>Configurar os aceleradores

O Tecido de Serviço tem dois mecanismos para estrangular o número de movimentos de réplica. O mecanismo predefinido que existia antes do Tecido de Serviço 5.7 representa a aceleração como um número absoluto de movimentos permitidos. Isto não funciona para aglomerados de todos os tamanhos. Em particular, para os grandes aglomerados, o valor predefinido pode ser demasiado pequeno, abrandando significativamente o equilíbrio mesmo quando é necessário, sem ter qualquer efeito em clusters menores. Este mecanismo anterior foi substituído por estrangulamento sucuviado por percentagem, o que se baseia melhor em clusters dinâmicos em que o número de serviços e nós muda regularmente.

Os aceleradores baseiam-se numa percentagem do número de réplicas nos clusters. Os aceleradores baseados em percentagem permitem expressar a regra: "não se mover mais de 10% das réplicas num intervalo de 10 minutos", por exemplo.

As definições de configuração para estrangulamento baseado em percentagem são:

  - GlobalMovementThrottleThresholdPercentage - Número máximo de movimentos permitidos em cluster a qualquer momento, expressos em percentagem do número total de réplicas no cluster. 0 indica que não há limite. O valor predefinido é 0. Se esta definição e o GlobalMovementThrottleThreshold forem especificados, então o limite mais conservador é usado.
  - GlobalMovementThrottleThresholdPercentageForPlacement - Número máximo de movimentos permitidos durante a fase de colocação, expressos em percentagem do número total de réplicas no cluster. 0 indica que não há limite. O valor predefinido é 0. Se esta definição e o GlobalMovementThrottleThresholdForPlacement forem especificados, então o limite mais conservador é usado.
  - GlobalMovementThrottleThresholdPercentageForBalance - Número máximo de movimentos permitidos durante a fase de equilíbrio, expressos em percentagem do número total de réplicas no cluster. 0 indica que não há limite. O valor predefinido é 0. Se esta definição e o GlobalMovementThrottleThresholdForBalancesão forem especificados, então o limite mais conservador é usado.

Ao especificar a percentagem do acelerador, especifice 5% como 0,05. O intervalo em que estes aceleradores são regidos é o GlobalMovementThrottleCountingInterval, que é especificado em segundos.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

via ClusterConfig.json para implantações autónomas ou template.json para clusters alojados em Azure:

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

### <a name="default-count-based-throttles"></a>Aceleração baseada na contagem padrão
Estas informações são fornecidas no caso de ter clusters mais antigos ou ainda manter estas configurações em clusters que foram entretanto atualizados. Em geral, recomenda-se que estes sejam substituídos pelos aceleradores percentuais acima. Uma vez que o estrangulamento baseado em percentagem é desativado por padrão, estes aceleradores permanecem os aceleradores padrão para um cluster até que sejam desativados e substituídos pelos aceleradores percentuais. 

  - GlobalMovementThrottleThreshold – esta definição controla o número total de movimentos no cluster ao longo de algum tempo. A quantidade de tempo é especificada em segundos como o GlobalMovementThrottleCountingInterval. O valor padrão para o GlobalMovementThrottleThreshold é de 1000 e o valor padrão para o GlobalMovementThrottleCountingInterval é de 600.
  - MovementPerPartitionThrottleThreshold – esta definição controla o número total de movimentos para qualquer partição de serviço ao longo de algum tempo. A quantidade de tempo é especificada em segundos como o MovementPerPartitionThrottleCountingInterval. O valor padrão para o MovementPerPartitionThrottleThreshold é de 50 e o valor padrão para o MovementPerPartitionThrottleCountingInterval é de 600.

A configuração para estes aceleradores segue o mesmo padrão que a aceleração baseada em percentagem.

## <a name="next-steps"></a>Passos seguintes
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre [a carga](service-fabric-cluster-resource-manager-balancing.md) de equilíbrio
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](service-fabric-cluster-resource-manager-cluster-description.md)
