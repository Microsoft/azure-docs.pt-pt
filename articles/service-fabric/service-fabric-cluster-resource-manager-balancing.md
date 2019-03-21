---
title: Equilibrar o seu cluster do Azure Service Fabric | Documentos da Microsoft
description: Uma introdução ao seu cluster com o Service Fabric Resource Manager de Cluster de balanceamento.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9a124bd9a52e22c359fb771e4d4c8714bd1dbe2c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123237"
---
# <a name="balancing-your-service-fabric-cluster"></a>Balanceamento do seu cluster do service fabric
O Gestor de recursos de Cluster do Service Fabric suporta alterações de carga dinâmico, reagir a adições ou remoções de nós ou serviços. Também automaticamente corrige violações de restrição e rebalances proativamente o cluster. Mas quantas vezes essas ações direcionadas, e o que aciona-los?

Existem três categorias diferentes de trabalho que executa o Gestor de recursos do Cluster. São:

1. Colocação – neste estágio lida com colocar qualquer réplicas com monitoração de estado ou instâncias sem monitoração de estado que estão em falta. A colocação inclui novos serviços e lidar com réplicas com monitoração de estado ou instâncias sem monitoração de estado com falhas. A eliminar e soltando réplicas ou instâncias são manipulados aqui.
2. Restrição verifica – neste estágio verifica e corrige violações diferentes restrições de posicionamento (regras) no sistema. Exemplos de regras são coisas como garantir que nós não estão acima da capacidade e que as restrições de posicionamento de um serviço são cumpridas.
3. Balanceamento – neste estágio verifica para ver se o reequilíbrio é necessário com base no nível pretendido configurado de saldo para métricas diferentes. Se assim for tenta localizar uma organização do cluster que mais é balanceado.

## <a name="configuring-cluster-resource-manager-timers"></a>Configurar os temporizadores de Gestor de recursos de Cluster
O primeiro conjunto de controlos em balanceamento são um conjunto de temporizadores. Desses temporizadores regem a frequência com que o Gestor de recursos de Cluster examina o cluster e executa as ações corretivas.

Cada um desses tipos diferentes de correções pode fazer com que o Gestor de recursos do Cluster é controlada por um temporizador diferente que controla a frequência. Quando cada temporizador é disparado, a tarefa está agendada. Por padrão, o Gestor de recursos:

* verifica o estado e aplica as atualizações (como gravação de um nó estiver em baixo) cada 1/10 de um segundo
* Define o sinalizador de verificação de colocação 
* Define o sinalizador de verificação de restrição cada segundo
* Define o sinalizador balanceamento a cada cinco segundos.

São exemplos de configuração que regem desses temporizadores abaixo:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

por meio de ClusterConfig.json das implementações autónomas ou Template para o Azure alojados clusters:

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

Hoje mesmo o Gestor de recursos de Cluster apenas realiza uma destas ações por vez, em seqüência. Este é o motivo pelo qual nos Referimos desses temporizadores como "intervalos mínimo" e as ações que serão direcionadas quando os temporizadores deixar como "sinalizadores de configuração". Por exemplo, o Gestor de recursos de Cluster assume o controlo de pedidos pendentes para criar serviços antes do cluster de balanceamento. Como pode ver, os intervalos de tempo predefinido especificados, o Gestor de recursos de Cluster verifica a existência de tudo o que necessita para fazer com frequência. Normalmente, isso significa que o conjunto de alterações feitas durante cada passo é pequeno. Fazer pequenas alterações com frequência, permite que o Gestor de recursos do Cluster seja reativo, quando as coisas acontecem no cluster. Os temporizadores de predefinição fornecem algum processamento em lote, uma vez que muitos dos mesmos tipos de eventos tendem a ocorrem em simultâneo. 

Por exemplo, quando nós falharem eles podem fazer por isso, todo domínios de falha de cada vez. Todas estas falhas são capturadas durante o próximo estado atualizar depois do *PLBRefreshGap*. As correções são determinadas durante a colocação seguinte, a verificação de restrição, e executa o balanceamento. Por predefinição o Gestor de recursos de Cluster não é examinar as horas das alterações no cluster e tentou abordar todas as alterações ao mesmo tempo. Se o fizer, poderia levar a aumentos repentinos de alterações.

Além disso, o Gestor de recursos do Cluster tem algumas informações adicionais para determinar se o cluster desequilibrado. Para isso, temos duas outras partes da configuração: *BalancingThresholds* e *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Balanceamento de limiares
Um limiar de balanceamento é o principal controle para acionar o reequilíbrio. O limite de balanceamento de mensagens em fila para uma métrica é um _proporção_. Se a carga para uma métrica no nó mais carregado dividido pela quantidade de carga no nó menos carregado excede essa métrica *BalancingThreshold*, em seguida, o cluster está desequilibrado. Como resultado balanceamento é acionado na próxima vez que verifica o Gestor de recursos do Cluster. O *MinLoadBalancingInterval* temporizador define a frequência com que o Gestor de recursos de Cluster deve verificar se o reequilíbrio é necessário. A verificação não significa nada acontece. 

Balanceamento de limiares são definidas numa base por métrica como parte da definição do cluster. Para obter mais informações sobre as métricas, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

por meio de ClusterConfig.json das implementações autónomas ou Template para o Azure alojados clusters:

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

![Balanceamento de limiar de exemplo][Image1]
</center>

Neste exemplo, cada serviço está a consumir uma unidade de alguns métrica. No exemplo superior, a carga máxima num nó é cinco e o mínimo é dois. Digamos que o limiar do balanceamento desta métrica é três. Uma vez que a proporção do cluster é 5/2 = 2,5 e isto é menor do que o especificado balanceamento de limiar de três, o cluster é balanceado. Sem balanceamento é acionado quando verifica o Gestor de recursos do Cluster.

No exemplo na parte inferior, a carga máxima num nó é 10, enquanto o mínimo é duas, resultando numa proporção de cinco. Cinco é superior ao limiar designado de balanceamento de três para essa métrica. Como resultado, um reequilibrar execute será agendada a próxima vez, o balanceamento timer é acionado. Numa situação como essa alguma carga normalmente é distribuída para Nó3. Uma vez que o Gestor de recursos de Cluster do Service Fabric não usa uma abordagem greedy, alguns carga também pode ser distribuída para Node2. 

<center>

![Balanceamento de ações de exemplo do limiar][Image2]
</center>

> [!NOTE]
> "Balanceamento" lida com duas estratégias diferentes para gerir a carga no seu cluster. A estratégia de predefinição que utiliza o Gestor de recursos do Cluster é distribuir carga entre os nós do cluster. É a estratégia de outra [desfragmentação](service-fabric-cluster-resource-manager-defragmentation-metrics.md). A desfragmentação é executada durante o balanceamento mesmo executar. As estratégias de balanceamento e desfragmentação podem ser utilizadas para métricas diferentes do mesmo cluster. Um serviço pode ter balanceamento e desfragmentação de métricas. Para métricas de desfragmentação, a proporção entre as cargas do cluster aciona reequilibrar quando estiver _abaixo_ o limiar balanceamento. 
>

Obter abaixo do limiar balanceamento não é um objetivo explícito. Balanceamento de limiares são apenas uma *acionador*. Quando o balanceamento de execuções, o Gestor de recursos de Cluster determina que aprimoramentos pode tornar a, se houver. Só porque uma pesquisa balanceamento é iniciada não significa que nada move. Por vezes, o cluster é desequilibrado mas demasiado restrita para corrigir. Em alternativa, os aprimoramentos necessitam de movimentos que são demasiado [dispendiosa](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Limiares de atividade
Às vezes, apesar dos nós são relativamente desequilibrados, o *total* quantidade de carga no cluster é baixa. A falta de carga pode ser um dip transitório ou porque o cluster é novo e apenas começando reiniciada. Em ambos os casos, poderá não quer perder tempo o cluster de balanceamento porque não há muito a ser obtida. Se o cluster passaram por balanceamento, seria gastar a rede e recursos de computação para mover as coisas sem fazer qualquer grande *absoluto* diferença. Para evitar desnecessárias for movido, há outro controle conhecido como limiares de atividade. Limiares de atividade permite-lhe especificar algum limite inferior absoluto para a atividade. Se nenhum nó está acima deste limiar, balanceamento não é acionado, mesmo que o limiar balanceamento é cumprido.

Digamos que podemos manter nosso limiar de balanceamento de três para esta métrica. Vamos também supor que temos um limiar de atividade de 1536. No primeiro caso, enquanto o cluster está desequilibrado pelo limiar de balanceamento de mensagens em fila não existe nenhum nó atende a esse limite de atividade, portanto, nada acontece. No exemplo na parte inferior, Node1 é superior ao limiar de atividade. Uma vez que são excedido o limiar de balanceamento e o limite de atividade para a métrica, balanceamento está agendado. Por exemplo, vamos examinar o diagrama seguinte: 

<center>

![Exemplo de limiar de atividade][Image3]
</center>

Assim como balanceamento limiares, limiares de atividade são definido por-métrica por meio da definição do cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

por meio de ClusterConfig.json das implementações autónomas ou Template para o Azure alojados clusters:

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

Limiares de balanceamento e a atividade são ambos vinculado a uma métrica específica - balanceamento é acionado apenas se o limiar de balanceamento e a atividade limiar foi excedido para a mesma métrica.

> [!NOTE]
> Quando não especificado, o limite de balanceamento de mensagens em fila para uma métrica é 1 e o limite de atividade é 0. Isso significa que o Gestor de recursos do Cluster irá tentar manter essa métrica perfeitamente equilibrada para qualquer carga de determinado. Se estiver a utilizar métricas personalizadas é recomendado que defina explicitamente o seu próprio limiares balanceamento e a atividade para as suas métricas. 
>

## <a name="balancing-services-together"></a>Serviços de balanceamento em conjunto
Se o cluster é desequilibrado ou não é uma decisão de todo o cluster. No entanto, a forma como podemos corrigi-lo é mover as réplicas do serviço individual e instâncias em torno. Isso faz sentido, certo? Se a memória é empilhada a cópia de segurança num nó, várias réplicas ou instâncias podem estar contribuindo para ele. Corrigir o desequilíbrio pode exigir a mover qualquer um dos réplicas com monitoração de estado ou instâncias sem monitoração de estado que utilizam a métrica desequilibrada.

Ocasionalmente, no entanto, um serviço que não era o próprio desequilibrado obtém movido (Lembre-se a discussão de locais e globais pesos anteriormente). Por que seria um serviço é movido quando tudo o que eram com balanceamento de métricas do serviço? Vejamos um exemplo:

- Digamos que existem quatro serviços, Service1, Service2, Service3 e Service4. 
- Service1 relatórios de métricas Metric1 e Metric2. 
- Service2 relatórios de métricas Metric2 e Metric3. 
- Service3 relatórios de métricas Metric3 e Metric4.
- Os relatórios de Service4 Metric99 métrica. 

Obviamente, pode ver em que vamos aqui: Há uma cadeia! Não temos realmente quatro serviços independentes, temos três serviços que estão relacionados e outro que está desativada por conta própria.

<center>

![Serviços de balanceamento em conjunto][Image4]
</center>

Devido a esta cadeia, é possível que um desequilíbrio nas métricas de 1 a 4 pode causar réplicas ou de instâncias que pertencem aos serviços de 1 a 3 para mover-se. Também Sabemos que um desequilíbrio nas métricas 1, 2 ou 3 não pode fazer com que movimentos de Service4. Não haveria nenhum ponto desde que migrou as réplicas ou instâncias que pertencem a Service4 em torno de fazer absolutamente nada para afetar o saldo das métricas de 1 a 3.

O Gestor de recursos de Cluster detecta automaticamente os serviços que estão relacionados. Adicionar, remover ou alterar as métricas para os serviços pode afetar suas relações. Por exemplo, entre as duas execuções do balanceamento Service2 a podem ter sido atualizadas para remover Metric2. Isso divide a cadeia entre Service1 e Service2. Agora, em vez de dois grupos de serviços relacionados, há três:

<center>

![Serviços de balanceamento em conjunto][Image5]
</center>

## <a name="next-steps"></a>Passos Seguintes
* As métricas são como o Service Fabric Cluster Resource Manager gere o consumo e a capacidade do cluster. Para saber mais sobre métricas e como configurá-las, confira [neste artigo](service-fabric-cluster-resource-manager-metrics.md)
* O custo de movimento é uma maneira de sinalização o Gestor de recursos do Cluster que determinados serviços são mais caros mover-se que outros. Para mais informações sobre o custo de movimento, consulte [neste artigo](service-fabric-cluster-resource-manager-movement-cost.md)
* O Gestor de recursos do Cluster tem várias limitações que pode configurar para diminuir o volume de alterações no cluster. Eles não são normalmente necessários, mas se precisar deles pode saber mais sobre-los [aqui](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
