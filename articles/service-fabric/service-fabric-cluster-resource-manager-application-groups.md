---
title: Gestor de recursos de cluster de tecido de serviço - Grupos de aplicações
description: Visão geral da funcionalidade do Grupo de Aplicação no Gestor de Recursos de Cluster de Tecidos de Serviço
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452154"
---
# <a name="introduction-to-application-groups"></a>Introdução aos grupos de aplicação
O Gestor de Recursos cluster da Service Fabric normalmente gere os recursos de cluster espalhando a carga (representada via [Métricas)](service-fabric-cluster-resource-manager-metrics.md)uniformemente em todo o cluster. O Serviço Fabric gere a capacidade dos nós no cluster e do cluster como um todo através [da capacidade](service-fabric-cluster-resource-manager-cluster-description.md). As métricas e a capacidade funcionam bem para muitas cargas de trabalho, mas padrões que fazem uso pesado de diferentes instâncias de aplicação de tecido de serviço às vezes trazem requisitos adicionais. Por exemplo, pode querer:

- Reserve alguma capacidade nos nós do cluster para os serviços dentro de alguma instância de candidatura nomeada
- Limite o número total de nós que os serviços de uma instância de candidatura nomeada executam (em vez de os espalhar por todo o cluster)
- Definir capacidades na própria instância de candidatura nomeada para limitar o número de serviços ou o consumo total de recursos dos serviços no seu interior

Para satisfazer estes requisitos, o Gestor de Recursos de Cluster de Tecidos de Serviço suporta uma funcionalidade chamada Grupos de Aplicação.

## <a name="limiting-the-maximum-number-of-nodes"></a>Limitando o número máximo de nós
O caso de utilização mais simples para a capacidade de aplicação é quando uma instância de aplicação precisa de ser limitada a um certo número máximo de nós. Isto consolida todos os serviços dentro dessa instância de aplicação em um conjunto de máquinas. A consolidação é útil quando se está a tentar prever ou limitar o uso de recursos físicos pelos serviços dentro dessa instância de candidatura nomeada. 

A imagem seguinte mostra uma instância de aplicação com e sem um número máximo de nós definidos:

<center>

![Instância de candidatura que define o número máximo de nós][Image1]
</center>

No exemplo esquerdo, a aplicação não tem um número máximo de nós definidos, e tem três serviços. O Cluster Resource Manager espalhou todas as réplicas em seis nós disponíveis para obter o melhor equilíbrio no cluster (o comportamento padrão). No exemplo certo, vemos a mesma aplicação limitada a três nós.

O parâmetro que controla este comportamento chama-se MaximumNodes. Este parâmetro pode ser definido durante a criação de candidaturas, ou atualizado para uma instância de candidatura que já estava em execução.

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

Dentro do conjunto de nós, o Gestor de Recursos de Cluster não garante quais os objetos de serviço colocados juntos ou quais os nós que são usados.

## <a name="application-metrics-load-and-capacity"></a>Métricas de aplicação, carga e capacidade
Os Grupos de Aplicação também permitem definir métricas associadas a uma determinada instância de aplicação nomeada, e a capacidade dessa instância de aplicação para essas métricas. As métricas de aplicação permitem-lhe rastrear, reservar e limitar o consumo de recursos dos serviços dentro dessa instância de aplicação.

Para cada métrica de aplicação, existem dois valores que podem ser definidos:

- **Capacidade total de aplicação** – Esta definição representa a capacidade total da aplicação para uma determinada métrica. O Cluster Resource Manager proíbe a criação de quaisquer novos serviços dentro desta instância de aplicação que fariam com que a carga total excedesse este valor. Por exemplo, digamos que a instância de aplicação tinha uma capacidade de 10 e já tinha carga de cinco. A criação de um serviço com uma carga padrão total de 10 seria proibida.
- **Capacidade máxima do nó** – Esta definição especifica a carga total máxima para a aplicação num único nó. Se a carga ultrapassar esta capacidade, o Cluster Resource Manager move réplicas para outros nós para que a carga diminua.


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

## <a name="reserving-capacity"></a>Capacidade de reserva
Outra utilização comum para os grupos de aplicação é garantir que os recursos dentro do cluster sejam reservados para uma determinada instância de aplicação. O espaço é sempre reservado quando a instância de aplicação é criada.

Reservar espaço no cluster para a aplicação acontece imediatamente mesmo quando:
- a instância de aplicação é criada, mas não tem quaisquer serviços dentro dela ainda
- o número de serviços dentro da instância de aplicação muda cada vez 
- os serviços existem, mas não estão a consumir os recursos 

Reservar recursos para uma instância de candidatura requer especificar dois parâmetros adicionais: *Nódeos mínimos* e *NodeReservationCapacity*

- **Mínimos -** Define o número mínimo de nós que a instância de aplicação deve ser executada.  
- **NodeReservationCapacidadey** - Esta definição é por métrica para a aplicação. O valor é o valor dessa métrica reservada para a aplicação em qualquer nó onde os serviços dessa aplicação funcionam.

Combinar **Nódeos Mínimos** e **NodeReservationCapacidadey** garante uma reserva mínima de carga para a aplicação dentro do cluster. Se houver menos capacidade restante no cluster do que a reserva total necessária, a criação da aplicação falha. 

Vejamos um exemplo de reserva de capacidade:

<center>

![Instâncias de aplicação que definem a capacidade reservada][Image2]
</center>

No exemplo esquerdo, as aplicações não têm qualquer Capacidade de Aplicação definida. O Cluster Resource Manager equilibra tudo de acordo com as regras normais.

No exemplo à direita, digamos que a Aplicação1 foi criada com as seguintes definições:

- Mínimos Denodes definidos para dois
- Uma métrica de aplicação definida com
  - NodeReservationCapacidadede 20

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

Serviço Fabric reserva capacidade em dois nós para Aplicação1, e não permite que os serviços da Aplicação 2 consumam essa capacidade mesmo que não haja carga está sendo consumido pelos serviços dentro da Aplicação 1 na época. Esta capacidade de aplicação reservada é considerada consumida e conta com a capacidade restante nesse nó e dentro do cluster.  A reserva é deduzida imediatamente da capacidade restante do cluster, no entanto o consumo reservado é deduzido da capacidade de um nó específico apenas quando pelo menos um objeto de serviço é colocado sobre ele. Esta reserva posterior permite flexibilidade e melhor utilização de recursos, uma vez que os recursos só são reservados em nós quando necessário.

## <a name="obtaining-the-application-load-information"></a>Obtenção da informação de carga de aplicação
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

A consulta applicationLoad devolve as informações básicas sobre a Capacidade de Aplicação especificadas para a aplicação. Estas informações incluem as informações relativas aos nós mínimos e nóes máximos, e o número que a aplicação está atualmente a ocupar. Também inclui informações sobre cada métrica de carga de aplicação, incluindo:

* Nome métrico: Nome da métrica.
* Capacidade de Reserva: Capacidade de cluster reservada no cluster para esta Aplicação.
* Carga de aplicação: Carga total das réplicas infantis desta aplicação.
* Capacidade de aplicação: Valor máximo permitido da Carga de Aplicação.

## <a name="removing-application-capacity"></a>Remoção da capacidade de aplicação
Uma vez definidos os parâmetros de capacidade de aplicação para uma aplicação, podem ser removidos usando APIs de aplicação de atualização ou cmdlets PowerShell. Por exemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Este comando remove todos os parâmetros de gestão da capacidade da Aplicação da instância de aplicação. Isto inclui Nódos mínimos, Nódeos Máximos e métricas da Aplicação, se houver. O efeito do comando é imediato. Após o completo deste comando, o Gestor de Recursos de Cluster utiliza o comportamento padrão para gerir aplicações. Os parâmetros de capacidade de `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`aplicação podem ser especificados novamente através de .

### <a name="restrictions-on-application-capacity"></a>Restrições à capacidade de aplicação
Existem várias restrições aos parâmetros de capacidade de aplicação que devem ser respeitadas. Se houver erros de validação, não haverá alterações.

- Todos os parâmetros inteiros devem ser números não negativos.
- Mínimos Nódeos nunca devem ser maiores que Os Nódeos Máximos.
- Se as capacidades para uma métrica de carga forem definidas, então devem seguir estas regras:
  - A capacidade de reserva do nó não deve ser maior do que a capacidade máxima do nó. Por exemplo, não é possível limitar a capacidade para a métrica "CPU" no nó a duas unidades e tentar reservar três unidades em cada nó.
  - Se forespecificado o MaximumNodes, o produto dos Nódos máximos e da capacidade máxima do nó não deve ser superior à capacidade total de aplicação. Por exemplo, digamos que a capacidade máxima do nó para a métrica de carga "CPU" está definida para oito. Digamos também que você definiu os nódosmáximos para 10. Neste caso, a capacidade total de aplicação deve ser superior a 80 para esta métrica de carga.

As restrições são aplicadas tanto durante a criação de aplicações como as atualizações.

## <a name="how-not-to-use-application-capacity"></a>Como não utilizar a Capacidade de Aplicação
- Não tente utilizar as funcionalidades do Grupo de Aplicação para limitar a aplicação a um subconjunto _específico_ de nódosos. Por outras palavras, pode especificar que a aplicação funciona no máximo cinco nós, mas não quais cinco nós específicos no cluster. A restrição de uma aplicação a nódosos específicos pode ser conseguida utilizando restrições de colocação para serviços.
- Não tente utilizar a Capacidade de Aplicação para garantir que dois serviços da mesma aplicação sejam colocados nos mesmos nódosos. Em vez disso, utilize restrições de [afinidade](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) ou [colocação.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="next-steps"></a>Passos seguintes
- Para mais informações sobre a configuração de serviços, [Saiba sobre a configuração de Serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre [a carga](service-fabric-cluster-resource-manager-balancing.md) de equilíbrio
- Comece do início e obtenha uma introdução ao Gestor de Recursos de Cluster de [Tecidos de Serviço](service-fabric-cluster-resource-manager-introduction.md)
- Para obter mais informações sobre como as métricas funcionam em geral, leia as [métricas](service-fabric-cluster-resource-manager-metrics.md) de carga de tecido de serviço
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
