---
title: Gerir a carga de aplicativos Azure Service Fabric usando métricas
description: Saiba como configurar e utilizar métricas em Tecido de Serviço para gerir o consumo de recursos de serviço.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a7dedea2937c9cafb4216da3628aa1360ad6993
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173010"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Gestão do consumo de recursos e carga em Tecido de Serviço com métricas
*As métricas* são os recursos que os seus serviços preocupam e que são fornecidos pelos nós no cluster. Uma métrica é tudo o que pretende gerir para melhorar ou monitorizar o desempenho dos seus serviços. Por exemplo, pode ver o consumo de memória para saber se o seu serviço está sobrecarregado. Outra utilidade é descobrir se o serviço poderia mover-se para outro lugar onde a memória é menos limitada para obter um melhor desempenho.

Coisas como memória, disco e uso de CPU são exemplos de métricas. Estas métricas são métricas físicas, recursos que correspondem a recursos físicos no nó que precisam de ser geridos. As métricas também podem ser (e geralmente são) métricas lógicas. Métricas lógicas são coisas como "MyWorkQueueDepth" ou "MessagesToProcess" ou "TotalRecords". As métricas lógicas são definidas pela aplicação e correspondem indiretamente a algum consumo físico de recursos. As métricas lógicas são comuns porque pode ser difícil medir e reportar o consumo de recursos físicos numa base de serviço. A complexidade de medir e reportar as suas próprias métricas físicas é também a razão pela qual o Service Fabric fornece algumas métricas padrão.

## <a name="default-metrics"></a>Métricas padrão
Digamos que quer começar a escrever e a implementar o seu serviço. Neste momento não sabe que recursos físicos ou lógicos consome. Não há problema! O Gestor de Recursos do Cluster de Tecido de Serviço utiliza algumas métricas padrão quando não são especificadas outras métricas. São:

  - PrimaryCount - contagem de réplicas primárias no nó 
  - ReplicaCount - contagem de réplicas totais no nó
  - Conde - contagem de todos os objetos de serviço (apátridas e imponentes) no nó

| Métrica | Carga apátrida de instância | Carga Secundária Imponente | Carga Primária Imponente | Peso |
| --- | --- | --- | --- | --- |
| Contagem primária |0 |0 |1 |Alto |
| ReplicaCount |0 |1 |1 |Médio |
| de palavras |1 |1 |1 |Baixo |


Para cargas de trabalho básicas, as métricas padrão proporcionam uma distribuição decente do trabalho no cluster. No exemplo seguinte, vamos ver o que acontece quando criamos dois serviços e dependemos das métricas padrão para equilibrar. O primeiro serviço é um serviço imponente com três divisórias e uma réplica de três tamanhos. O segundo serviço é um serviço apátrida com uma divisória e uma contagem de três casos.

Eis o que obtém:

<center>

![Layout de cluster com métricas padrão][Image1]
</center>

Factos a ter em conta:
  - Réplicas primárias para o serviço imponente são distribuídas por vários nosdes
  - Réplicas para a mesma partição estão em diferentes nos acenos
  - O número total de primárias e secundários é distribuído no cluster
  - O número total de objetos de serviço são igualmente atribuídos em cada nó

Muito bem!

As métricas padrão funcionam muito bem como um começo. No entanto, as métricas padrão só irão levá-lo até agora. Por exemplo: Qual é a probabilidade de o esquema de partição que escolheste resultar numa perfeita utilização até mesmo por todas as divisórias? Qual é a hipótese de a carga para um determinado serviço ser constante ao longo do tempo, ou mesmo a mesma em várias divisórias agora?

Pode correr apenas com as métricas padrão. No entanto, fazê-lo normalmente significa que a sua utilização do cluster é mais baixa e mais desigual do que gostaria. Isto porque as métricas padrão não são adaptáveis e presumem que tudo é equivalente. Por exemplo, uma Primária que está ocupada e que não contribui "1" para a métrica PrimaryCount. No pior dos casos, a utilização apenas das métricas padrão também pode resultar em nódulos sobre-programados, resultando em problemas de desempenho. Se está interessado em tirar o máximo partido do seu cluster e evitar problemas de desempenho, precisa de usar métricas personalizadas e relatórios dinâmicos de carga.

## <a name="custom-metrics"></a>Métricas personalizadas
As métricas são configuradas numa base por instância de serviço quando se está a criar o serviço.

Qualquer métrica tem algumas propriedades que o descrevem: um nome, um peso e uma carga padrão.

* Nome métrico: O nome da métrica. O nome métrico é um identificador único para a métrica dentro do cluster a partir da perspetiva do Gestor de Recursos.
* Peso: O peso métrico define a importância desta métrica em relação às outras métricas para este serviço.
* Carga predefinida: A carga predefinida é representada de forma diferente, dependendo se o serviço é apátrida ou stateful.
  * Para serviços apátridas, cada métrica tem uma única propriedade chamada DefaultLoad
  * Para serviços estatais que define:
    * PrimaryDefaultLoad: A quantidade padrão desta métrica este serviço consome quando é primário
    * SecondaryDefaultLoad: A quantidade padrão desta métrica este serviço consome quando é secundário

> [!NOTE]
> Se definir métricas personalizadas e quiser _também_ utilizar as métricas padrão, tem de adicionar _explicitamente_ as métricas padrão para trás e definir pesos e valores para elas. Isto porque deve definir a relação entre as métricas padrão e as suas métricas personalizadas. Por exemplo, talvez se importe mais com o ConnectionCount ou com o WorkQueueDepth do que com a distribuição primária. Por predefinição, o peso da métrica PrimaryCount é elevado, pelo que pretende reduzi-lo a Médio quando adicionar as outras métricas para garantir que têm precedência.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definindo métricas para o seu serviço - um exemplo
Digamos que quer a seguinte configuração:

  - O seu serviço reporta uma métrica chamada "ConnectionCount"
  - Também pretende utilizar as métricas padrão 
  - Fez algumas medições e sabe que normalmente uma réplica primária desse serviço ocupa 20 unidades de "ConnectionCount"
  - Os secundários utilizam 5 unidades de "ConnectionCount"
  - Sabe que "ConnectionCount" é a métrica mais importante em termos de gestão do desempenho deste serviço em particular
  - Ainda queres réplicas primárias equilibradas. Equilibrar réplicas primárias é geralmente uma boa ideia, não importa o que aconteça. Isto ajuda a evitar que a perda de algum nó ou domínio de falha tenha impacto na maioria das réplicas primárias juntamente com a do mesmo. 
  - Caso contrário, as métricas padrão estão bem

Aqui está o código que escreveria para criar um serviço com esta configuração métrica:

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
> Os exemplos acima e o resto deste documento descrevem a gestão de métricas numa base de serviço por nome. Também é possível definir métricas para os seus serviços ao nível do _tipo de_ serviço. Isto é conseguido especificando-os nos seus manifestos de serviço. A definição das métricas de nível do tipo não é recomendada por várias razões. A primeira razão é que os nomes métricos são frequentemente específicos do ambiente. A menos que exista um contrato firme, não pode ter a certeza de que a métrica "Cores" num ambiente não é "MiliCores" ou "CoReS" noutros. Se as suas métricas estiverem definidas no seu manifesto, precisa de criar novos manifestos por ambiente. Isto conduz geralmente a uma proliferação de diferentes manifestos com apenas pequenas diferenças, o que pode levar a dificuldades de gestão.  
>
> As cargas métricas são geralmente atribuídas numa base por instância de serviço. Por exemplo, digamos que cria uma instância do serviço para o CustomerA que planeia usá-lo apenas de ânimo leve. Digamos também que cria outro para o CustomerB que tem uma carga de trabalho maior. Neste caso, provavelmente quereria ajustar as cargas padrão para esses serviços. Se tiver métricas e cargas definidas através de manifestos e pretender apoiar este cenário, requer diferentes tipos de aplicação e serviço para cada cliente. Os valores definidos no tempo de criação de serviços sobrepõem-se aos definidos no manifesto, para que possa usá-lo para definir os predefinidos específicos. No entanto, faz com que os valores declarados nos manifestos não correspondam aos que o serviço realmente funciona. Isto pode levar a confusão. 
>

Como lembrete: se apenas quiser usar as métricas padrão, não precisa de tocar na recolha de métricas ou fazer qualquer coisa especial ao criar o seu serviço. As métricas padrão são usadas automaticamente quando não são definidas outras. 

Agora, vamos analisar cada uma destas configurações com mais detalhes e falar sobre o comportamento que influencia.

## <a name="load"></a>Carregamento
O objetivo de definir métricas é representar alguma carga. *A carga* é a quantidade de uma determinada métrica consumida por alguma instância de serviço ou réplica num dado nó. A carga pode ser configurada em quase qualquer ponto. Por exemplo:

  - A carga pode ser definida quando um serviço é criado. Este tipo de configuração de carga chama-se _carga predefinida_.
  - As informações métricas, incluindo cargas predefinidos, para um serviço podem ser atualizadas após a criação do serviço. Esta atualização métrica é feita _através da atualização de um serviço._
  - As cargas para uma determinada partição podem ser reiniciadas para os valores predefinidos para esse serviço. Esta atualização métrica chama-se _reposição da carga de partição_.
  - A carga pode ser reportada por cada objeto de serviço, dinamicamente durante o tempo de funcionaamento. Esta atualização métrica chama-se _carga de reporte_.
  - A carga para réplicas ou instâncias de partição também pode ser atualizada reportando valores de carga através de uma chamada de API de tecido. Esta atualização métrica chama-se _carga de reporte para uma partição_.

Todas estas estratégias podem ser usadas dentro do mesmo serviço ao longo da sua vida.

## <a name="default-load"></a>Carga predefinida
*A carga predefinitiva* é a quantidade de métrica que cada objeto de serviço (instância apátrida ou réplica imponente) deste serviço consome. O Cluster Resource Manager utiliza este número para a carga do objeto de serviço até receber outras informações, como um relatório de carga dinâmico. Para serviços mais simples, a carga predefinida é uma definição estática. A carga predefinida nunca é atualizada e é utilizada durante o tempo de vida do serviço. As cargas predefinidos funcionam muito bem para cenários simples de planeamento de capacidade, onde determinadas quantidades de recursos são dedicadas a diferentes cargas de trabalho e não mudam.

> [!NOTE]
> Para obter mais informações sobre a gestão da capacidade e definição de capacidades para os nós no seu cluster, consulte [este artigo.](service-fabric-cluster-resource-manager-cluster-description.md#capacity)
> 

O Cluster Resource Manager permite que os serviços estatais especifiquem uma carga padrão diferente para as suas Primárias e Secundárias. Os serviços apátridas só podem especificar um valor que se aplica a todas as instâncias. Para serviços estatais, a carga padrão para réplicas primárias e secundárias são tipicamente diferentes, uma vez que as réplicas fazem diferentes tipos de trabalho em cada papel. Por exemplo, as primárias geralmente servem tanto leituras como escritas, e lidam com a maior parte do fardo computacional, enquanto os secundários não. Normalmente, a carga predefinida para uma réplica primária é maior do que a carga predefinida para réplicas secundárias. Os números reais devem depender das suas próprias medidas.

## <a name="dynamic-load"></a>Carga dinâmica
Digamos que tem estado a fazer o seu serviço há algum tempo. Com alguma monitorização, reparaste que:

1. Algumas divisórias ou instâncias de um determinado serviço consomem mais recursos do que outros
2. Alguns serviços têm carga que varia ao longo do tempo.

Há muitas coisas que podem causar este tipo de flutuações de carga. Por exemplo, diferentes serviços ou divisórias estão associados a diferentes clientes com diferentes requisitos. A carga também pode mudar porque a quantidade de trabalho que o serviço faz varia ao longo do dia. Independentemente da razão, normalmente não há um único número que possa usar por defeito. Isto é especialmente verdade se quiser tirar a maior utilização do aglomerado. Qualquer valor que escolha para a carga predefinida está errado algumas vezes. Cargas predefinidos incorretas resultam no Gestor de Recursos do Cluster, quer sobre quer alocando recursos. Como resultado, você tem nós que estão acima ou sob uso, mesmo que o Cluster Resource Manager pense que o cluster é equilibrado. As cargas padrão ainda são boas, uma vez que fornecem algumas informações para a colocação inicial, mas não são uma história completa para cargas de trabalho reais. Para capturar com precisão os requisitos de recursos alterados, o Cluster Resource Manager permite que cada objeto de serviço atualize a sua própria carga durante o tempo de funcionamento. Isto chama-se relatório dinâmico de carga.

Os relatórios dinâmicos de carga permitem que réplicas ou instâncias ajustem a sua carga de métricas de atribuição/reportada ao longo da sua vida útil. Uma réplica de serviço ou instância que estava fria e não estava a fazer nenhum trabalho normalmente relataria que estava a usar quantidades baixas de uma determinada métrica. Uma réplica ou instância ocupada relataria que estão a usar mais.

Reportar carga por réplica ou instância permite ao Cluster Resource Manager reorganizar os objetos de serviço individuais no cluster. A reorganização dos serviços ajuda a garantir que obtêm os recursos de que necessitam. Os serviços ocupados conseguem efetivamente "recuperar" recursos de outras réplicas ou casos que estão atualmente frios ou a fazer menos trabalho.

Dentro dos Serviços Fiáveis, o código para reportar a carga dinamicamente é assim:

Código:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Um serviço pode reportar qualquer uma das métricas definidas para ele no momento da criação. Se um serviço reportar a carga para uma métrica que não está configurada para usar, o Service Fabric ignora esse relatório. Se houver outras métricas reportadas ao mesmo tempo que sejam válidas, esses relatórios são aceites. O código de serviço pode medir e reportar todas as métricas que sabe fazer, e os operadores podem especificar a configuração métrica a utilizar sem terem de alterar o código de serviço. 

## <a name="reporting-load-for-a-partition"></a>Carga de reporte para uma partição
A secção anterior descreve como as réplicas de serviço ou casos reportam a si mesmos. Existe uma opção adicional para reportar dinamicamente a carga com o FabricClient. Ao reportar a carga para uma partição, pode apresentar-se para várias divisórias ao mesmo tempo.

Esses relatórios serão utilizados exatamente da mesma forma que os relatórios de carga provenientes das réplicas ou dos próprios casos. Os valores reportados serão válidos até que sejam reportados novos valores de carga, quer pela réplica, quer por exemplo, quer por reportar um novo valor de carga para uma partição.

Com esta API, existem várias formas de atualizar a carga no cluster:

  - Uma divisória de serviço estatal pode atualizar a sua carga de réplica primária.
  - Tanto os serviços apátridas como os serviços estatais podem atualizar a carga de todas as suas réplicas secundárias ou instâncias.
  - Tanto os serviços apátridas como os serviços estatais podem atualizar a carga de uma réplica ou instância específica num nó.

Também é possível combinar qualquer uma dessas atualizações por partição ao mesmo tempo.

A atualização das cargas para múltiplas divisórias é possível com uma única chamada API, caso em que a saída conterá uma resposta por partição. Caso a atualização da partição não seja aplicada com sucesso por qualquer motivo, serão ignoradas atualizações para essa partição e será fornecido o código de erro correspondente para uma partição direcionada:

  - PartitionNotFound - ID de partição especificada não existe.
  - Reconfiguração Gastos - A partição está atualmente a reconfigurar.
  - InvalidForStatelessServices - Foi feita uma tentativa de alterar a carga de uma réplica primária para uma partição pertencente a um serviço apátrida.
  - ReplicaDoesNotExist - Réplica ou instância secundária não existe num nó especificado.
  - InválidaOperação - Pode acontecer em dois casos: a atualização da carga para uma partição que pertence à aplicação do Sistema ou a atualização da carga prevista não está ativada.

Se alguns desses erros forem devolvidos, pode atualizar a entrada para uma partição específica e voltar a tentar a atualização para uma partição específica.

Código:

```csharp
Guid partitionId = Guid.Parse("53df3d7f-5471-403b-b736-bde6ad584f42");
string metricName0 = "CustomMetricName0";
List<MetricLoadDescription> newPrimaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 100)
};

string nodeName0 = "NodeName0";
List<MetricLoadDescription> newSpecificSecondaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 200)
};

OperationResult<UpdatePartitionLoadResultList> updatePartitionLoadResults =
    await this.FabricClient.UpdatePartitionLoadAsync(
        new UpdatePartitionLoadQueryDescription
        {
            PartitionMetricLoadDescriptionList = new List<PartitionMetricLoadDescription>()
            {
                new PartitionMetricLoadDescription(
                    partitionId,
                    newPrimaryReplicaLoads,
                    new List<MetricLoadDescription>(),
                    new List<ReplicaMetricLoadDescription>()
                    {
                        new ReplicaMetricLoadDescription(nodeName0, newSpecificSecondaryReplicaLoads)
                    })
            }
        },
        this.Timeout,
        cancellationToken);
```

Com este exemplo, irá realizar uma atualização da última carga reportada para uma partição _53df3d7f-5471-403b-b736-bde6ad584f42_. A carga de réplica primária para um _Nome métrico PersonalizadoMétrico será_ atualizada com o valor 100. Ao mesmo tempo, a carga para a mesma métrica para uma réplica secundária específica localizada no _nó NodeName0_, será atualizada com o valor 200.

### <a name="updating-a-services-metric-configuration"></a>Atualizar a configuração métrica de um serviço
A lista de métricas associadas ao serviço, e as propriedades dessas métricas podem ser atualizadas dinamicamente enquanto o serviço está ao vivo. Isto permite a experimentação e flexibilidade. Alguns exemplos de quando isto é útil são:

  - desativando uma métrica com um relatório buggy para um serviço particular
  - reconfiguração dos pesos das métricas com base no comportamento desejado
  - permitindo uma nova métrica apenas depois de o código já ter sido implementado e validado através de outros mecanismos
  - alterar a carga padrão para um serviço baseado no comportamento e consumo observados

As principais APIs para alterar a configuração métrica `FabricClient.ServiceManagementClient.UpdateServiceAsync` estão em C# e `Update-ServiceFabricService` em PowerShell. Qualquer informação que especifique com estas APIs substitui imediatamente as informações métricas existentes para o serviço. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Misturar valores de carga predefinidos e relatórios de carga dinâmicos
Carga padrão e cargas dinâmicas podem ser usadas para o mesmo serviço. Quando um serviço utiliza relatórios de carga predefinidos e dinâmicos, a carga padrão serve como estimativa até que apareçam relatórios dinâmicos. A carga predefinida é boa porque dá ao Cluster Resource Manager algo com que trabalhar. A carga predefinida permite ao Cluster Resource Manager colocar os objetos de serviço em boas localizações quando são criados. Se não for fornecida nenhuma informação de carga predefinido, a colocação de serviços é efetivamente aleatória. Quando os relatórios de carga chegam mais tarde, a colocação aleatória inicial é muitas vezes errada e o Cluster Resource Manager tem de mover os serviços.

Vamos pegar no nosso exemplo anterior e ver o que acontece quando adicionamos algumas métricas personalizadas e relatórios dinâmicos de carga. Neste exemplo, usamos o "MemoryInMb" como exemplo métrica.

> [!NOTE]
> A memória é uma das métricas do sistema que o Service Fabric pode [recorrer,](service-fabric-resource-governance.md)e denunciá-la é tipicamente difícil. Não esperamos que reporte sobre o consumo de memória; A memória é usada aqui como uma ajuda para a aprendizagem das capacidades do Cluster Resource Manager.
>

Vamos presumir que inicialmente criámos o serviço imponente com o seguinte comando:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Como lembrete, esta sintaxe é ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Vamos ver como um possível layout de cluster pode parecer:

<center>

![Cluster Equilibrado com métricas padrão e personalizadas][Image2]
</center>

Algumas coisas que valem a pena notar:

* Réplicas secundárias dentro de uma partição podem ter a sua própria carga
* No geral, as métricas parecem equilibradas. Para a Memória, a relação entre a carga máxima e mínima é de 1,75 (o nó com mais carga é N3, o mínimo é N2, e 28/16 = 1,75).

Há algumas coisas que ainda precisamos explicar:

* O que determinou se um rácio de 1,75 era razoável ou não? Como é que o Gestor de Recursos do Cluster sabe se isso é bom o suficiente ou se há mais trabalho a fazer?
* Quando é que o equilíbrio acontece?
* O que significa que a memória foi ponderada "Alta"?

## <a name="metric-weights"></a>Pesos métricos
É importante seguir as mesmas métricas em diferentes serviços. Essa visão global é o que permite ao Cluster Resource Manager acompanhar o consumo no cluster, equilibrar o consumo entre nós e garantir que os nós não ultrapassem a capacidade. No entanto, os serviços podem ter opiniões diferentes sobre a importância da mesma métrica. Além disso, num cluster com muitas métricas e muitos serviços, soluções perfeitamente equilibradas podem não existir para todas as métricas. Como deve o Cluster Resource Manager lidar com estas situações?

Os pesos métricos permitem ao Cluster Resource Manager decidir como equilibrar o cluster quando não há uma resposta perfeita. Os pesos métricos também permitem ao Cluster Resource Manager equilibrar serviços específicos de forma diferente. As métricas podem ter quatro níveis de peso diferentes: Zero, Baixo, Médio e Alto. Uma métrica com um peso de Zero não contribui para considerar se as coisas estão equilibradas ou não. No entanto, a sua carga continua a contribuir para a gestão da capacidade. As métricas com peso Zero ainda são úteis e são frequentemente usadas como parte do comportamento de serviço e monitorização do desempenho. [Este artigo](service-fabric-diagnostics-event-generation-infra.md) fornece mais informações sobre a utilização de métricas para monitorização e diagnóstico dos seus serviços. 

O impacto real de diferentes pesos métricos no cluster é que o Cluster Resource Manager gera diferentes soluções. Os pesos métricos dizem ao Cluster Resource Manager que certas métricas são mais importantes do que outras. Quando não há solução perfeita, o Cluster Resource Manager pode preferir soluções que equilibram melhor as métricas ponderadas. Se um serviço achar que uma determinada métrica não é importante, pode achar a sua utilização dessa métrica desequilibrada. Isto permite que outro serviço obtenha uma distribuição uniforme de alguma métrica que é importante para ele.

Vejamos um exemplo de alguns relatórios de carga e como diferentes pesos métricos resultam em diferentes alocações no cluster. Neste exemplo, vemos que mudar o peso relativo das métricas faz com que o Cluster Resource Manager crie diferentes arranjos de serviços.

<center>

![Exemplo de peso métrico e seu impacto em soluções de equilíbrio][Image3]
</center>

Neste exemplo, existem quatro serviços diferentes, todos reportando valores diferentes para duas métricas diferentes, MetricA e MetricB. Num dos casos, todos os serviços que definem a MetricA é o mais importante (Peso = Alto) e o MetricB como sem importância (Peso = Baixo). Como resultado, vemos que o Cluster Resource Manager coloca os serviços de modo a que a MetricA seja melhor equilibrada do que a MetricB. "Melhor equilibrado" significa que a MetricA tem um desvio padrão inferior ao da MetricB. No segundo caso, invertemos os pesos métricos. Como resultado, o Cluster Resource Manager troca os serviços A e B para chegar a uma alocação em que o MetricB é mais equilibrado do que o MetricA.

> [!NOTE]
> Os pesos métricos determinam como o Cluster Resource Manager deve equilibrar-se, mas não quando o equilíbrio deve acontecer. Para mais informações sobre o equilíbrio, confira [este artigo](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Pesos métricos globais
Digamos que o ServiceA define a MetricA como peso alto, e o ServiceB define o peso para MetricA para Baixo ou Zero. Qual é o peso real que acaba por ser usado?

Há vários pesos que são rastreados para cada métrica. O primeiro peso é o definido para a métrica quando o serviço é criado. O outro peso é um peso global, que é calculado automaticamente. O Cluster Resource Manager utiliza estes dois pesos ao marcar soluções. Ter em conta ambos os pesos é importante. Isto permite ao Cluster Resource Manager equilibrar cada serviço de acordo com as suas próprias prioridades, e também garantir que o cluster como um todo é alocado corretamente.

O que aconteceria se o Gestor de Recursos do Cluster não se importasse tanto com o equilíbrio global como com o local? Bem, é fácil construir soluções que sejam globalmente equilibradas, mas que resultem num fraco equilíbrio de recursos para serviços individuais. No exemplo seguinte, vamos olhar para um serviço configurado apenas com as métricas padrão, e ver o que acontece quando apenas o equilíbrio global é considerado:

<center>

![O impacto de uma solução global só][Image4]
</center>

No exemplo de topo baseado apenas no equilíbrio global, o cluster no seu conjunto é, de facto, equilibrado. Todos os nós têm a mesma contagem de primárias e o mesmo número de réplicas totais. No entanto, se olharmos para o impacto real desta alocação não é tão bom: a perda de qualquer nó impacta desproporcionalmente uma determinada carga de trabalho, porque retira todas as suas primárias. Por exemplo, se o primeiro nó falhar as três primárias para as três divisórias diferentes do serviço Circle, todas perder-se-iam. Inversamente, os serviços triângulo e hexagon têm as suas divisórias perdem uma réplica. Isto não causa nenhuma perturbação, a não ser ter de recuperar a réplica para baixo.

No exemplo inferior, o Cluster Resource Manager distribuiu as réplicas com base no equilíbrio global e por serviço. Ao calcular a pontuação da solução, dá a maior parte do peso à solução global, e uma porção (configurável) para serviços individuais. O saldo global de uma métrica é calculado com base na média dos pesos métricos de cada serviço. Cada serviço é equilibrado de acordo com os seus próprios pesos métricos definidos. Isto garante que os serviços são equilibrados dentro de si de acordo com as suas próprias necessidades. Como resultado, se o mesmo primeiro nó falhar, a falha é distribuída por todas as divisórias de todos os serviços. O impacto para cada um é o mesmo.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre serviços de configuração, [Saiba sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definir métricas de desfragmentação é uma forma de consolidar a carga nos nós em vez de espalhá-la. Para aprender a configurar a desfragmentação, consulte [este artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre a [carga de equilíbrio](service-fabric-cluster-resource-manager-balancing.md)
- Comece do início e [obtenha uma introdução ao Gestor](service-fabric-cluster-resource-manager-introduction.md) de Recursos do Cluster de Tecido de Serviço
- O Custo de Movimento é uma forma de sinalizar ao Cluster Resource Manager que certos serviços são mais caros de movimentar do que outros. Para saber mais sobre o custo de movimento, consulte [este artigo](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
