---
title: Gestor de Recursos do Cluster de Tecidos de Serviço - Grupos de Aplicações
description: Visão geral da funcionalidade do Grupo de Aplicações no Gestor de Recursos do Cluster de Tecidos de Serviço
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75452154"
---
# <a name="introduction-to-application-groups"></a>Introdução aos Grupos de Candidaturas
O Cluster Resource Manager do Service Fabric normalmente gere os recursos do cluster espalhando a carga (representada através [de Métricas)](service-fabric-cluster-resource-manager-metrics.md)uniformemente em todo o cluster. O Service Fabric gere a capacidade dos nós no cluster e no cluster como um todo através da [capacidade.](service-fabric-cluster-resource-manager-cluster-description.md) As métricas e a capacidade funcionam muito bem para muitas cargas de trabalho, mas padrões que fazem uso pesado de diferentes Instâncias de Aplicação de Tecido de Serviço às vezes trazem requisitos adicionais. Por exemplo, pode querer:

- Reserve alguma capacidade nos nos nos mesmos do cluster para os serviços dentro de algumas instâncias de aplicação nomeadas
- Limitar o número total de nós em que os serviços num caso de aplicação nomeado funcionam (em vez de os difundir em todo o cluster)
- Definir capacidades na própria instância de aplicação nomeada para limitar o número de serviços ou o consumo total de recursos dos serviços no seu interior

Para satisfazer estes requisitos, o Service Fabric Cluster Resource Manager suporta uma funcionalidade chamada Grupos de Aplicação.

## <a name="limiting-the-maximum-number-of-nodes"></a>Limitação do número máximo de nós
O caso de utilização mais simples para a capacidade de aplicação é quando uma instância de aplicação precisa de ser limitada a um certo número máximo de nós. Isto consolida todos os serviços nessa instância de aplicação num conjunto de máquinas. A consolidação é útil quando se está a tentar prever ou limitar o uso de recursos físicos pelos serviços dentro dessa instância de aplicação nomeada. 

A imagem a seguir mostra uma instância de aplicação com e sem um número máximo de nós definidos:

<center>

![Instância de aplicação que define o número máximo de nós][Image1]
</center>

No exemplo esquerdo, a aplicação não tem um número máximo de nós definidos, e tem três serviços. O Cluster Resource Manager espalhou todas as réplicas em seis nós disponíveis para alcançar o melhor equilíbrio no cluster (o comportamento padrão). No exemplo certo, vemos a mesma aplicação limitada a três nós.

O parâmetro que controla este comportamento chama-se MaximumNodes. Este parâmetro pode ser definido durante a criação da aplicação, ou atualizado para uma instância de aplicação que já estava em execução.

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

Dentro do conjunto de nós, o Cluster Resource Manager não garante quais os objetos de serviço que são colocados juntos ou quais os nós que são usados.

## <a name="application-metrics-load-and-capacity"></a>Métricas de Aplicação, Carga e Capacidade
Os Grupos de Aplicação também permitem definir métricas associadas a uma determinada instância de aplicação, e a capacidade dessa aplicação para essas métricas. As métricas de aplicação permitem-lhe rastrear, reservar e limitar o consumo de recursos dos serviços dentro dessa aplicação.

Para cada métrica de aplicação, existem dois valores que podem ser definidos:

- **Capacidade total de aplicação** – Esta definição representa a capacidade total do pedido para uma determinada métrica. O Cluster Resource Manager não permite a criação de quaisquer novos serviços dentro desta aplicação que fariam com que a carga total excedesse este valor. Por exemplo, digamos que a aplicação tinha uma capacidade de 10 e já tinha uma carga de cinco. A criação de um serviço com uma carga total de padrão de 10 seria proibida.
- **Capacidade máxima do nó** – Esta definição especifica a carga total máxima para a aplicação num único nó. Se a carga ultrapassar esta capacidade, o Cluster Resource Manager move réplicas para outros nós de modo a que a carga diminua.


Powershell:

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

## <a name="reserving-capacity"></a>Capacidade de Reserva
Outra utilização comum para os grupos de aplicação é garantir que os recursos dentro do cluster sejam reservados para uma determinada instância de aplicação. O espaço é sempre reservado quando a aplicação é criada.

Reservar espaço no cluster para a aplicação acontece imediatamente mesmo quando:
- a instância de aplicação é criada, mas não tem nenhum serviço dentro dele ainda
- o número de serviços dentro da instância de aplicação muda cada vez 
- os serviços existem, mas não estão consumindo os recursos 

Reservar recursos para uma instância de aplicação requer especificar dois parâmetros adicionais: *Mínimos Nodes* e *NodeReservationCapacity*

- **Mínimos -** Define o número mínimo de nós que a instância de candidatura deve ser executada.  
- **NodeReservationCapacity** - Esta definição é por métrica para a aplicação. O valor é o valor dessa métrica reservada para a aplicação em qualquer nó onde os serviços nessa aplicação corram.

A combinação **de MinimumNodes** e **NodeReservationCapacity** garante uma reserva mínima de carga para a aplicação dentro do cluster. Se houver menos capacidade restante no cluster do que a reserva total necessária, a criação da aplicação falha. 

Vejamos um exemplo de reserva de capacidade:

<center>

![Instâncias de aplicação que definem a capacidade reservada][Image2]
</center>

No exemplo esquerdo, as aplicações não têm qualquer Capacidade de Aplicação definida. O Cluster Resource Manager equilibra tudo de acordo com as regras normais.

No exemplo à direita, digamos que a Aplicação1 foi criada com as seguintes definições:

- Mínimos Denodes definidos para dois
- Uma Métrica de aplicação definida com
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

O Service Fabric reserva capacidade em dois nós para a Aplicação1, e não permite que os serviços da Aplicação2 consumam essa capacidade mesmo que não existam cargas que estejam a ser consumidas pelos serviços dentro da Aplicação1 no momento. Esta capacidade de aplicação reservada é considerada consumida e conta contra a capacidade remanescente nesse nó e dentro do cluster.  A reserva é deduzida imediatamente da capacidade restante do cluster, no entanto o consumo reservado é deduzido da capacidade de um nó específico apenas quando pelo menos um objeto de serviço é colocado sobre ele. Esta reserva posterior permite flexibilidade e melhor utilização dos recursos, uma vez que os recursos só são reservados em nós quando necessário.

## <a name="obtaining-the-application-load-information"></a>Obtenção da informação sobre a carga de aplicação
Para cada aplicação que tenha uma Capacidade de Aplicação definida para uma ou mais métricas pode obter a informação sobre a carga agregada reportada por réplicas dos seus serviços.

Powershell:

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

A consulta ApplicationLoad devolve as informações básicas sobre a Capacidade de Aplicação especificadas para a aplicação. Esta informação inclui os nós mínimos e a informação de Nó máximo, e o número que a aplicação está atualmente a ocupar. Inclui também informações sobre cada métrica de carga de aplicação, incluindo:

* Nome métrico: Nome da métrica.
* Capacidade de Reserva: Capacidade de Cluster reservada no cluster para esta Aplicação.
* Carga de aplicação: Carga total das réplicas infantis desta aplicação.
* Capacidade de aplicação: Valor máximo permitido da Carga de Aplicação.

## <a name="removing-application-capacity"></a>Remoção da capacidade de aplicação
Uma vez definidos os parâmetros da capacidade de aplicação para uma aplicação, podem ser removidos utilizando APIs de aplicação de atualização ou cmdlets PowerShell. Por exemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Este comando remove todos os parâmetros de gestão da capacidade de aplicação da instância de aplicação. Isto inclui Os Mínimos, Os Nodes Máximo e as métricas da Aplicação, se houver. O efeito do comando é imediato. Após o fim deste comando, o Cluster Resource Manager utiliza o comportamento predefinido para gerir aplicações. Os parâmetros da capacidade de aplicação podem ser especificados novamente através de `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()` .

### <a name="restrictions-on-application-capacity"></a>Restrições à Capacidade de Aplicação
Existem várias restrições aos parâmetros da Capacidade de Aplicação que devem ser respeitadas. Se houver erros de validação, não ocorram alterações.

- Todos os parâmetros inteiros devem ser números não negativos.
- Os mínimos nunca devem ser superiores aos Máximos.
- Se forem definidas as capacidades para uma métrica de carga, devem seguir estas regras:
  - A capacidade de reserva do nó não deve ser maior do que a capacidade máxima do nó. Por exemplo, não é possível limitar a capacidade da métrica "CPU" no nó a duas unidades e tentar reservar três unidades em cada nó.
  - Se forem especificados Máximos, o produto de MáximaNodes e a Capacidade máxima de nó não devem ser superiores à capacidade total de aplicação. Por exemplo, digamos que a capacidade máxima do nó para a métrica de carga "CPU" está definida para oito. Digamos também que definiu os nós máximos para 10. Neste caso, a capacidade total de aplicação deve ser superior a 80 para esta métrica de carga.

As restrições são aplicadas tanto durante a criação de aplicações como atualizações.

## <a name="how-not-to-use-application-capacity"></a>Como não utilizar a Capacidade de Aplicação
- Não tente utilizar as funcionalidades do Grupo de Aplicação para limitar a aplicação a um subconjunto _específico_ de nós. Por outras palavras, pode especificar que a aplicação é executado no máximo cinco nós, mas não quais os cinco nós específicos no cluster. A restrição de uma aplicação a nós específicos pode ser alcançada usando restrições de colocação para serviços.
- Não tente utilizar a Capacidade de Aplicação para garantir que dois serviços da mesma aplicação sejam colocados nos mesmos nós. Em vez disso, utilize restrições [de afinidade](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) ou [colocação](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre serviços de configuração, [Saiba mais sobre a configuração dos serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre a [carga de equilíbrio](service-fabric-cluster-resource-manager-balancing.md)
- Comece do início e [obtenha uma introdução ao Gestor](service-fabric-cluster-resource-manager-introduction.md) de Recursos do Cluster de Tecido de Serviço
- Para obter mais informações sobre como as métricas funcionam em geral, leia-se em [Métricas de Carga de Tecido de Serviço](service-fabric-cluster-resource-manager-metrics.md)
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
