---
title: Gerir a carga de aplicativo sinuoso azure service fabric usando métricas
description: Saiba como configurar e utilizar métricas no Tecido de Serviço para gerir o consumo de recursos de serviço.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ea21502cdab35b261e20af7f23b7b522f77c6667
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75452004"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Gestão do consumo e carga de recursos em Tecido de Serviço com métricas
*As métricas* são os recursos com que os seus serviços se preocupam e que são fornecidos pelos nós do cluster. Uma métrica é tudo o que pretende gerir para melhorar ou monitorizar o desempenho dos seus serviços. Por exemplo, pode ver o consumo de memória para saber se o seu serviço está sobrecarregado. Outra utilização é descobrir se o serviço pode mover-se para outro lugar onde a memória é menos limitada para obter um melhor desempenho.

Coisas como memória, disco e uso de CPU são exemplos de métricas. Estas métricas são métricas físicas, recursos que correspondem aos recursos físicos no nó que precisam de ser geridos. As métricas também podem ser (e geralmente são) métricas lógicas. Métricas lógicas são coisas como "MyWorkQueueDepth" ou "MessagesToProcess" ou "TotalRecords". As métricas lógicas são definidas pela aplicação e correspondem indiretamente a algum consumo de recursos físicos. As métricas lógicas são comuns porque pode ser difícil medir e reportar o consumo de recursos físicos por serviço. A complexidade de medir e reportar as suas próprias métricas físicas é também a razão pela qual o Service Fabric fornece algumas métricas padrão.

## <a name="default-metrics"></a>Métricas padrão
Digamos que quer começar a escrever e a implementar o seu serviço. Neste momento não sabe que recursos físicos ou lógicos consome. Não há problema! O Gestor de Recursos de Cluster de Tecidos de Serviço utiliza algumas métricas padrão quando não são especificadas outras métricas. São:

  - Contagem Primária - contagem de réplicas primárias no nó 
  - ReplicaCount - contagem de réplicas totais e audais no nó
  - Contagem - contagem de todos os objetos de serviço (apátrida e apátrida) no nó

| Métrica | Carga de instância apátrida | Carga Secundária Audais | Carga Primária Apátrida | Peso |
| --- | --- | --- | --- | --- |
| Contagem primária |0 |0 |1 |Alta |
| RepliceCount |0 |1 |1 |Médio |
| Contagem |1 |1 |1 |Baixa |


Para cargas de trabalho básicas, as métricas padrão proporcionam uma distribuição decente do trabalho no cluster. No exemplo seguinte, vamos ver o que acontece quando criamos dois serviços e dependemos das métricas padrão para equilibrar. O primeiro serviço é um serviço imponente com três divisórias e um conjunto de réplica seletiva de três. O segundo serviço é um serviço apátrida com uma partição e uma contagem de três exemplos.

Eis o que obtém:

<center>

![Layout de cluster com métricas padrão][Image1]
</center>

Factos a ter em conta:
  - Réplicas primárias para o serviço de estado são distribuídas por vários nódosos
  - Réplicas para a mesma partição estão em diferentes nódosos
  - O número total de primárias e secundários é distribuído no cluster
  - O número total de objetos de serviço são igualmente alocados em cada nó

Muito bem!

As métricas padrão funcionam muito bem como um começo. No entanto, as métricas padrão só irão levá-lo até agora. Por exemplo: Qual é a probabilidade de o esquema de partição que escolheu resultar em perfeitamente até utilização por todas as divisórias? Qual é a hipótese de a carga para um determinado serviço ser constante ao longo do tempo, ou mesmo a mesma em várias divisórias neste momento?

Pode saquecom apenas as métricas padrão. No entanto, fazê-lo geralmente significa que a utilização do seu cluster é menor e mais desigual do que gostaria. Isto porque as métricas padrão não são adapttivas e presumem que tudo é equivalente. Por exemplo, uma Primary que está ocupada e que não é ambas contribuem "1" para a métrica do Conde Primário. Na pior das hipóteses, a utilização apenas das métricas padrão também pode resultar em nós sobreprogramados, resultando em problemas de desempenho. Se está interessado em tirar o máximo partido do seu cluster e evitar problemas de desempenho, precisa de usar métricas personalizadas e relatórios dinâmicos de carga.

## <a name="custom-metrics"></a>Métricas personalizadas
As métricas são configuradas numa base por exemplo de serviço quando está a criar o serviço.

Qualquer métrica tem algumas propriedades que o descrevem: um nome, um peso e uma carga padrão.

* Nome métrico: O nome da métrica. O nome métrico é um identificador único para a métrica dentro do cluster a partir da perspetiva do Gestor de Recursos.
* Peso: O peso métrico define a importância desta métrica em relação às outras métricas para este serviço.
* Carga predefinida: A carga predefinida é representada de forma diferente dependendo se o serviço é apátrida ou apátrida.
  * Para serviços apátridas, cada métrica tem uma única propriedade chamada DefaultLoad
  * Para serviços imponentes, define:
    * PrimaryDefaultLoad: A quantidade padrão desta métrica que este serviço consome quando é uma Primary
    * SecondaryDefaultLoad: A quantidade padrão desta métrica que este serviço consome quando é secundária

> [!NOTE]
> Se definir métricas personalizadas e pretender _também_ utilizar as métricas padrão, precisa de adicionar _explicitamente_ as métricas padrão de volta e definir pesos e valores para elas. Isto porque deve definir a relação entre as métricas padrão e as suas métricas personalizadas. Por exemplo, talvez se preocupe mais com o ConnectionCount ou workQueueDepth do que com a distribuição primária. Por padrão, o peso da métrica PrimaryCount é elevado, pelo que pretende reduzi-lo para Médio quando adicionar as suas outras métricas para garantir que têm precedência.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definição de métricas para o seu serviço - um exemplo
Digamos que quer a seguinte configuração:

  - O seu serviço reporta uma métrica chamada "ConnectionCount"
  - Também quer usar as métricas padrão 
  - Fez algumas medições e sabe que normalmente uma réplica primária desse serviço ocupa 20 unidades de "ConnectionCount"
  - Os secundários utilizam 5 unidades de "ConnectionCount"
  - Você sabe que "ConnectionCount" é a métrica mais importante em termos de gestão do desempenho deste serviço em particular
  - Ainda quer réplicas primárias equilibradas. Equilibrar réplicas primárias é geralmente uma boa ideia, não importa o que aconteça. Isto ajuda a evitar que a perda de algum domínio de nó ou de falha tenha impacto na maioria das réplicas primárias juntamente com ele. 
  - Caso contrário, as métricas padrão estão bem

Aqui está o código que escreveria para criar um serviço com essa configuração métrica:

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

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Os exemplos acima referidos e o resto deste documento descrevem a gestão das métricas por serviço. Também é possível definir métricas para os seus serviços ao nível do _tipo_ de serviço. Isto é conseguido especificando-os nos manifestos de serviço. A definição de métricas de nível de tipo não é recomendada por várias razões. A primeira razão é que os nomes métricos são frequentemente específicos do ambiente. A menos que haja um contrato firme em vigor, não pode ter a certeza de que a métrica "Cores" num ambiente não é "MiliCores" ou "CoReS" noutros. Se as suas métricas forem definidas no seu manifesto, precisa de criar novos manifestos por ambiente. Isto conduz geralmente a uma proliferação de manifestos diferentes com apenas pequenas diferenças, o que pode levar a dificuldades de gestão.  
>
> As cargas métricas são geralmente atribuídas por exemplo de serviço. Por exemplo, digamos que cria uma instância do serviço para o ClienteA que planeia usá-lo apenas de ânimo leve. Digamos também que cria outro para o CustomerB que tem uma carga de trabalho maior. Neste caso, provavelmente quereria ajustar as cargas padrão para esses serviços. Se tiver métricas e cargas definidas através de manifestos e quiser apoiar este cenário, requer diferentes tipos de aplicação e serviço para cada cliente. Os valores definidos no tempo de criação de serviço supõem-se aos definidos no manifesto, para que possa usá-lo para definir os incumprimentos específicos. No entanto, fazer isso faz com que os valores declarados nos manifestos não correspondam aos que o serviço realmente funciona. Isto pode levar à confusão. 
>

Como lembrete: se apenas quiser usar as métricas padrão, não precisa de tocar na coleção de métricas ou fazer algo especial ao criar o seu serviço. As métricas padrão são usadas automaticamente quando não são definidas outras. 

Agora, vamos analisar cada uma destas configurações com mais detalhes e falar sobre o comportamento que influencia.

## <a name="load"></a>Carregar
O objetivo da definição das métricas é representar alguma carga. *Carga* é quanto de uma determinada métrica é consumida por algum exemplo de serviço ou réplica em um dado nó. A carga pode ser configurada em qualquer ponto. Por exemplo:

  - A carga pode ser definida quando um serviço é criado. Isto chama-se _carga padrão_.
  - A informação métrica, incluindo cargas por defeito, para um serviço pode ser atualizada após a criação do serviço. Isto chama-se _atualização de um serviço._ 
  - As cargas para uma determinada partição podem ser redefinidas para os valores padrão desse serviço. Isto chama-se reposição da _carga da divisória._
  - A carga pode ser reportada numa base de objeto de serviço de forma dinâmica durante o tempo de execução. Isto chama-se carga de _reporte._ 
  
Todas estas estratégias podem ser usadas dentro do mesmo serviço ao longo da sua vida. 

## <a name="default-load"></a>Carga padrão
*A carga padrão* é a quantidade de métrica que cada objeto de serviço (instância apátrida ou réplica apátrida) deste serviço consome. O Cluster Resource Manager utiliza este número para a carga do objeto de serviço até receber outras informações, como um relatório dinâmico de carga. Para serviços mais simples, a carga padrão é uma definição estática. A carga predefinida nunca é atualizada e é utilizada durante toda a vida útil do serviço. As cargas padrão funcionam muito bem para cenários simples de planeamento de capacidades onde determinadas quantidades de recursos são dedicadas a diferentes cargas de trabalho e não mudam.

> [!NOTE]
> Para obter mais informações sobre a gestão da capacidade e definição de capacidades para os nós do seu cluster, consulte [este artigo](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

O Cluster Resource Manager permite que os serviços estatais especifiquem uma carga padrão diferente para as suas Primárias e Secundárias. Os serviços apátridas só podem especificar um valor que se aplique a todas as instâncias. Para serviços estatais, a carga padrão para réplicas primárias e secundárias é tipicamente diferente, uma vez que as réplicas fazem diferentes tipos de trabalho em cada função. Por exemplo, as primárias geralmente servem tanto as leituras como as escritas, e lidam com a maior parte dos encargos computacionais, enquanto os secundários não. Normalmente, a carga padrão para uma réplica primária é superior à carga padrão para réplicas secundárias. Os números reais devem depender das suas próprias medidas.

## <a name="dynamic-load"></a>Carga dinâmica
Digamos que tem gerido o seu serviço há algum tempo. Com alguma monitorização, reparaste que:

1. Algumas divisórias ou instâncias de um determinado serviço consomem mais recursos do que outros
2. Alguns serviços têm carga que varia ao longo do tempo.

Há muitas coisas que podem causar este tipo de flutuações de carga. Por exemplo, diferentes serviços ou divisórias estão associados a diferentes clientes com diferentes requisitos. A carga também pode mudar porque a quantidade de trabalho que o serviço faz varia ao longo do dia. Independentemente da razão, normalmente não há um único número que possa usar para o padrão. Isto é especialmente verdade se quiser tirar o máximo de utilização do cluster. Qualquer valor que escolha para a carga padrão está errado algumas vezes. As cargas predefinidas incorretas resultam no Gestor de Recursos de Cluster, quer sobre ou sob a atribuição de recursos. Como resultado, você tem nós que estão sobre ou sob utilização, mesmo que o Cluster Resource Manager pense que o cluster é equilibrado. As cargas padrão ainda são boas, uma vez que fornecem algumas informações para a colocação inicial, mas não são uma história completa para cargas de trabalho reais. Para capturar com precisão os requisitos de mudança de recursos, o Cluster Resource Manager permite que cada objeto de serviço atualize a sua própria carga durante o tempo de execução. Isto chama-se relatório dinâmico de carga.

Os relatórios dinâmicos de carga permitem que réplicas ou instâncias ajustem a sua alocação/carga reportada de métricas ao longo da sua vida útil. Uma réplica de serviço ou instância que estava fria e que não estava a fazer nenhum trabalho normalmente relataria que estava a usar baixas quantidades de uma determinada métrica. Uma réplica ou instância ocupada relataria que estão a usar mais.

Reportar carga por réplica ou instância permite ao Cluster Resource Manager reorganizar os objetos de serviço individuais no cluster. A reorganização dos serviços ajuda a garantir que obtêm os recursos necessários. Os serviços ocupados conseguem efetivamente "recuperar" recursos de outras réplicas ou instâncias que são atualmente frias ou que estão a fazer menos trabalho.

Dentro dos Serviços Fiáveis, o código para reportar a carga dinamicamente é assim:

Código:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Um serviço pode reportar qualquer uma das métricas definidas para ele no momento da criação. Se um serviço reportar a carga para uma métrica que não está configurada para usar, o Service Fabric ignora esse relatório. Se houver outras métricas reportadas ao mesmo tempo que sejam válidas, esses relatórios são aceites. O código de serviço pode medir e reportar todas as métricas que sabe, e os operadores podem especificar a configuração métrica a utilizar sem ter que alterar o código de serviço. 

### <a name="updating-a-services-metric-configuration"></a>Atualizar a configuração métrica de um serviço
A lista de métricas associadas ao serviço, e as propriedades dessas métricas podem ser atualizadas dinamicamente enquanto o serviço está ao vivo. Isto permite a experimentação e flexibilidade. Alguns exemplos de quando isto é útil são:

  - desativando uma métrica com um relatório buggy para um serviço particular
  - reconfigurar os pesos das métricas com base no comportamento desejado
  - permitindo uma nova métrica apenas depois de o código já ter sido implementado e validado através de outros mecanismos
  - alterando a carga padrão para um serviço baseado no comportamento e consumo observados

As principais APIs para `FabricClient.ServiceManagementClient.UpdateServiceAsync` alterar a `Update-ServiceFabricService` configuração métrica estão em C# e no PowerShell. Qualquer informação que especifique com estas APIs substitui imediatamente a informação métrica existente para o serviço. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Misturar valores de carga padrão e relatórios de carga dinâmicos
A carga padrão e as cargas dinâmicas podem ser utilizadas para o mesmo serviço. Quando um serviço utiliza relatórios de carga padrão e dinâmicos, a carga padrão serve como estimativa até que os relatórios dinâmicos apareçam. A carga padrão é boa porque dá ao Gestor de Recursos cluster algo com que trabalhar. A carga predefinida permite ao Gestor de Recursos de Cluster colocar os objetos de serviço em boas localizações quando são criados. Se não for emprecida nenhuma informação de carga por defeito, a colocação de serviços é efetivamente aleatória. Quando os relatórios de carga chegam mais tarde, a colocação aleatória inicial é muitas vezes errada e o Gestor de Recursos de Cluster tem que mover serviços.

Vamos dar o nosso exemplo anterior e ver o que acontece quando adicionamos algumas métricas personalizadas e relatórios dinâmicos de carga. Neste exemplo, usamos "MemoryInMb" como uma métrica de exemplo.

> [!NOTE]
> A memória é uma das métricas do sistema que o Service Fabric pode [reger,](service-fabric-resource-governance.md)e denunciá-la por si mesmo é tipicamente difícil. Não esperamos que informe sobre o consumo de memória; A memória é usada aqui como uma ajuda para aprender sobre as capacidades do Cluster Resource Manager.
>

Vamos presumir que inicialmente criámos o serviço imponente com o seguinte comando:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Como lembrete, esta sintaxe é ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Vamos ver como um possível layout de cluster poderia ser:

<center>

![Cluster Equilibrado com métricas padrão e personalizadas][Image2]
</center>

Algumas coisas que valem a pena notar:

* Réplicas secundárias dentro de uma divisória podem ter cada uma a sua própria carga
* No geral, as métricas parecem equilibradas. Para memória, a razão entre a carga máxima e a mínima é de 1,75 (o nó com mais carga é N3, o mínimo é N2, e 28/16 = 1,75).

Há algumas coisas que ainda precisamos explicar:

* O que determinou se um rácio de 1,75 era razoável ou não? Como é que o Gestor de Recursos de Cluster sabe se isso é bom o suficiente ou se há mais trabalho a fazer?
* Quando é que o equilíbrio acontece?
* O que significa que a memória foi ponderada "Alta"?

## <a name="metric-weights"></a>Pesos métricos
Rastrear as mesmas métricas em diferentes serviços é importante. Essa visão global é o que permite ao Cluster Resource Manager acompanhar o consumo no cluster, equilibrar o consumo em nós, e garantir que os nós não ultrapassem a capacidade. No entanto, os serviços podem ter opiniões diferentes quanto à importância da mesma métrica. Além disso, num cluster com muitas métricas e muitos serviços, soluções perfeitamente equilibradas podem não existir para todas as métricas. Como deve o Gestor de Recursos de Cluster lidar com estas situações?

Os pesos métricos permitem ao Gestor de Recursos de Cluster decidir como equilibrar o cluster quando não há uma resposta perfeita. Os pesos métricos também permitem ao Cluster Resource Manager equilibrar serviços específicos de forma diferente. As métricas podem ter quatro níveis de peso diferentes: Zero, Baixo, Médio e Alto. Uma métrica com um peso de Zero não contribui para nada quando se considera se as coisas estão equilibradas ou não. No entanto, a sua carga continua a contribuir para a gestão da capacidade. As métricas com peso Zero ainda são úteis e são frequentemente usadas como parte do comportamento do serviço e monitorização do desempenho. [Este artigo](service-fabric-diagnostics-event-generation-infra.md) fornece mais informações sobre a utilização de métricas para monitorização e diagnóstico dos seus serviços. 

O impacto real de diferentes pesos métricos no cluster é que o Cluster Resource Manager gera diferentes soluções. Os pesos métricos dizem ao Gestor de Recursos de Cluster que certas métricas são mais importantes que outras. Quando não há uma solução perfeita, o Cluster Resource Manager pode preferir soluções que equilibrem melhor as métricas ponderadas mais elevadas. Se um serviço achar que uma determinada métrica não é importante, pode encontrar o seu uso dessa métrica desequilibrada. Isto permite que outro serviço obtenha uma distribuição uniforme de alguma métrica que é importante para ele.

Vejamos um exemplo de alguns relatórios de carga e como diferentes pesos métricos resultam em diferentes alocações no cluster. Neste exemplo, vemos que a mudança do peso relativo das métricas faz com que o Cluster Resource Manager crie diferentes arranjos de serviços.

<center>

![Exemplo de peso métrico e seu impacto em soluções de equilíbrio][Image3]
</center>

Neste exemplo, existem quatro serviços diferentes, todos reportando valores diferentes para duas métricas diferentes, MetricA e MetricB. Num dos casos, todos os serviços definem a MetricA é o mais importante (Peso = Alto) e MetricB como pouco importante (Peso = Baixo). Como resultado, vemos que o Cluster Resource Manager coloca os serviços de modo a que a MetricA seja mais equilibrada do que a MetricB. "Melhor equilibrado" significa que a MetricA tem um desvio padrão inferior ao do MetricB. No segundo caso, revertemos os pesos métricos. Como resultado, o Cluster Resource Manager troca os serviços A e B para chegar a uma dotação onde o MetricB é mais equilibrado do que o MetricA.

> [!NOTE]
> Os pesos métricos determinam como o Gestor de Recursos de Cluster deve equilibrar,mas não quando o equilíbrio deve acontecer. Para mais informações sobre o equilíbrio, confira [este artigo](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Pesos métricos globais
Digamos que o ServiceA define a Métrica como peso alto, e o ServiceB define o peso para A Métrica para Baixo ou Zero. Qual é o peso real que acaba por ser usado?

Há vários pesos que são rastreados para cada métrica. O primeiro peso é o definido para a métrica quando o serviço é criado. O outro peso é um peso global, que é calculado automaticamente. O Cluster Resource Manager utiliza ambos estes pesos ao marcar soluções. Ter em conta ambos os pesos é importante. Isto permite ao Cluster Resource Manager equilibrar cada serviço de acordo com as suas próprias prioridades, e também garantir que o cluster como um todo é alocado corretamente.

O que aconteceria se o Gestor de Recursos de Cluster não se importasse com o equilíbrio global e local? Bem, é fácil construir soluções que são globalmente equilibradas, mas que resultam num fraco equilíbrio de recursos para os serviços individuais. No exemplo seguinte, vamos olhar para um serviço configurado apenas com as métricas padrão, e ver o que acontece quando apenas o equilíbrio global é considerado:

<center>

![O Impacto de uma Solução Global Única][Image4]
</center>

No exemplo de topo baseado apenas no equilíbrio global, o cluster no seu conjunto é, de facto, equilibrado. Todos os nós têm a mesma contagem de primárias e o mesmo número total de réplicas. No entanto, se olharmos para o impacto real desta dotação não é tão bom: a perda de qualquer nó tem um impacto desproporcionado numa determinada carga de trabalho, porque elimina todas as suas primárias. Por exemplo, se o primeiro nó falhar as três primárias para as três divisórias diferentes do serviço Circle perder-se-iam todas. Inversamente, os serviços triângulo e hexágono têm as suas divisórias a perder uma réplica. Isto não causa nenhuma perturbação, a não ser ter que recuperar a réplica para baixo.

No exemplo inferior, o Cluster Resource Manager distribuiu as réplicas com base no saldo global e por serviço. Ao calcular a pontuação da solução, dá a maior parte do peso à solução global, e uma porção (configurável) aos serviços individuais. O saldo global para uma métrica é calculado com base na média dos pesos métricos de cada serviço. Cada serviço é equilibrado de acordo com os seus próprios pesos métricos definidos. Isto garante que os serviços são equilibrados dentro de si mesmos de acordo com as suas próprias necessidades. Como resultado, se o mesmo nó falhar, a falha é distribuída por todas as divisórias de todos os serviços. O impacto para cada um é o mesmo.

## <a name="next-steps"></a>Passos seguintes
- Para mais informações sobre a configuração de serviços, [Saiba sobre a configuração](service-fabric-cluster-resource-manager-configure-services.md)de Serviços (service-fabric-cluster-resource-manager-configure-services.md)
- Definir as Métricas de Desfragmentação é uma forma de consolidar a carga nos nódosos em vez de a espalhar. Para aprender a configurar a desfragmentação, consulte [este artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre [a carga](service-fabric-cluster-resource-manager-balancing.md) de equilíbrio
- Comece do início e obtenha uma introdução ao Gestor de Recursos de Cluster de [Tecidos de Serviço](service-fabric-cluster-resource-manager-introduction.md)
- O Custo de Movimento é uma forma de sinalizar ao Gestor de Recursos cluster que certos serviços são mais caros para se movimentardo do que outros. Para saber mais sobre o custo de movimento, consulte [este artigo](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
