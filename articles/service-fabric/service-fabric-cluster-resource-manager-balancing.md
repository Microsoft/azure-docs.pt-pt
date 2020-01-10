---
title: Equilibre o cluster de Service Fabric do Azure
description: Uma introdução ao balanceamento de seu cluster com o Service Fabric Gerenciador de recursos de cluster.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8e170c27923d2bb091c4121e350809b85e4c48a5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452092"
---
# <a name="balancing-your-service-fabric-cluster"></a>Equilibrando o cluster do Service Fabric
O Service Fabric cluster Resource Manager dá suporte a alterações de carga dinâmica, reagindo a adições ou remoções de nós ou serviços. Ele também corrige automaticamente violações de restrição e reequilibra o cluster de forma proativa. Mas com que frequência essas ações são executadas e o que as dispara?

Há três categorias diferentes de trabalho que o Gerenciador de recursos de cluster executa. São:

1. Posicionamento – esse estágio lida com o posicionamento de réplicas com estado ou instâncias sem estado que estão ausentes. O posicionamento inclui novos serviços e manipulação de réplicas com estado ou instâncias sem estado que falharam. A exclusão e o descarte de réplicas ou instâncias são tratados aqui.
2. Verificações de restrição – esse estágio verifica e corrige violações das diferentes restrições de posicionamento (regras) no sistema. Exemplos de regras são coisas como garantir que os nós não estejam acima da capacidade e que as restrições de posicionamento de um serviço sejam atendidas.
3. Balanceamento – esse estágio verifica se o rebalanceamento é necessário com base no nível de saldo desejado configurado para métricas diferentes. Se estiver, ele tentará encontrar uma organização no cluster que seja mais equilibrada.

## <a name="configuring-cluster-resource-manager-timers"></a>Configurando temporizadores do Gerenciador de recursos de cluster
O primeiro conjunto de controles em volta do balanceamento é um conjunto de temporizadores. Esses temporizadores regem a frequência com que o Gerenciador de recursos de cluster examina o cluster e executa ações corretivas.

Cada um desses tipos diferentes de correções que o Gerenciador de recursos de cluster pode fazer é controlado por um temporizador diferente que governa sua frequência. Quando cada temporizador é acionado, a tarefa é agendada. Por padrão, o Gerenciador de recursos:

* verifica seu estado e aplica atualizações (como a gravação de que um nó está inoperante) a cada 1/10º de um segundo
* define o sinalizador de verificação de posicionamento a cada segundo
* define o sinalizador de verificação de restrição a cada segundo
* define o sinalizador de balanceamento a cada cinco segundos

Os exemplos da configuração que regem esses temporizadores estão abaixo:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

via ClusterConfig. JSON para implantações autônomas ou template. JSON para clusters hospedados do Azure:

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

Hoje, o Gerenciador de recursos de cluster executa apenas uma dessas ações de cada vez, sequencialmente. É por isso que nos referimos a esses temporizadores como "intervalos mínimos" e as ações que são tomadas quando os temporizadores são desligados como "sinalizadores de configuração". Por exemplo, o Gerenciador de recursos de cluster cuida de solicitações pendentes para criar serviços antes de balancear o cluster. Como você pode ver com os intervalos de tempo padrão especificados, o Gerenciador de recursos de cluster verifica o que for necessário para fazer com frequência. Normalmente, isso significa que o conjunto de alterações feitas durante cada etapa é pequeno. Fazer pequenas alterações frequentemente permite que o Gerenciador de recursos de cluster responda quando as coisas acontecem no cluster. Os temporizadores padrão fornecem algum envio em lote, já que muitos dos mesmos tipos de eventos tendem a ocorrer simultaneamente. 

Por exemplo, quando os nós falham, eles podem fazer todo domínio de falha por vez. Todas essas falhas são capturadas durante a próxima atualização de estado após o *PLBRefreshGap*. As correções são determinadas durante o posicionamento, verificação de restrição e execuções de balanceamento a seguir. Por padrão, o Gerenciador de recursos de cluster não está examinando horas de alterações no cluster e tentando resolver todas as alterações ao mesmo tempo. Isso levaria a picos de rotatividade.

O Gerenciador de recursos de cluster também precisa de algumas informações adicionais para determinar se o cluster está desbalanceado. Para isso, temos duas outras partes de configuração: *limites* e *limites*.

## <a name="balancing-thresholds"></a>Limites de balanceamento
Um limite de balanceamento é o controle principal para disparar o rebalanceamento. O limite de balanceamento para uma métrica é uma _taxa_. Se a carga de uma métrica no nó mais carregado dividido pela quantidade de carga no nó menos carregado exceder o *limite*da métrica, o cluster será desequilibrado. Como um balanceamento de resultado é disparado na próxima vez que o Gerenciador de recursos de cluster verifica. O temporizador *MinLoadBalancingInterval* define com que frequência o Gerenciador de recursos de cluster deve verificar se o rebalanceamento é necessário. A verificação não significa que nada aconteça. 

Os limites de balanceamento são definidos em uma base por métrica como parte da definição do cluster. Para obter mais informações sobre métricas, confira [Este artigo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

via ClusterConfig. JSON para implantações autônomas ou template. JSON para clusters hospedados do Azure:

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

Exemplo de limite de balanceamento de ![][Image1]
</center>

Neste exemplo, cada serviço está consumindo uma unidade de alguma métrica. No exemplo superior, a carga máxima em um nó é cinco e o mínimo é dois. Digamos que o limite de balanceamento para essa métrica seja três. Como a taxa no cluster é 5/2 = 2,5 e é menor que o limite de balanceamento especificado de três, o cluster é balanceado. Nenhum balanceamento é disparado quando o Gerenciador de recursos de cluster verifica.

No exemplo inferior, a carga máxima em um nó é 10, enquanto o mínimo é dois, resultando em uma taxa de cinco. Cinco é maior que o limite de balanceamento designado de três para essa métrica. Como resultado, uma execução de rebalanceamento será agendada na próxima vez que o temporizador de balanceamento for acionado. Em uma situação como essa, alguma carga é normalmente distribuída para o Node3. Como o Gerenciador de recursos de Cluster Service Fabric não usa uma abordagem de ávido, alguma carga também pode ser distribuída para NODE2. 

<center>

Ações de exemplo de limite de balanceamento de ![][Image2]
</center>

> [!NOTE]
> "Balanceamento" trata de duas estratégias diferentes para gerenciar a carga em seu cluster. A estratégia padrão que o Gerenciador de recursos de cluster usa é distribuir a carga entre os nós no cluster. A outra estratégia é a [desfragmentação](service-fabric-cluster-resource-manager-defragmentation-metrics.md). A desfragmentação é executada durante a execução do mesmo balanceamento. As estratégias de balanceamento e desfragmentação podem ser usadas para diferentes métricas dentro do mesmo cluster. Um serviço pode ter métricas de balanceamento e desfragmentação. Para métricas de desfragmentação, a taxa das cargas no cluster dispara o rebalanceamento quando está _abaixo_ do limite de balanceamento. 
>

Ficar abaixo do limite de balanceamento não é uma meta explícita. Limites de balanceamento são apenas um *gatilho*. Quando o balanceamento é executado, o Gerenciador de recursos de cluster determina quais melhorias ele pode fazer, se houver. Só porque uma pesquisa de balanceamento é inicializada não significa que nada se mova. Às vezes, o cluster é desequilibrado, mas muito restrito para correto. Como alternativa, os aprimoramentos exigem movimentações que são muito [dispendiosas](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Limites de atividade
Às vezes, embora os nós sejam relativamente desequilibrados, a quantidade *total* de carga no cluster é baixa. A falta de carga pode ser um DIP transitório ou porque o cluster é novo e acaba sendo inicializado. Em ambos os casos, talvez você não queira gastar tempo balanceando o cluster porque há pouco a ser obtido. Se o cluster sofreu o balanceamento, você gastaria recursos de rede e computação para mover as coisas sem fazer nenhuma diferença *absoluta* . Para evitar movimentações desnecessárias, há outro controle conhecido como limites de atividade. Os limites de atividade permitem que você especifique algum limite inferior absoluto para a atividade. Se nenhum nó estiver acima desse limite, o balanceamento não será disparado mesmo se o limite de balanceamento for atingido.

Digamos que retenhamos nosso limite de balanceamento de três para essa métrica. Digamos também que temos um limite de atividade de 1536. No primeiro caso, embora o cluster seja desequilibrado de acordo com o limite de balanceamento, nenhum nó atende a esse limite de atividade, portanto nada acontece. No exemplo inferior, Node1 está acima do limite de atividade. Como o limite de balanceamento e o limite de atividade para a métrica foram excedidos, o balanceamento é agendado. Como exemplo, vamos examinar o diagrama a seguir: 

<center>

![exemplo de limite de atividade][Image3]
</center>

Assim como os limites de balanceamento, os limites de atividade são definidos por métrica por meio da definição de cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

via ClusterConfig. JSON para implantações autônomas ou template. JSON para clusters hospedados do Azure:

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

Os limites de balanceamento e de atividade são vinculados a um balanceamento de métrica específico que será disparado somente se o limite de balanceamento e o limite de atividade forem excedidos para a mesma métrica.

> [!NOTE]
> Quando não especificado, o limite de balanceamento para uma métrica é 1 e o limite de atividade é 0. Isso significa que o Gerenciador de recursos de cluster tentará manter essa métrica perfeitamente equilibrada para qualquer carga determinada. Se você estiver usando métricas personalizadas, é recomendável que você defina explicitamente seus próprios limites de balanceamento e atividade para suas métricas. 
>

## <a name="balancing-services-together"></a>Equilibrando serviços juntos
Se o cluster está desequilibrado ou não é uma decisão em todo o cluster. No entanto, a maneira de corrigi-lo é migrar as réplicas e instâncias de serviço individuais. Isso faz sentido, certo? Se a memória for empilhada em um nó, várias réplicas ou instâncias poderão estar contribuindo para ela. Corrigir o desequilíbrio pode exigir a movimentação de qualquer uma das réplicas com estado ou instâncias sem estado que usam a métrica desbalanceada.

Ocasionalmente, no entanto, um serviço que não era desbalanceado é movido (Lembre-se da discussão sobre pesos local e global anteriormente). Por que um serviço seria movido quando todas as métricas do serviço fossem balanceadas? Vejamos um exemplo:

- Digamos que haja quatro serviços, Service1, Service2, Service3 e Service4. 
- Service1 relata as métricas Metric1 e Metric2. 
- Service2 relata as métricas Metric2 e Metric3. 
- Service3 relata as métricas Metric3 e Metric4.
- Service4 relata Metric99 métrica. 

Certamente, você pode ver onde estamos aqui: há uma cadeia! Não temos realmente quatro serviços independentes, temos três serviços relacionados e um que está por conta própria.

<center>

os serviços de balanceamento de ![juntos][Image4]
</center>

Devido a essa cadeia, é possível que um desequilíbrio nas métricas 1-4 possa fazer com que réplicas ou instâncias pertencentes aos serviços 1-3 sejam movidas. Também sabemos que um desequilíbrio nas métricas 1, 2 ou 3 não pode causar movimentos em Service4. Não haveria nenhum ponto, pois mover as réplicas ou instâncias que pertencem a Service4 pode não fazer absolutamente nada para afetar o saldo das métricas 1-3.

O Gerenciador de recursos de cluster descobre automaticamente quais serviços estão relacionados. Adicionar, remover ou alterar as métricas para serviços pode afetar suas relações. Por exemplo, entre duas execuções de Service2 de balanceamento podem ter sido atualizadas para remover Metric2. Isso interrompe a cadeia entre Service1 e Service2. Agora, em vez de dois grupos de serviços relacionados, há três:

<center>

os serviços de balanceamento de ![juntos][Image5]
</center>

## <a name="next-steps"></a>Passos seguintes
* As métricas são como o Gerenciador de recursos de Cluster Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [Este artigo](service-fabric-cluster-resource-manager-metrics.md)
* O custo de movimento é uma maneira de sinalizar para o Gerenciador de recursos de cluster que determinados serviços são mais caros de serem movidos do que outros. Para obter mais informações sobre o custo de movimento, consulte [Este artigo](service-fabric-cluster-resource-manager-movement-cost.md)
* O Gerenciador de recursos de cluster tem várias restrições que podem ser configuradas para reduzir a rotatividade no cluster. Eles normalmente não são necessários, mas se você precisar deles, poderá aprender sobre eles [aqui](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
