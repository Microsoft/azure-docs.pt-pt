---
title: Gerenciar a carga do aplicativo Service Fabric do Azure usando métricas
description: Saiba mais sobre como configurar e usar métricas no Service Fabric para gerenciar o consumo de recursos de serviço.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ea21502cdab35b261e20af7f23b7b522f77c6667
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452004"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Gerenciando o consumo de recursos e a carga em Service Fabric com métricas
As *métricas* são os recursos sobre os quais seus serviços se preocupam e quais são fornecidos pelos nós no cluster. Uma métrica é tudo o que você deseja gerenciar para melhorar ou monitorar o desempenho de seus serviços. Por exemplo, você pode observar o consumo de memória para saber se o serviço está sobrecarregado. Outro uso é descobrir se o serviço pode se mover para outro lugar onde a memória está menos restrita para obter um melhor desempenho.

Coisas como memória, disco e uso de CPU são exemplos de métricas. Essas métricas são métricas físicas, recursos que correspondem a recursos físicos no nó que precisam ser gerenciados. As métricas também podem ser métricas lógicas (e normalmente são). Métricas lógicas são coisas como "MyWorkQueueDepth" ou "MessagesToProcess" ou "TotalRecords". As métricas lógicas são definidas pelo aplicativo e correspondem indiretamente a algum consumo de recursos físicos. Métricas lógicas são comuns porque podem ser difíceis de medir e relatar o consumo de recursos físicos por serviço. A complexidade de medir e relatar suas próprias métricas físicas também é o motivo pelo qual Service Fabric fornece algumas métricas padrão.

## <a name="default-metrics"></a>Métricas predefinidas
Digamos que você deseja começar a escrever e implantar seu serviço. Neste ponto, você não sabe quais recursos físicos ou lógicos são consumidos. Tudo bem! O Gerenciador de recursos de Cluster Service Fabric usa algumas métricas padrão quando nenhuma outra métrica é especificada. São:

  - PrimaryCount-contagem de réplicas primárias no nó 
  - ReplicaCount-contagem do total de réplicas com estado no nó
  - Contagem-contagem de todos os objetos de serviço (com e sem estado) no nó

| Métrica | Carga de instância sem estado | Carga secundária com estado | Carga primária com estado | Peso |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Elevado |
| ReplicaCount |0 |1 |1 |Médio |
| Contagem |1 |1 |1 |Baixo |


Para cargas de trabalho básicas, as métricas padrão fornecem uma distribuição razoável de trabalhos no cluster. No exemplo a seguir, vamos ver o que acontece quando criamos dois serviços e confiamos nas métricas padrão para balanceamento. O primeiro serviço é um serviço com estado com três partições e um tamanho de conjunto de réplicas de destino de três. O segundo serviço é um serviço sem estado com uma partição e uma contagem de instâncias de três.

Eis o que obtém:

<center>

![o layout de cluster com métricas padrão][Image1]
</center>

Factos a ter em conta:
  - Réplicas primárias para o serviço com estado são distribuídas entre vários nós
  - As réplicas para a mesma partição estão em nós diferentes
  - O número total de primários e secundários é distribuído no cluster
  - O número total de objetos de serviço é alocado uniformemente em cada nó

Recomendá!

As métricas padrão funcionam muito bem como um início. No entanto, as métricas padrão só levarão você até o momento. Por exemplo: Qual é a probabilidade de que o esquema de particionamento que você escolheu resulte na utilização perfeita até mesmo por todas as partições? Qual é a chance de que a carga de um determinado serviço seja constante ao longo do tempo, ou mesmo apenas o mesmo em várias partições agora?

Você pode executar apenas com as métricas padrão. No entanto, isso geralmente significa que a utilização do cluster é menor e mais desigual à desejada. Isso ocorre porque as métricas padrão não são adaptáveis e presumem que tudo seja equivalente. Por exemplo, um primário que está ocupado e outro que não contribui com "1" para a métrica PrimaryCount. Na pior das hipóteses, usar apenas as métricas padrão também pode resultar em nós com agendamentos totais, resultando em problemas de desempenho. Se estiver interessado em obter o máximo do seu cluster e evitar problemas de desempenho, você precisará usar métricas personalizadas e relatórios de carga dinâmico.

## <a name="custom-metrics"></a>Métricas personalizadas
As métricas são configuradas em uma base de instância de serviço por nome quando você está criando o serviço.

Qualquer métrica tem algumas propriedades que a descrevem: um nome, um peso e uma carga padrão.

* Nome da métrica: o nome da métrica. O nome da métrica é um identificador exclusivo para a métrica no cluster da perspectiva do Gerenciador de recursos.
* Peso: o peso da métrica define o quão importante essa métrica é relativa às outras métricas para esse serviço.
* Carga padrão: a carga padrão é representada de forma diferente, dependendo se o serviço é com ou sem estado.
  * Para serviços sem estado, cada métrica tem uma única propriedade chamada defaultload
  * Para serviços com estado, você define:
    * PrimaryDefaultLoad: a quantidade padrão dessa métrica que esse serviço consome quando é primário
    * SecondaryDefaultLoad: a quantidade padrão dessa métrica que esse serviço consome quando é um secundário

> [!NOTE]
> Se você definir métricas personalizadas e quiser _também_ usar as métricas padrão, precisará adicionar _explicitamente_ as métricas padrão de volta e definir pesos e valores para elas. Isso ocorre porque você deve definir a relação entre as métricas padrão e suas métricas personalizadas. Por exemplo, talvez você se preocupa com ConnectionCount ou WorkQueueDepth mais do que a distribuição primária. Por padrão, o peso da métrica PrimaryCount é alto, portanto, você deseja reduzi-lo para médio ao adicionar suas outras métricas para garantir que elas tenham precedência.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definindo métricas para seu serviço-um exemplo
Digamos que você queira a seguinte configuração:

  - Seu serviço relata uma métrica chamada "ConnectionCount"
  - Você também deseja usar as métricas padrão 
  - Você fez algumas medições e sabe que normalmente uma réplica primária desse serviço ocupa 20 unidades de "ConnectionCount"
  - Os secundários usam 5 unidades de "ConnectionCount"
  - Você sabe que "ConnectionCount" é a métrica mais importante em termos de gerenciamento do desempenho desse serviço específico
  - Você ainda deseja que as réplicas primárias sejam balanceadas. O balanceamento de réplicas primárias geralmente é uma boa ideia, não importa. Isso ajuda a evitar que a perda de algum domínio de nó ou de falha afete a maioria das réplicas primárias junto com ela. 
  - Caso contrário, as métricas padrão são bem

Este é o código que você escreveria para criar um serviço com essa configuração de métrica:

Código:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Os exemplos acima e o restante deste documento descrevem o gerenciamento de métricas em uma base de serviço por nome. Também é possível definir métricas para seus serviços no nível do _tipo_ de serviço. Isso é feito especificando-os em seus manifestos de serviço. A definição de métricas de nível de tipo não é recomendada por vários motivos. A primeira razão é que os nomes de métricas são frequentemente específicos do ambiente. A menos que haja um contrato firme em vigor, não é possível ter certeza de que a métrica "núcleos" em um ambiente não é "Milinúcelos" ou "núcleos" em outros. Se suas métricas estiverem definidas em seu manifesto, você precisará criar novos manifestos por ambiente. Isso geralmente leva a uma proliferação de manifestos diferentes com apenas pequenas diferenças, o que pode levar a dificuldades de gerenciamento.  
>
> As cargas de métricas são normalmente atribuídas em uma base de instância de serviço por nome. Por exemplo, digamos que você crie uma instância do serviço para o clienteA que planeja usá-la apenas levemente. Digamos também que você crie outro para Clienteb que tenha uma carga de trabalho maior. Nesse caso, você provavelmente desejaria ajustar as cargas padrão para esses serviços. Se você tiver métricas e cargas definidas por meio de manifestos e quiser dar suporte a esse cenário, ele exigirá tipos diferentes de aplicativo e de serviço para cada cliente. Os valores definidos no momento da criação do serviço substituem aqueles definidos no manifesto, portanto, você pode usá-los para definir os padrões específicos. No entanto, fazer isso faz com que os valores declarados nos manifestos não correspondam aos que o serviço realmente executa. Isso pode levar à confusão. 
>

Como lembrete: se você quiser usar apenas as métricas padrão, não precisará tocar na coleção de métricas ou fazer algo especial ao criar seu serviço. As métricas padrão são usadas automaticamente quando nenhuma outra é definida. 

Agora, vamos passar por cada uma dessas configurações mais detalhadamente e falar sobre o comportamento que ela influencia.

## <a name="load"></a>Carregar
O ponto total de definição de métricas é representar alguma carga. *Carga* é a quantidade de determinada métrica consumida por alguma instância de serviço ou réplica em um determinado nó. A carga pode ser configurada em quase qualquer ponto. Por exemplo:

  - A carga pode ser definida quando um serviço é criado. Isso é chamado de _carga padrão_.
  - As informações de métrica, incluindo as cargas padrão, para um serviço podem ser atualizadas depois que o serviço é criado. Isso é chamado _de atualização de um serviço_. 
  - As cargas para uma determinada partição podem ser redefinidas para os valores padrão para esse serviço. Isso é chamado de _redefinição de carga de partição_.
  - A carga pode ser relatada por objeto de serviço dinamicamente durante o tempo de execução. Isso é chamado de _carga de relatório_. 
  
Todas essas estratégias podem ser usadas no mesmo serviço durante seu tempo de vida. 

## <a name="default-load"></a>Carga padrão
A *carga padrão* é a quantidade de métricas que cada objeto de serviço (instância sem monitoração de estado ou réplica com estado) desse serviço consome. O Gerenciador de recursos de cluster usa esse número para a carga do objeto de serviço até receber outras informações, como um relatório de carga dinâmica. Para serviços mais simples, a carga padrão é uma definição estática. A carga padrão nunca é atualizada e é usada durante o tempo de vida do serviço. As cargas padrão funcionam bem para cenários de planejamento de capacidade simples, em que determinadas quantidades de recursos são dedicadas a cargas de trabalho diferentes e não são alteradas.

> [!NOTE]
> Para obter mais informações sobre o gerenciamento de capacidade e definir as capacidades para os nós em seu cluster, consulte [Este artigo](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

O Gerenciador de recursos de cluster permite que serviços com estado especifiquem uma carga padrão diferente para seus primários e secundários. Os serviços sem estado só podem especificar um valor que se aplique a todas as instâncias. Para serviços com estado, a carga padrão para réplicas primárias e secundárias normalmente é diferente, uma vez que as réplicas têm diferentes tipos de trabalho em cada função. Por exemplo, os primários geralmente servem leituras e gravações e lidam com a maior parte da carga computacional, enquanto as secundárias não. Normalmente, a carga padrão para uma réplica primária é maior do que a carga padrão para réplicas secundárias. Os números reais devem depender de suas próprias medições.

## <a name="dynamic-load"></a>Carga dinâmica
Digamos que você esteja executando seu serviço por um tempo. Com algum monitoramento, você percebeu que:

1. Algumas partições ou instâncias de um determinado serviço consomem mais recursos do que outras
2. Alguns serviços têm carga que varia ao longo do tempo.

Há muitas coisas que podem causar esses tipos de flutuações de carga. Por exemplo, diferentes serviços ou partições são associados a diferentes clientes com requisitos diferentes. A carga também pode ser alterada porque a quantidade de trabalho que o serviço faz varia ao longo do dia. Independentemente do motivo, geralmente não há um único número que você possa usar para o padrão. Isso é especialmente verdadeiro se você quiser obter a maior parte da utilização do cluster. Qualquer valor escolhido para a carga padrão está errado o tempo todo. As cargas padrão incorretas resultam no Gerenciador de recursos de cluster sobre ou sob alocação de recursos. Como resultado, você tem nós que estão acima ou sob utilização, embora o Gerenciador de recursos de cluster pense que o cluster está balanceado. As cargas padrão ainda são boas, pois fornecem algumas informações para posicionamento inicial, mas não são uma história completa para cargas de trabalho reais. Para capturar com precisão a alteração dos requisitos de recursos, o Gerenciador de recursos de cluster permite que cada objeto de serviço Atualize sua própria carga durante o tempo de execução. Isso é chamado de relatório de carga dinâmica.

Os relatórios de carga dinâmica permitem que réplicas ou instâncias ajustem sua carga de alocação/relatada de métricas durante seu tempo de vida. Uma réplica de serviço ou instância que estava frio e sem trabalho normalmente relataria que estava usando valores baixos de uma determinada métrica. Uma réplica ou instância ocupada relataria que está usando mais.

A carga de relatórios por réplica ou instância permite que o Gerenciador de recursos de cluster reorganize os objetos de serviço individuais no cluster. Reorganizar os serviços ajuda a garantir que eles obtenham os recursos de que precisam. Os serviços ocupados efetivamente obtêm "recuperar" recursos de outras réplicas ou instâncias que estão atualmente frios ou realizando menos trabalho.

Em Reliable Services, o código para relatar a carga dinamicamente tem esta aparência:

Código:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Um serviço pode relatar qualquer uma das métricas definidas para ela no momento da criação. Se um serviço relatar uma carga para uma métrica que não está configurada para usar, Service Fabric ignorará esse relatório. Se houver outras métricas relatadas ao mesmo tempo que são válidas, esses relatórios serão aceitos. O código de serviço pode medir e relatar todas as métricas que ele sabe como, e os operadores podem especificar a configuração de métrica a ser usada sem precisar alterar o código do serviço. 

### <a name="updating-a-services-metric-configuration"></a>Atualizando a configuração de métrica de um serviço
A lista de métricas associadas ao serviço e as propriedades dessas métricas podem ser atualizadas dinamicamente enquanto o serviço está ativo. Isso permite a experimentação e a flexibilidade. Alguns exemplos de quando isso é útil são:

  - desabilitando uma métrica com um relatório de bugs para um serviço específico
  - reconfiguração dos pesos de métricas com base no comportamento desejado
  - habilitar uma nova métrica somente depois que o código já foi implantado e validado por meio de outros mecanismos
  - alterando a carga padrão de um serviço com base no comportamento e no consumo observados

As APIs principais para alterar a configuração de métrica são C# `FabricClient.ServiceManagementClient.UpdateServiceAsync` e `Update-ServiceFabricService` no PowerShell. Quaisquer informações que você especificar com essas APIs substituirão as informações de métrica existentes para o serviço imediatamente. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Misturando valores de carga padrão e relatórios de carga dinâmica
Cargas dinâmicas e carga padrão podem ser usadas para o mesmo serviço. Quando um serviço utiliza os relatórios carga padrão e carga dinâmica, a carga padrão serve como uma estimativa até que os relatórios dinâmicos sejam exibidos. A carga padrão é boa porque dá ao Gerenciador de recursos de cluster algo com o qual trabalhar. A carga padrão permite que o Gerenciador de recursos de cluster Coloque os objetos de serviço em bons locais quando eles são criados. Se nenhuma informação de carga padrão for fornecida, o posicionamento dos serviços será efetivamente aleatório. Quando os relatórios de carga chegam posteriormente, o posicionamento aleatório inicial geralmente está errado e o Gerenciador de recursos de cluster precisa mover os serviços.

Vamos pegar nosso exemplo anterior e ver o que acontece quando adicionamos algumas métricas personalizadas e um relatório de carga dinâmico. Neste exemplo, usamos "MemoryInMb" como uma métrica de exemplo.

> [!NOTE]
> A memória é uma das métricas do sistema que Service Fabric pode [controlar recursos](service-fabric-resource-governance.md)e, em geral, é difícil reportá-la. Na verdade, não esperamos que você relate o consumo de memória; A memória é usada aqui como uma ajuda para aprender sobre os recursos do Gerenciador de recursos de cluster.
>

Vamos supor que inicialmente criamos o serviço com estado com o seguinte comando:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Como lembrete, essa sintaxe é ("Metricname, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Vejamos a aparência de um possível layout de cluster:

<center>

![cluster equilibrado com métricas padrão e personalizadas][Image2]
</center>

Algumas coisas que valem a pena observar:

* As réplicas secundárias em uma partição podem ter sua própria carga
* Em geral, as métricas parecem equilibradas. Para a memória, a taxa entre a carga máxima e a mínima é de 1,75 (o nó com a maior carga é N3, o mínimo é N2 e 28/16 = 1,75).

Há algumas coisas que ainda precisamos explicar:

* O que determinou se uma proporção de 1,75 era razoável ou não? Como o Gerenciador de recursos de cluster sabe se isso é bom o suficiente ou se há mais trabalho a fazer?
* Quando o balanceamento ocorre?
* O que significa que a memória foi ponderada "alta"?

## <a name="metric-weights"></a>Pesos de métrica
É importante controlar as mesmas métricas entre diferentes serviços. Essa exibição global é o que permite que o Gerenciador de recursos de cluster rastreie o consumo no cluster, equilibre o consumo entre os nós e garanta que os nós não ultrapassem a capacidade. No entanto, os serviços podem ter diferentes exibições quanto à importância da mesma métrica. Além disso, em um cluster com muitas métricas e muitos serviços, as soluções perfeitamente balanceadas podem não existir para todas as métricas. Como o Gerenciador de recursos de cluster deve lidar com essas situações?

Os pesos de métrica permitem que o Gerenciador de recursos de cluster decida como balancear o cluster quando não houver uma resposta perfeita. Os pesos de métrica também permitem que o Gerenciador de recursos de cluster equilibre serviços específicos de forma diferente. As métricas podem ter quatro níveis de peso diferentes: zero, baixa, média e alta. Uma métrica com peso zero não contribui nada ao considerar se as coisas estão equilibradas ou não. No entanto, sua carga ainda contribui para o gerenciamento de capacidade. As métricas com peso zero ainda são úteis e são usadas com frequência como parte do monitoramento de desempenho e comportamento do serviço. [Este artigo](service-fabric-diagnostics-event-generation-infra.md) fornece mais informações sobre o uso de métricas para monitoramento e diagnóstico de seus serviços. 

O impacto real de pesos de métricas diferentes no cluster é que o Gerenciador de recursos de cluster gera soluções diferentes. Os pesos de métricas informam ao Gerenciador de recursos de cluster que determinadas métricas são mais importantes do que outras. Quando não há uma solução perfeita, o Gerenciador de recursos de cluster pode preferir soluções que equilibrem melhor as métricas ponderadas mais altas. Se um serviço achar que uma determinada métrica não é importante, ele poderá encontrar o uso dessa métrica desbalanceada. Isso permite que outro serviço obtenha uma distribuição uniforme de alguma métrica que seja importante para ele.

Vejamos um exemplo de alguns relatórios de carga e como os pesos de métrica diferentes resultam em alocações diferentes no cluster. Neste exemplo, vemos que alternar o peso relativo das métricas faz com que o Gerenciador de recursos de cluster Crie diferentes disposições de serviços.

<center>

![exemplo de peso de métrica e seu impacto sobre as soluções de balanceamento][Image3]
</center>

Neste exemplo, há quatro serviços diferentes, todos relatando valores diferentes para duas métricas diferentes, Metrica e métrica b. Em um caso, todos os serviços definem Métricaa é o mais importante (peso = alto) e métrica b como não importante (peso = baixo). Como resultado, vemos que o Gerenciador de recursos de cluster coloca os serviços para que a Métricaa seja melhor balanceada do que métrica b. "Melhor equilibrado" significa que a Métricaa tem um menor desvio padrão que o métrica b. No segundo caso, invertemos os pesos da métrica. Como resultado, o cluster Resource Manager troca os serviços A e B para criar uma alocação em que a métrica b é mais equilibrada do que a Métricaa.

> [!NOTE]
> Os pesos de métrica determinam como o Gerenciador de recursos de cluster deve balancear, mas não quando o balanceamento deve ocorrer. Para obter mais informações sobre balanceamento, confira [Este artigo](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Pesos de métrica global
Digamos que o servicea defina a Métricaa como peso alta, e ServiceB define o peso da Métricaa como baixa ou zero. Qual é o peso real que acaba sendo usado?

Há vários pesos que são controlados para cada métrica. O primeiro peso é aquele definido para a métrica quando o serviço é criado. O outro peso é um peso global, que é calculado automaticamente. O Gerenciador de recursos de cluster usa esses dois pesos ao pontuar soluções. É importante pegar os pesos em conta. Isso permite que o Gerenciador de recursos de cluster equilibre cada serviço de acordo com suas próprias prioridades e verifique também se o cluster como um todo está alocado corretamente.

O que aconteceria se o Gerenciador de recursos de cluster não se preocupasse com o balanceamento global e local? Bem, é fácil construir soluções que são balanceadas globalmente, mas que resultam em um equilíbrio de recursos insatisfatório para serviços individuais. No exemplo a seguir, vamos examinar um serviço configurado apenas com as métricas padrão e ver o que acontece quando apenas o saldo global é considerado:

<center>

![o impacto de uma solução somente global][Image4]
</center>

No exemplo superior com base apenas no saldo global, o cluster como um todo está realmente balanceado. Todos os nós têm a mesma contagem de primários e o mesmo número total de réplicas. No entanto, se você observar o impacto real dessa alocação, isso não é tão bom: a perda de qualquer nó afeta uma determinada carga de trabalho desproporcionalmente, pois ela retira todos os seus primários. Por exemplo, se o primeiro nó falhar, os três primários para as três partições diferentes do serviço de círculo seriam perdidos. Por outro lado, os serviços de triângulo e hexágono têm suas partições perdem uma réplica. Isso não causa nenhuma interrupção, além de ter que recuperar a réplica inoperante.

No exemplo inferior, o Gerenciador de recursos de cluster distribuiu as réplicas com base no saldo global e por serviço. Ao calcular a pontuação da solução, ela fornece a maior parte do peso para a solução global e uma parte (configurável) para serviços individuais. O saldo global para uma métrica é calculado com base na média dos pesos de métrica de cada serviço. Cada serviço é balanceado de acordo com seus próprios pesos de métrica definidos. Isso garante que os serviços sejam balanceados em si mesmos de acordo com suas próprias necessidades. Como resultado, se o mesmo primeiro nó falhar, a falha será distribuída entre todas as partições de todos os serviços. O impacto de cada um é o mesmo.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como configurar serviços, [saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)(Service-Fabric-cluster-Resource-Manager-Configure-Services.MD)
- A definição de métricas de desfragmentação é uma maneira de consolidar a carga em nós em vez de difundir. Para saber como configurar a desfragmentação, consulte [Este artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Para saber como o Gerenciador de recursos de cluster gerencia e equilibra a carga no cluster, confira o artigo sobre [balanceamento](service-fabric-cluster-resource-manager-balancing.md) de carga
- Comece desde o início e [obtenha uma introdução ao Gerenciador de recursos de Cluster Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- O custo de movimento é uma maneira de sinalizar para o Gerenciador de recursos de cluster que determinados serviços são mais caros de serem movidos do que outros. Para saber mais sobre o custo de movimento, consulte [Este artigo](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
