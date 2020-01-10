---
title: Limitação no Gerenciador de recursos de Cluster Service Fabric
description: Saiba como configurar as restrições fornecidas pelo Gerenciador de recursos de Cluster Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452168"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Limitação do Gerenciador de recursos de Cluster Service Fabric
Mesmo que você tenha configurado o Gerenciador de recursos de cluster corretamente, o cluster pode ser interrompido. Por exemplo, pode haver falhas simultâneas de domínio e de nó de falha – o que aconteceria se isso ocorresse durante uma atualização? O Gerenciador de recursos de cluster sempre tenta corrigir tudo, consumindo os recursos do cluster tentando reorganizar e corrigir o cluster. As restrições ajudam a fornecer um Backstop para que o cluster possa usar recursos para estabilizar-os nós voltam, as partições de rede são reparadas, os bits corrigidos são implantados.

Para ajudar com esses tipos de situações, o Gerenciador de recursos de Cluster Service Fabric inclui várias restrições. Essas restrições são muito grandes martelo. Em geral, eles não devem ser alterados sem planejamento e teste cuidadosos.

Se você alterar as restrições do Gerenciador de recursos de cluster, deverá ajustá-las à carga real esperada. Você pode determinar que precisa ter algumas restrições em vigor, mesmo que isso signifique que o cluster leva mais tempo para se estabilizar em algumas situações. O teste é necessário para determinar os valores corretos para restrições. As restrições precisam ser altas o suficiente para permitir que o cluster responda às alterações em um período razoável e baixo o suficiente para realmente impedir o consumo excessivo de recursos. 

Na maior parte do tempo que vimos, os clientes usam os limites porque eles já estavam em um ambiente com restrição de recursos. Alguns exemplos seriam largura de banda de rede limitada para nós individuais ou discos que não são capazes de criar muitas réplicas com estado em paralelo devido a limitações de taxa de transferência. Sem restrições, as operações poderiam sobrecarregar esses recursos, causando a falha ou a lentidão das operações. Nessas situações, os clientes usaram restrições e sabiam que estivessem estendendo a quantidade de tempo que levaria o cluster a alcançar um estado estável. Os clientes também compreenderam que poderiam acabar executando com uma confiabilidade geral mais baixa enquanto foram restringidos.


## <a name="configuring-the-throttles"></a>Configurando as restrições

Service Fabric tem dois mecanismos para limitar o número de movimentações de réplica. O mecanismo padrão que existia antes de Service Fabric 5,7 representa a limitação como um número absoluto de movimentos permitido. Isso não funciona para clusters de todos os tamanhos. Em particular, para clusters grandes, o valor padrão pode ser muito pequeno, diminuindo significativamente o balanceamento, mesmo quando necessário, sem nenhum efeito em clusters menores. Esse mecanismo anterior foi substituído pela limitação baseada em percentual, que escala melhor com clusters dinâmicos nos quais o número de serviços e nós é alterado regularmente.

As restrições são baseadas em uma porcentagem do número de réplicas nos clusters. As restrições baseadas em porcentagem permitem expressar a regra: "não mova mais de 10% das réplicas em um intervalo de 10 minutos", por exemplo.

As definições de configuração para a limitação baseada em porcentagem são:

  - GlobalMovementThrottleThresholdPercentage-número máximo de movimentos permitidos no cluster a qualquer momento, expresso como um percentual do número total de réplicas no cluster. 0 indica nenhum limite. O valor predefinido é 0. Se essa configuração e GlobalMovementThrottleThreshold forem especificadas, o limite mais conservador será usado.
  - GlobalMovementThrottleThresholdPercentageForPlacement-número máximo de movimentações permitidas durante a fase de posicionamento, expressa como porcentagem do número total de réplicas no cluster. 0 indica nenhum limite. O valor predefinido é 0. Se essa configuração e GlobalMovementThrottleThresholdForPlacement forem especificadas, o limite mais conservador será usado.
  - GlobalMovementThrottleThresholdPercentageForBalancing-número máximo de movimentações permitidas durante a fase de balanceamento, expressa como um percentual do número total de réplicas no cluster. 0 indica nenhum limite. O valor predefinido é 0. Se essa configuração e GlobalMovementThrottleThresholdForBalancing forem especificadas, o limite mais conservador será usado.

Ao especificar a porcentagem de limitação, você especificará 5% como 0, 5. O intervalo no qual essas restrições são governadas é o GlobalMovementThrottleCountingInterval, que é especificado em segundos.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

via ClusterConfig. JSON para implantações autônomas ou template. JSON para clusters hospedados do Azure:

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

### <a name="default-count-based-throttles"></a>Limitação com base na contagem padrão
Essas informações são fornecidas caso você tenha clusters mais antigos ou ainda retenha essas configurações em clusters que foram atualizados desde então. Em geral, é recomendável que eles sejam substituídos pelas restrições baseadas em porcentagem acima. Como a limitação baseada em percentual é desabilitada por padrão, essas restrições permanecem como limitadores padrão para um cluster até que sejam desabilitadas e substituídas pelas restrições baseadas em porcentagem. 

  - GlobalMovementThrottleThreshold – essa configuração controla o número total de movimentos no cluster ao longo de um tempo. A quantidade de tempo é especificada em segundos como o GlobalMovementThrottleCountingInterval. O valor padrão para GlobalMovementThrottleThreshold é 1000 e o valor padrão para GlobalMovementThrottleCountingInterval é 600.
  - MovementPerPartitionThrottleThreshold – essa configuração controla o número total de movimentos para qualquer partição de serviço ao longo de um tempo. A quantidade de tempo é especificada em segundos como o MovementPerPartitionThrottleCountingInterval. O valor padrão para MovementPerPartitionThrottleThreshold é 50 e o valor padrão para MovementPerPartitionThrottleCountingInterval é 600.

A configuração para essas restrições segue o mesmo padrão que a limitação baseada em porcentagem.

## <a name="next-steps"></a>Passos seguintes
- Para saber como o Gerenciador de recursos de cluster gerencia e equilibra a carga no cluster, confira o artigo sobre [balanceamento](service-fabric-cluster-resource-manager-balancing.md) de carga
- O Gerenciador de recursos de cluster tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre como [descrever um cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
