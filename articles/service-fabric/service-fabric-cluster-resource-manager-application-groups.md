---
title: Gerenciador de recursos de Cluster Service Fabric-grupos de aplicativos
description: Visão geral da funcionalidade do grupo de aplicativos no Gerenciador de recursos de Cluster Service Fabric
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452154"
---
# <a name="introduction-to-application-groups"></a>Introdução aos grupos de aplicativos
O Gerenciador de recursos de Cluster Service Fabric normalmente gerencia recursos de cluster distribuindo a carga (representada por meio de [métricas](service-fabric-cluster-resource-manager-metrics.md)) uniformemente em todo o cluster. O Service Fabric gerencia a capacidade dos nós no cluster e o cluster como um todo por meio da [capacidade](service-fabric-cluster-resource-manager-cluster-description.md). Métricas e capacidade funcionam muito bem para muitas cargas de trabalho, mas os padrões que fazem uso intensivo de diferentes instâncias de aplicativo Service Fabric às vezes trazem requisitos adicionais. Por exemplo, talvez você queira:

- Reserve alguma capacidade nos nós no cluster para os serviços em alguma instância de aplicativo nomeada
- Limitar o número total de nós em que os serviços em uma instância de aplicativo nomeada são executados (em vez de profundi-los por todo o cluster)
- Defina as capacidades na própria instância do aplicativo nomeada para limitar o número de serviços ou o consumo total de recursos dos serviços dentro dele

Para atender a esses requisitos, o Service Fabric Gerenciador de recursos de cluster dá suporte a um recurso chamado grupos de aplicativos.

## <a name="limiting-the-maximum-number-of-nodes"></a>Limitando o número máximo de nós
O caso de uso mais simples para a capacidade do aplicativo é quando uma instância do aplicativo precisa ser limitada a um determinado número máximo de nós. Isso consolida todos os serviços nessa instância do aplicativo em um número definido de computadores. A consolidação é útil quando você está tentando prever ou limitar o uso de recursos físicos pelos serviços dentro dessa instância de aplicativo nomeada. 

A imagem a seguir mostra uma instância do aplicativo com e sem um número máximo de nós definidos:

<center>

![instância do aplicativo definindo o número máximo de nós][Image1]
</center>

No exemplo à esquerda, o aplicativo não tem um número máximo de nós definido e tem três serviços. O Gerenciador de recursos de cluster distribuiu todas as réplicas em seis nós disponíveis para obter o melhor equilíbrio no cluster (o comportamento padrão). No exemplo à direita, vemos o mesmo aplicativo limitado a três nós.

O parâmetro que controla esse comportamento é chamado de MaximumNodes. Esse parâmetro pode ser definido durante a criação do aplicativo ou atualizado para uma instância do aplicativo que já estava em execução.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

Dentro do conjunto de nós, o Gerenciador de recursos de cluster não garante quais objetos de serviço são colocados juntos ou quais nós são usados.

## <a name="application-metrics-load-and-capacity"></a>Métricas, carga e capacidade do aplicativo
Os grupos de aplicativos também permitem que você defina as métricas associadas a uma determinada instância de aplicativo nomeada e a capacidade da instância do aplicativo para essas métricas. As métricas do aplicativo permitem controlar, reservar e limitar o consumo de recursos dos serviços dentro dessa instância do aplicativo.

Para cada métrica de aplicativo, há dois valores que podem ser definidos:

- **Capacidade total do aplicativo** – essa configuração representa a capacidade total do aplicativo para uma métrica específica. O Gerenciador de recursos de cluster não permite a criação de novos serviços nesta instância do aplicativo que fariam com que o total de carga excedesse esse valor. Por exemplo, digamos que a instância do aplicativo tinha uma capacidade de 10 e já tinha uma carga de cinco. A criação de um serviço com uma carga padrão total de 10 não seria permitida.
- **Capacidade máxima do nó** – essa configuração especifica a carga total máxima para o aplicativo em um único nó. Se a carga passar por essa capacidade, o Gerenciador de recursos de cluster moverá as réplicas para outros nós para que a carga seja reduzida.


PowerShell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Reservando capacidade
Outro uso comum para grupos de aplicativos é garantir que os recursos no cluster sejam reservados para uma determinada instância do aplicativo. O espaço sempre é reservado quando a instância do aplicativo é criada.

Reservar espaço no cluster para o aplicativo ocorre imediatamente mesmo quando:
- a instância do aplicativo é criada, mas ainda não tem nenhum serviço dentro dela
- o número de serviços na instância do aplicativo é alterado toda vez 
- os serviços existem, mas não estão consumindo os recursos 

Reservar recursos para uma instância de aplicativo requer a especificação de dois parâmetros adicionais: *MinimumNodes* e *NodeReservationCapacity*

- **MinimumNodes** – define o número mínimo de nós em que a instância do aplicativo deve ser executada.  
- **NodeReservationCapacity** – essa configuração é por métrica para o aplicativo. O valor é a quantidade dessa métrica reservada para o aplicativo em qualquer nó em que os serviços nesse aplicativo são executados.

A combinação de **MinimumNodes** e **NodeReservationCapacity** garante uma reserva de carga mínima para o aplicativo no cluster. Se houver menos capacidade restante no cluster do que a reserva total necessária, a criação do aplicativo falhará. 

Vejamos um exemplo de reserva de capacidade:

<center>

![instâncias de aplicativo definindo a capacidade reservada][Image2]
</center>

No exemplo à esquerda, os aplicativos não têm nenhuma capacidade de aplicativo definida. O Gerenciador de recursos de cluster equilibra tudo de acordo com as regras normais.

No exemplo à direita, digamos que Application1 foi criado com as seguintes configurações:

- MinimumNodes definido como dois
- Uma métrica de aplicativo definida com
  - NodeReservationCapacity de 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric reserva a capacidade em dois nós para Application1 e não permite que os serviços de Application2 consumam essa capacidade, mesmo que não haja nenhuma carga sendo consumida pelos serviços dentro de Application1 no momento. Essa capacidade de aplicativo reservada é considerada consumida e conta com a capacidade restante nesse nó e dentro do cluster.  A reserva é deduzida da capacidade restante do cluster imediatamente. no entanto, o consumo reservado é deduzido da capacidade de um nó específico somente quando pelo menos um objeto de serviço é colocado nele. Essa reserva posterior permite flexibilidade e melhor utilização de recursos, já que os recursos são reservados somente em nós quando necessário.

## <a name="obtaining-the-application-load-information"></a>Obtendo as informações de carregamento do aplicativo
Para cada aplicativo que tem uma capacidade de aplicativo definida para uma ou mais métricas, você pode obter as informações sobre a carga agregada relatada por réplicas de seus serviços.

PowerShell:

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

A consulta ApplicationLoad retorna as informações básicas sobre a capacidade do aplicativo que foi especificada para o aplicativo. Essas informações incluem os nós mínimos e as informações de nós máximos e o número que o aplicativo está ocupando no momento. Ele também inclui informações sobre cada métrica de carga de aplicativo, incluindo:

* Nome da métrica: o nome da métrica.
* Capacidade de reserva: capacidade de cluster reservada no cluster para este aplicativo.
* Carga do aplicativo: carga total das réplicas filho deste aplicativo.
* Capacidade do aplicativo: valor máximo permitido da carga do aplicativo.

## <a name="removing-application-capacity"></a>Removendo a capacidade do aplicativo
Depois que os parâmetros de capacidade do aplicativo são definidos para um aplicativo, eles podem ser removidos usando as APIs do aplicativo de atualização ou os cmdlets do PowerShell. Por exemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Esse comando Remove todos os parâmetros de gerenciamento de capacidade do aplicativo da instância do aplicativo. Isso inclui MinimumNodes, MaximumNodes e as métricas do aplicativo, se houver. O efeito do comando é imediato. Depois que esse comando for concluído, o Gerenciador de recursos de cluster usará o comportamento padrão para gerenciar aplicativos. Os parâmetros de capacidade do aplicativo podem ser especificados novamente por meio de `Update-ServiceFabricApplication`/`System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Restrições de capacidade do aplicativo
Há várias restrições nos parâmetros de capacidade do aplicativo que devem ser respeitadas. Se houver erros de validação, nenhuma alteração ocorrerá.

- Todos os parâmetros de inteiro devem ser números não negativos.
- MinimumNodes nunca deve ser maior que MaximumNodes.
- Se as capacidades de uma métrica de carga forem definidas, elas deverão seguir estas regras:
  - A capacidade de reserva do nó não deve ser maior que a capacidade máxima do nó. Por exemplo, você não pode limitar a capacidade para a métrica "CPU" no nó para duas unidades e tentar reservar três unidades em cada nó.
  - Se MaximumNodes for especificado, o produto de MaximumNodes e a capacidade máxima do nó não deverão ser maiores que a capacidade total do aplicativo. Por exemplo, digamos que a capacidade máxima do nó para a métrica de carga "CPU" esteja definida como oito. Digamos também que você defina os nós máximos como 10. Nesse caso, a capacidade total do aplicativo deve ser maior que 80 para essa métrica de carga.

As restrições são impostas durante a criação e as atualizações do aplicativo.

## <a name="how-not-to-use-application-capacity"></a>Como não usar a capacidade do aplicativo
- Não tente usar os recursos do grupo de aplicativos para restringir o aplicativo a um subconjunto _específico_ de nós. Em outras palavras, você pode especificar que o aplicativo seja executado em no máximo cinco nós, mas não em quais cinco nós específicos no cluster. A restrição de um aplicativo para nós específicos pode ser obtida usando restrições de posicionamento para serviços.
- Não tente usar a capacidade do aplicativo para garantir que dois serviços do mesmo aplicativo sejam colocados nos mesmos nós. Em vez disso, use as restrições de [afinidade](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) ou de [posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como configurar serviços, [saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para saber como o Gerenciador de recursos de cluster gerencia e equilibra a carga no cluster, confira o artigo sobre [balanceamento](service-fabric-cluster-resource-manager-balancing.md) de carga
- Comece desde o início e [obtenha uma introdução ao Gerenciador de recursos de Cluster Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Para obter mais informações sobre como as métricas funcionam geralmente, leia sobre [métricas de carga de Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
- O Gerenciador de recursos de cluster tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre como [descrever um cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
