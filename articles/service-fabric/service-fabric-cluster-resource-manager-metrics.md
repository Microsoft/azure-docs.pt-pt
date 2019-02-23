---
title: Gerir a carga de aplicação do Azure Service Fabric com métricas | Documentos da Microsoft
description: Saiba mais sobre como configurar e utilizar as métricas no Service Fabric para gerir o consumo de recursos de serviço.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 2d1818f42cb2bcb19f979f25962a6c9bdea10155
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728017"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Consumo de recursos de gerenciamento e a carga no Service Fabric com a métrica
*Métricas* são os recursos que o cuidado de serviços sobre e que é fornecido por nós no cluster. Uma métrica é tudo o que pretende gerir para melhorar ou monitorizar o desempenho dos seus serviços. Por exemplo, poderá ver o consumo de memória para saber se o seu serviço está sobrecarregado. Outro uso é descobrir se o serviço foi possível mover a outro lugar em que a memória é que menos restrita para obter um melhor desempenho.

Coisas como a utilização de memória, disco e CPU são exemplos de métricas. Estas métricas são métricas físicas, os recursos que correspondam aos recursos físicos no nó que tem de ser geridos. Métricas também podem ser (e são normalmente) lógicas métricas. Métricas de lógicas são coisas como "MyWorkQueueDepth" ou "MessagesToProcess" ou "TotalRecords". Métricas de lógicas são definidos pela aplicação e indiretamente correspondem a algum consumo de recursos físicos. Métricas de lógicas são comuns, porque pode ser difícil de medida e relatório consumo de recursos físicos numa base por serviço. A complexidade de medir e criar relatórios suas próprias métricas físicas também é por que o Service Fabric fornece algumas métricas de predefinição.

## <a name="default-metrics"></a>Métricas predefinidas
Digamos que deseja começar a gravar e implantar o seu serviço. Neste momento não sabe quais recursos físicos ou lógicos que consome. Não há problema! O Gestor de recursos de Cluster do Service Fabric utiliza algumas métricas predefinidas quando não existem outras métricas são especificadas. São:

  - PrimaryCount - contagem de réplicas primárias no nó 
  - ReplicaCount - contagem de réplicas com monitoração de estado do total no nó
  - Contagem - contagem de todos os objetos do serviço (com e sem monitoração de estado) no nó

| Métrica | Carga de instância sem monitoração de estado | Carga secundária com monitoração de estado | Carga primária com monitoração de estado | Peso |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Elevado |
| ReplicaCount |0 |1 |1 |Médio |
| Contagem |1 |1 |1 |Baixa |


Para cargas de trabalho básicas, as métricas do padrão fornecem uma distribuição decente de trabalho no cluster. No exemplo a seguir, vamos ver o que acontece quando criamos dois serviços e contar com as métricas de padrão para o balanceamento. O primeiro serviço é um serviço com monitorização de estado com três partições e uma réplica de destino definir o tamanho de três. O serviço de segundo é um serviço sem monitoração de estado com uma partição e uma contagem de instâncias de três.

Eis o que fazer:

<center>
![Esquema de cluster com métricas predefinidas][Image1]
</center>

Algumas coisas a serem observadas:
  - Réplicas primárias para o serviço com estado são distribuídas por vários nós
  - Réplicas para a mesma partição estão em nós diferentes
  - O número total de cores primárias e secundárias é distribuído em cluster
  - O número total de objetos do serviço de forma uniforme é alocado em cada nó

Bom!

As métricas de predefinição funcionam muito bem como um começo. No entanto, as métricas de predefinição apenas incluirão até agora. Por exemplo: O que é a probabilidade de que a criação de partições de esquema escolheu resultados na utilização do perfeitamente até mesmo por todas as partições? O que é a chance de que a carga para um determinado serviço for constante ao longo do tempo, ou até mesmo da mesma em várias partições neste momento?

Pode executar com apenas as métricas de predefinição. No entanto, se o fizer, normalmente, significa que a utilização do cluster é inferior e mais desigual que gostaria. Isto acontece porque as métricas de predefinição não são adaptáveis e presumem tudo o que é equivalente. Por exemplo, um site primário que está ocupado e que não seja ambos contribuem com "1" para a métrica de PrimaryCount. Na pior das hipóteses, a utilização apenas as métricas de predefinição pode também resultar em nós overscheduled, resultando em problemas de desempenho. Se estiver interessado em obter o máximo proveito do seu cluster e evitando problemas de desempenho, precisa usar métricas personalizadas e relatórios de carga dinâmica.

## <a name="custom-metrics"></a>Métricas personalizadas
As métricas são configuradas numa base por-chamada--instância de serviço ao criar o serviço.

Qualquer métrica tem algumas propriedades que descrevem ele: um nome, um peso e uma carga de predefinição.

* Nome da métrica: O nome da métrica. O nome da métrica é um identificador exclusivo para a métrica dentro do cluster do ponto de vista do Resource Manager.
* Peso: Peso métrica define quão importante que esta métrica é relativo as outras métricas para este serviço.
* Padrão de carga: A carga de padrão é representada em forma diferente, dependendo do serviço é com ou sem estado.
  * Para serviços sem estado, cada uma tem uma única propriedade chamada DefaultLoad
  * Para serviços com estado definir:
    * PrimaryDefaultLoad: A quantidade de padrão desta métrica este serviço consome quando é um site primário
    * SecondaryDefaultLoad: A quantidade de padrão desta métrica este serviço consome quando é uma secundária

> [!NOTE]
> Se definir métricas personalizadas e pretender _também_ utilizar as métricas de padrão, precisa _explicitamente_ adicionar as métricas de predefinição fazer uma cópia e definem os pesos e valores para os mesmos. Isso é porque tem de definir a relação entre as métricas padrão e as suas métricas personalizadas. Por exemplo, talvez se preocupa ConnectionCount ou WorkQueueDepth mais do que a distribuição primária. Por predefinição, a ponderação da métrica PrimaryCount é alta, para que deseja reduzir para médio quando adiciona as outras métricas para garantir que eles têm precedência.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definir métricas para o seu serviço – um exemplo
Digamos que pretende que a seguinte configuração:

  - Uma métrica com o nome "ConnectionCount" relatórios do seu serviço
  - Também queira utilizar as métricas de predefinição 
  - Fizemos algumas medições e saber o que normalmente uma réplica primária desse serviço demora cerca de 20 unidades de "ConnectionCount"
  - Bases de dados secundárias utilizam 5 unidades de "ConnectionCount"
  - Sabe que "ConnectionCount" é a métrica mais importante em termos de gerir o desempenho deste serviço específico
  - Pretender continuar com balanceamento de réplicas primárias. Balanceamento de réplicas primárias geralmente é uma boa idéia seja qual for. Isto ajuda a impedir a perda de um domínio de nó ou uma falha de afetar a maioria das réplicas primárias, juntamente com ele. 
  - Caso contrário, as métricas padrão podem ser utilizadas

Aqui está o código que faria para criar um serviço com essa configuração da métrica:

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
> Os exemplos acima e o restante deste documento descrevem as métricas de gestão numa base por chamada-serviço. Também é possível definir métricas para os seus serviços no serviço _tipo_ nível. Isto é conseguido ao especificá-los em seus manifestos de serviço. Definir as métricas ao nível do tipo não é recomendada por vários motivos. O primeiro motivo é que os nomes de métrica são específicas do ambiente com frequência. A menos que exista um contrato de empresa no local, é possível ter certeza de que a métrica "Núcleos" num ambiente não é para "MiliCores" ou "Núcleos" em outros. Se as suas métricas são definidas no seu manifesto que tem de criar novos manifestos por ambiente. Isso geralmente leva a uma proliferação de manifestos diferentes com apenas pequenas diferenças, o que podem levar a problemas de gestão.  
>
> Cargas de métrica normalmente são atribuídas individualmente por-chamada--instância de serviço. Por exemplo, digamos que criar uma instância do serviço para CustomerA que planeia utilizá-lo apenas superficialmente. Vamos também supor que cria outro para CustomerB que tenha uma carga de trabalho maior. Neste caso, provavelmente desejaria ajustar as cargas de padrão para os serviços. Se o ter métricas e cargas definidas por meio de manifestos e quiser suportar este cenário, requer aplicação diferente e tipos de serviço para cada cliente. Os valores definidos no momento da criação de serviço substituem as que definido no manifesto, para que poderia usá-lo para definir os padrões específicos. No entanto, isso faz com que os valores declarados nos manifestos, que não correspondam às, o serviço, na verdade, é executado com. Isso pode gerar confusão. 
>

Como lembrete: Se quiser apenas utilizar as métricas de padrão, não precisa tocar a coleção de métricas de todo ou fazer nada de especial ao criar o seu serviço. As métricas de predefinição se acostumar automaticamente quando não existem outros são definidos. 

Agora, vamos percorrer cada uma destas definições mais detalhadamente e falar sobre o comportamento que afeta.

## <a name="load"></a>Carregar
Todo o objetivo da definição de métrica é representar alguma carga. *Carga* é a quantidade de uma determinada métrica é consumido por alguma instância de serviço ou a réplica num determinado nó. Carga pode ser configurada em praticamente qualquer ponto. Por exemplo:

  - Carga pode ser definida quando é criado um serviço. Isso é chamado _carga padrão_.
  - As informações de métricas, incluindo cargas da predefinição, para um serviço pode atualizado depois do serviço é criado. Isso é chamado _atualizar um serviço_. 
  - As cargas de uma determinada partição podem ser repostas para os valores predefinidos para esse serviço. Isso é chamado _repor a carga de partição_.
  - Carga pode ser comunicada num por base do objeto de serviço dinamicamente durante o tempo de execução. Isso é chamado _reporting carga_. 
  
Todas essas estratégias podem ser utilizadas no mesmo serviço durante sua vida útil. 

## <a name="default-load"></a>Padrão de carga
*Padrão de carga* é a quantidade da métrica consome de cada objeto de serviço (instância sem monitoração de estado ou a réplica com monitoração de estado) deste serviço. O Gestor de recursos de Cluster utiliza este número para a carga do objeto serviço até receber outras informações, como um relatório de carga dinâmico. Para os serviços mais simples, o padrão de carga é uma definição estática. A carga de predefinição nunca é atualizada e é usada pelo tempo de vida do serviço. Padrão carrega funciona bem para cenários em que determinadas quantidades de recursos dedicadas para diferentes cargas de trabalho e não alterar o planeamento de capacidade simple.

> [!NOTE]
> Para obter mais informações sobre o gerenciamento de capacidade e definir as capacidades para os nós do cluster, consulte [este artigo](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

O Gestor de recursos de Cluster permite que os serviços com estado especificar uma carga de predefinido diferente para as cores primárias e secundárias. Serviços sem estado só podem especificar um valor que se aplica a todas as instâncias. Para serviços com estado, a carga de padrão principal e secundário réplicas são normalmente diferentes, uma vez que as réplicas fazer diferentes tipos de trabalho em cada função. Por exemplo, cores primárias, normalmente, servem leituras e gravações e lidar com a maior parte do trabalho computacional, ao contrário bases de dados secundárias. Normalmente, a carga de padrão para uma réplica primária é maior do que a carga de padrão para as réplicas secundárias. Os números reais devem depender de suas próprias medidas.

## <a name="dynamic-load"></a>Carga dinâmica
Digamos que executa o serviço durante algum tempo. Com alguma monitorização, tenha notado que:

1. Algumas partições ou instâncias de um determinado serviço consumam mais recursos do que outras pessoas
2. Alguns serviços têm carga varia ao longo do tempo.

Há muitas coisas que podem fazer com que esses tipos de flutuações de carga. Por exemplo, os diferentes serviços ou partições estão associadas a clientes diferentes com requisitos diferentes. Também pode mudar a carga porque a quantidade de trabalho, que o serviço não varia ao longo do dia. Independentemente do motivo, normalmente, não existe um número único que pode utilizar para a predefinição. Isso é especialmente verdadeiro se quiser obter a utilização da maioria dos fora do cluster. Qualquer valor que escolher para a carga de predefinição está errada por vezes. Padrão incorreto carrega o resultado no Resource Manager de Cluster através de ou em alocar recursos. Como resultado, tiver nós que estão sobre ou abaixo do mesmo utilizados, apesar do Gestor de recursos de Cluster achar que o cluster é balanceado. Cargas de padrão ainda são boas, pois eles fornecem algumas informações para colocação inicial, mas eles não são uma história completa para cargas de trabalho real. Para capturar com precisão a mudança de requisitos de recursos, o Gestor de recursos de Cluster permite que cada objeto de serviço atualizar o seu próprio carga durante o tempo de execução. Isso é chamado de relatórios de carga dinâmica.

Relatórios de carga dinâmico permitem réplicas ou de instâncias para ajustar sua carga comunicado/alocação das métricas ao longo da respetiva duração. Uma réplica de serviço ou a instância que era frio e não fazendo nenhum trabalho normalmente reportarão que estava a utilizar as quantidades baixas de uma determinada métrica. Uma réplica ocupada ou a instância que poderia informar que estão usando mais.

Relatórios de carga por réplica ou de instância permite que o Gestor de recursos de Cluster para reorganizar os objetos de serviço individuais no cluster. Reorganizar os serviços ajuda a garantir que eles obtenham os recursos que necessitam. Serviços ocupados efetivamente obtém "recuperar" recursos de outros réplicas ou instâncias que está atualmente frio ou menos trabalho a fazer.

Dentro do Reliable Services, o código para comunicar carga dinamicamente esta aparência:

Código:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Um serviço pode reportar qualquer uma das métricas definidas para o mesmo no momento da criação. Se uma carga de relatórios de serviço para uma métrica que não está configurado para utilizar, o Service Fabric ignora desse relatório. Se existirem outras métricas informadas ao mesmo tempo, que são válidos, esses relatórios são aceites. Código de serviço pode medir e comunicar todas as métricas que ele sabe como, e operadores podem especificar a configuração da métrica de utilizar sem ter de alterar o código de serviço. 

### <a name="updating-a-services-metric-configuration"></a>A atualizar a configuração da métrica de um serviço
A lista de métricas associado ao serviço e as propriedades dessas métricas podem ser atualizadas dinamicamente, enquanto o serviço está em direto. Isso permite a experimentação e flexibilidade. Alguns exemplos de quando isto é útil são:

  - desativar uma métrica com um relatório com bugs para um serviço específico
  - reconfigurar os pesos de métricas com base no comportamento desejado
  - Ativar uma métrica de nova apenas depois do código já foi implementado e validado por meio de outros mecanismos
  - alterar a carga de padrão para um serviço com base no comportamento observado e consumo

São as principais APIs para alterar a configuração da métrica `FabricClient.ServiceManagementClient.UpdateServiceAsync` em c# e `Update-ServiceFabricService` no PowerShell. Quaisquer informações que especificar com essas APIs substitui as informações de métrica existentes para o serviço imediatamente. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Misturar os valores de carga predefinido e relatórios de carga dinâmico
Padrão de carga e cargas dinâmicas podem ser utilizadas para o mesmo serviço. Quando um serviço utiliza a carga de predefinido e relatórios de carga dinâmico, carga padrão funciona como uma estimativa até aparecem a relatórios dinâmicos. Padrão de carga é bom porque dá-Gestor de recursos do Cluster algo para trabalhar com. A carga padrão permite que o Gestor de recursos de Cluster para colocar os objetos do serviço em localizações de boa quando forem criadas. Não se for fornecida nenhuma informação de carga padrão, o posicionamento de serviços é efetivamente aleatório. Quando os relatórios de carga chegam mais tarde a colocação aleatória inicial é muitas vezes errada e o Gestor de recursos do Cluster tem de mover os serviços.

Vamos pegar nosso exemplo anterior e ver o que acontece quando adicionamos algumas métricas personalizadas e, em seguida, relatórios de carga dinâmica. Neste exemplo, utilizamos "MemoryInMb" como uma métrica de exemplo.

> [!NOTE]
> Memória é uma das métricas de sistema que pode do Service Fabric [regem o recurso](service-fabric-resource-governance.md), e relatórios por conta própria é normalmente difícil. Não, na verdade, esperamos que um relatório sobre o consumo de memória; A memória é utilizada aqui como uma ajuda para saber mais sobre as capacidades do Gestor de recursos do Cluster.
>

Vamos presumem que criamos inicialmente o serviço com estado com o seguinte comando:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Como lembrete, essa sintaxe é ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Vamos ver quais um layout de cluster possível pode ser parecida com:

<center>
![Cluster equilibrado com métricas padrão e personalizados][Image2]
</center>

Algumas coisas que vale a pena observar:

* Cada réplicas secundárias dentro de uma partição podem ter seus próprios carga
* Em geral as métricas de dar uma olhada equilibradas. Para a memória, a proporção entre a carga máxima e mínima é 1,75 (o nó com o máximo de carga é N3, o menos é N2 e 28/16 = 1,75).

Existem algumas coisas que precisamos de explicar:

* O que determinar se um rácio de 1,75 foi razoável ou não? Como o Gestor de recursos de Cluster sabe se isso é bom o suficiente, ou se houver mais de trabalho?
* Quando balanceamento acontecer?
* O que significa que a memória foi a ponderada "Alto"?

## <a name="metric-weights"></a>Pesos de métrica
As mesmas métricas de controle em todos os diferentes serviços é importante. Essa visão global é o que permite que o Gestor de recursos de Cluster para monitorizar o consumo do cluster, equilibrar o consumo em todos os nós e certifique-se de que nós não passam pela capacidade. No entanto, os serviços podem ter diferentes exibições em relação a importância da mesma métrica. Além disso, num cluster com várias métricas e muitos dos serviços, soluções perfeitamente equilibradas podem não existir para todas as métricas. Como o Gestor de recursos de Cluster deve lidar com essas situações?

Métrica pesos permitem que o Gestor de recursos de Cluster para decidir como equilibrar o cluster quando existe uma resposta perfeita. Métrica pesos também permitem que o Gestor de recursos de Cluster para balancear serviços específicos de forma diferente. As métricas podem ter quatro níveis de peso diferente: Zero, baixa, média e alta. Uma métrica com uma ponderação igual a zero contribui nada ao considerar se as coisas são balanceadas ou não. No entanto, a sua carga ainda contribuem para a gestão de capacidade. As métricas com Zero peso ainda são úteis e são usadas com freqüência como parte do comportamento de serviço e monitorização do desempenho. [Este artigo](service-fabric-diagnostics-event-generation-infra.md) fornece mais informações sobre o uso de métricas para monitorização e diagnóstico dos seus serviços. 

O impacto real de pesos de métrica diferentes do cluster é que o Gestor de recursos de Cluster gera soluções diferentes. Métrica pesos informam o Gestor de recursos de Cluster que determinadas métricas são mais importantes do que outras pessoas. Quando existe uma solução perfeita o Gestor de recursos do Cluster pode preferir soluções que equilibrar as métricas ponderadas superior melhor. Se achar que um serviço uma métrica em particular for importante, que ele pode localizar o uso dessa métrica desequilibrado. Isso permite que outro serviço para obter uma distribuição uniforme de alguns métrica que é importante ao mesmo.

Vamos examinar um exemplo de alguns relatórios de carga e a métrica como diferente pesos resultados em alocações de diferentes no cluster. Neste exemplo, podemos ver que alternar o peso relativo das métricas faz com que o Gestor de recursos de Cluster para criar esquemas diferentes de serviços.

<center>
![Exemplo de métrica de peso e seu impacto no balanceamento de soluções][Image3]
</center>

Neste exemplo, existem quatro serviços diferentes, todos os relatórios de valores diferentes para duas métricas diferentes, MetricA e MetricB. Num caso, todos os serviços definem MetricA é mais importante (peso = alta) e MetricB como sem importância (peso = baixa). Como resultado, podemos ver que o Gestor de recursos de Cluster coloca os serviços, de modo a que MetricA melhor é balanceada que MetricB. "Melhor equilibrada" significa que MetricA tem um inferior tem um desvio padrão inferior que MetricB. No segundo caso, vamos inverter os pesos de métrica. Como resultado, o Gestor de recursos de Cluster troca serviços A e B propor uma alocação onde MetricB melhor é balanceada que MetricA.

> [!NOTE]
> Métrica pesos determinam como deve balancear o Gestor de recursos do Cluster, mas não quando balanceamento deve acontecer. Para obter mais informações sobre balanceamento, confira [neste artigo](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Global pesos de métrica
Digamos que ServiceA define MetricA como de importância alta e ServiceB define o peso para MetricA como baixa ou Zero. O que é o peso real que acaba acostumar?

Existem vários pesos que são controlados por cada medição. O peso primeiro é o definida para a métrica quando o serviço é criado. O outro peso é um peso global, que é calculado automaticamente. O Gestor de recursos de Cluster utiliza os dois desses pesos quando soluções de classificação. Tendo ambos os pesos em consideração é importante. Isso permite que o Gestor de recursos de Cluster para equilibrar a cada serviço, de acordo com seus próprio prioridades e também garantir que o cluster como um todo é atribuído corretamente.

O que aconteceria se o Gestor de recursos de Cluster não se preocupa com saldo global e local? Bem, é fácil construir soluções que globalmente são balanceadas, mas o que resulta em equilíbrio de recursos fraco para serviços individuais. No exemplo a seguir, vamos examinar um serviço configurado com apenas as métricas padrão e ver o que acontece quando é considerado apenas balanceamento global:

<center>
![O impacto de uma única solução Global][Image4]
</center>

No exemplo superior com base apenas nos balanceamento global, o cluster como um todo, de fato, é balanceado. Todos os nós têm a mesma contagem de cores primárias e as réplicas de total mesmo número. No entanto, se examinar o impacto real de alocação deste não é tão bom: a perda de qualquer nó afeta uma carga de trabalho específica desproporcional, porque demora todo seu cores primárias. Por exemplo, se o primeiro nó falhar as três cores primárias para as três partições diferentes do serviço círculo todos seria perdido. Por outro lado, os serviços de triângulo e Hexágono têm suas partições perder uma réplica. Isso faz com que sem interrupção, além de ter de recuperar a réplica de baixo.

No exemplo na parte inferior, o Gestor de recursos de Cluster distribuiu as réplicas com base no balanceamento global e por serviço. Ao calcular a pontuação da solução possibilita a maior parte o peso para a solução global e uma parte (configurável) para serviços individuais. Balanceamento global para uma métrica é calculado com base na média de métrica ponderações de cada serviço. Cada serviço seja balanceado, de acordo com seus próprio pesos de métricos definidos. Isto garante que os serviços são balanceados dentro de si próprios, de acordo com suas próprias necessidades. Como resultado, se o primeiro nó mesmo falhar a falha é distribuído por todas as partições de todos os serviços. O impacto para cada um é o mesmo.

## <a name="next-steps"></a>Passos Seguintes
- Para obter mais informações sobre como configurar os serviços, [Saiba mais sobre como configurar serviços](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- A definição de métricas de desfragmentação é uma forma de consolidar a carga em nós em vez de dispersão-lo. Para saber como configurar a desfragmentação, consulte [neste artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Para obter informações sobre como o Gestor de recursos de Cluster gere e faz o balanceamento de carga no cluster, consulte o artigo no [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- Começar do início e [obtenha uma introdução para o Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- O custo de movimento é uma maneira de sinalização o Gestor de recursos do Cluster que determinados serviços são mais caros mover-se que outros. Para saber mais sobre o custo de movimento, consulte [neste artigo](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
