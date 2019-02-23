---
title: Limitação no resource manager de cluster do Service Fabric | Documentos da Microsoft
description: Saiba como configurar os limitadores fornecidos pelo Service Fabric Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b1824fc5f93bafb26650d28277054869a3659099
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731757"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>O Gestor de recursos de Cluster do Service Fabric de limitação
Mesmo se tiver configurado o Gestor de recursos de Cluster corretamente, o cluster pode obter interrompido. Por exemplo, pode haver falhas simultâneas de nó e com falha domínio - o que aconteceria se de que ocorreu durante uma atualização? O Gestor de recursos de Cluster sempre tenta corrigir tudo, consumindo recursos do cluster tentando reorganizar e corrigir o cluster. Limitações do ajudar a fornecer uma opção para que o cluster pode utilizar os recursos para estabilizar - os nós voltam atrás, as partições de rede tratá-lo, bits corrigidos implementados.

Para ajudar com esses tipos de situações, o Gestor de recursos de Cluster do Service Fabric inclui várias limitações. Essas limitações são todos os hammers razoavelmente grandes. Em geral, não deve ser alterados sem planejamento e testes cuidadosos.

Se alterar as limitações do Gestor de recursos de Cluster, deve ajustá-los para a carga real esperada. Pode determinar que tem de ter algumas limitações no local, mesmo que isso que o cluster demora mais tempo para estabilizar em algumas situações. O teste é necessária para determinar os valores corretos para limitações. Limitações do tem de ser alto o suficiente para permitir que o cluster responder a alterações num período de tempo razoável e baixas o suficiente para realmente evitar muita consumo de recursos. 

Na maioria das vezes, vimos os clientes utiliza limitadores foi porque já estavam num ambiente restrito de recursos. Alguns exemplos seriam largura de banda de rede limitada para nós individuais ou discos que não são capazes de criar muitas réplicas com monitoração de estado em paralelo devido a limitações de débito. Sem limitações, operações poderiam sobrecarregar estes recursos, fazendo com que operações para fazer a ativação ou lenta. Nestas situações, os clientes utilizado limitadores e sabiam que estavam estendendo a quantidade de tempo que demoraria o cluster para atingir um estado estável. Os clientes também compreendi poderia acabar em execução na fiabilidade geral inferior enquanto eles eram limitados.


## <a name="configuring-the-throttles"></a>Configurar os limitadores

Service Fabric possui dois mecanismos para a limitação do número de movimentos de réplica. O mecanismo de predefinição que existiam antes 5.7 do Service Fabric representa a limitação como um número absoluto de movimentações permitido. Isso não funciona para os clusters de todos os tamanhos. Em particular, para grandes clusters o valor predefinido pode ser demasiado pequeno, significativamente mais lentos balanceamento mesmo quando é necessário, enquanto não tem nenhum efeito em clusters de menores. Esse mecanismo anterior foi substituído pela limitação de baseado em percentagem, que dimensiona melhor com clusters dinâmicos, na qual o número de serviços e nós alterar regularmente.

Os limitadores baseiam-se uma percentagem do número de réplicas nos clusters. Limitações do baseada em porcentagem ativar expressar a regra: "não são movidos mais de 10% das réplicas num intervalo de 10 minutos", por exemplo.

As definições de configuração para a limitação com base na percentagem de são:

  - GlobalMovementThrottleThresholdPercentage - o número máximo de movimentos permitido no cluster em qualquer altura, expresso como percentagem do número total de réplicas do cluster. 0 indica sem limite. O valor predefinido é 0. Se esta definição e o GlobalMovementThrottleThreshold forem especificados, o limite mais Conservadora é utilizado.
  - GlobalMovementThrottleThresholdPercentageForPlacement - número máximo de movimentos permitido durante a fase de colocação, expresso como percentagem do número total de réplicas do cluster. 0 indica sem limite. O valor predefinido é 0. Se esta definição e o GlobalMovementThrottleThresholdForPlacement forem especificados, o limite mais Conservadora é utilizado.
  - GlobalMovementThrottleThresholdPercentageForBalancing - número máximo de movimentos permitida durante a fase de balanceamento, expresso como percentagem do número total de réplicas do cluster. 0 indica sem limite. O valor predefinido é 0. Se esta definição e o GlobalMovementThrottleThresholdForBalancing forem especificados, o limite mais Conservadora é utilizado.

Ao especificar a percentagem de limitação, especifique 5% como 0,05. O intervalo no qual essas limitações são regidas é GlobalMovementThrottleCountingInterval, o que é especificada em segundos.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

por meio de ClusterConfig.json das implementações autónomas ou Template para o Azure alojados clusters:

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

### <a name="default-count-based-throttles"></a>Limitações de contagem com base do padrão
Estas informações são fornecidas no caso de ter clusters mais antigos ou continua a ter estas configurações nos clusters do que uma vez que foram atualizados. Em geral, recomenda-se que estes são substituídos pelos limitadores baseado em percentagem acima. Uma vez que a limitação com base em percentagem está desativada por predefinição, estas limitações permanecem os limitadores predefinido para um cluster até que eles são desativados e substituídos pelos limitadores baseado em percentagem. 

  - GlobalMovementThrottleThreshold – esta definição controla o número total de movimentos no cluster através de algum tempo. A quantidade de tempo é especificada em segundos, como o GlobalMovementThrottleCountingInterval. O valor predefinido para o GlobalMovementThrottleThreshold é 1000, sendo o valor predefinido para o GlobalMovementThrottleCountingInterval 600.
  - MovementPerPartitionThrottleThreshold – esta definição controla o número total de movimentos para qualquer partição de serviço através de algum tempo. A quantidade de tempo é especificada em segundos, como o MovementPerPartitionThrottleCountingInterval. O valor predefinido para o MovementPerPartitionThrottleThreshold é 50 e o valor predefinido para o MovementPerPartitionThrottleCountingInterval é 600.

A configuração para estas limitações segue o mesmo padrão de limitação com base em percentagem.

## <a name="next-steps"></a>Passos Seguintes
- Para obter informações sobre como o Gestor de recursos de Cluster gere e faz o balanceamento de carga no cluster, consulte o artigo no [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- O Gestor de recursos do Cluster tem muitas opções para descrever o cluster. Para obter mais informações sobre ele, visite este artigo no [descrever um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
