---
title: Gestor de recursos do Service Fabric Cluster - grupos de aplicações | Documentos da Microsoft
description: Descrição geral da funcionalidade do grupo de aplicações no Service Fabric Cluster Resource Manager
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e4f446ff67408ef390ba817de935c286c5b2a47e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727609"
---
# <a name="introduction-to-application-groups"></a>Introdução aos grupos de aplicações
Gestor de recursos de Cluster do Service Fabric gere, tipicamente, recursos do cluster ao propagar a carga (representado por meio [métricas](service-fabric-cluster-resource-manager-metrics.md)) uniformemente em todo o cluster. O Service Fabric gere a capacidade de nós no cluster e o cluster como um todo via [capacidade](service-fabric-cluster-resource-manager-cluster-description.md). Métricas e a capacidade funcionam muito bem para muitas cargas de trabalho, mas os padrões que utilizam diferentes instâncias da aplicação Service Fabric traga, às vezes, os requisitos adicionais. Por exemplo, talvez queira:

- Reservar alguma capacidade em nós do cluster para os serviços dentro de alguns instância da aplicação com nome
- Limitar o número total de nós que os serviços dentro de uma instância de aplicação com nome são executados em (em vez de dispersão-los ao longo de todo o cluster)
- Definir as capacidades na instância do aplicativo com o nome próprio para limitar o número de serviços ou de consumo de recursos total dos serviços no interior do mesmo

Para cumprir estes requisitos, o Gestor de recursos de Cluster do Service Fabric suporta um recurso chamado grupos de aplicações.

## <a name="limiting-the-maximum-number-of-nodes"></a>Limitar o número máximo de nós
O caso de uso mais simples para a capacidade da aplicação é quando uma instância de aplicação tem de estar limitado a um determinado número máximo de nós. Isto consolida todos os serviços dentro dessa instância do aplicativo num determinado número de máquinas. A consolidação é útil quando estiver a tentar prever ou limite de utilização do recurso físico pelos serviços de dentro dessa instância com nome de aplicação. 

A imagem seguinte mostra uma instância de aplicação com e sem um número máximo de nós definida:

<center>
![Instância da aplicação de definir o número máximo de nós][Image1]
</center>

No exemplo à esquerda, a aplicação não tem um número máximo de nós definida e ele tem três serviços. O Gestor de recursos do Cluster tem distribuídos todas as réplicas pelo seis nós disponíveis para obter o melhor equilíbrio do cluster (o comportamento padrão). O exemplo certo, podemos ver o mesmo aplicativo limitado a três nós.

O parâmetro que controla esse comportamento é chamado MaximumNodes. Este parâmetro pode ser definido durante a criação do aplicativo ou atualizado para uma instância de aplicação que já estava em execução.

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

Dentro do conjunto de nós, o Gestor de recursos de Cluster não garante os objetos de serviço são colocados em conjunto ou os nós nos acostumar.

## <a name="application-metrics-load-and-capacity"></a>Métricas de aplicativo, carga e capacidade
Grupos de aplicações também permitem-lhe definir métricas associadas com uma instância de determinado aplicativo nomeado e a capacidade dessa instância de aplicação para essas métricas. Métricas da aplicação permitem-lhe controlar, reservar e limitar o consumo de recursos dos serviços de dentro dessa instância da aplicação.

Para cada métrica da aplicação, existem dois valores que podem ser definidos:

- **Total de capacidade da aplicação** – esta definição representa a capacidade total da aplicação para uma métrica em particular. O Gestor de recursos de Cluster não permite a criação de quaisquer novos serviços dentro desta instância de aplicativo que poderia fazer com que a carga total exceder este valor. Por exemplo, digamos que a instância da aplicação tinha uma capacidade de 10 e já tinha a carga de cinco. A criação de um serviço com uma carga de padrão total de 10 seria não é permitida.
- **Capacidade máxima do nó** – esta definição especifica a carga máxima de total para a aplicação num único nó. Se a carga é feito por esta capacidade, o Gestor de recursos de Cluster move réplicas para outros nós, para que a carga diminui.


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

## <a name="reserving-capacity"></a>Reserva de capacidade
Outro uso comum de grupos de aplicações é garantir que os recursos dentro do cluster estão reservados para a instância de um determinado aplicativo. O espaço é reservado sempre quando é criada a instância da aplicação.

Reservar espaço no cluster para o aplicativo imediatamente acontece mesmo quando:
- a instância da aplicação é criada, mas ainda não tem quaisquer serviços dentro do mesmo
- o número de serviços dentro da instância de aplicação sempre que é alterado 
- os serviços existem mas não estão a consumir os recursos 

Reserva de recursos para uma instância de aplicação requer a especificação de dois parâmetros adicionais: *MinimumNodes* e *NodeReservationCapacity*

- **MinimumNodes** -define o número mínimo de nós que a instância da aplicação deve ser executado em.  
- **NodeReservationCapacity** -esta definição é por métrica para a aplicação. O valor é a quantidade dessa métrica reservada para o aplicativo em qualquer nó onde que executam os serviços nesse aplicativo.

Combinar **MinimumNodes** e **NodeReservationCapacity** garante uma reserva de carga mínima para o aplicativo dentro do cluster. Se existir seja menos capacidade restante do cluster que a reserva total necessário, a criação do aplicativo falha. 

Vejamos um exemplo de reserva de capacidade:

<center>
![Instâncias da aplicação definir a capacidade de reserva][Image2]
</center>

No exemplo à esquerda, aplicativos não têm qualquer capacidade da aplicação definida. O Gestor de recursos de Cluster faz o balanceamento de tudo, de acordo com regras normais.

No exemplo à direita, digamos que Application1 foi criado com as seguintes definições:

- MinimumNodes definida para dois
- Métrica de um aplicativo definido com
  - NodeReservationCapacity of 20

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

Recursos de infraestrutura do serviço de reserva de capacidade em dois nós para Application1 e não permitir que os serviços de Application2 para consumir essa capacidade, mesmo se houver que qualquer carga não está sendo consumida por serviços dentro Application1 no momento. Esta capacidade da aplicação reservado é considerada consumidos e conta para a capacidade restante nesse nó e dentro do cluster.  A reserva é deduzida da capacidade de cluster restante imediatamente, no entanto, o consumo reservado é deduzido da capacidade de um nó específico apenas quando o objeto, pelo menos, um serviço é colocado no mesmo. Esta reserva posterior permite flexibilidade e melhor utilização de recursos, uma vez que os recursos estão reservados apenas em nós quando necessário.

## <a name="obtaining-the-application-load-information"></a>Obter as informações de carga do aplicativo
Para cada aplicativo que tenha uma capacidade da aplicação definida para uma ou mais métricas, que pode obter as informações sobre a carga agregada comunicada pelo réplicas dos seus serviços.

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

A consulta de ApplicationLoad devolve as informações básicas sobre a capacidade da aplicação que foi especificada para a aplicação. Estas informações incluem as informações de nós de mínimo e máximo de nós e o número de que o aplicativo atualmente ocupa. Ele também inclui informações sobre cada métrica de carga do aplicativo, incluindo:

* Nome da métrica: Nome da métrica.
* Capacidade de reserva: Capacidade do cluster que está reservada no cluster para esta aplicação.
* Carga da aplicação: Carga total de réplicas de subordinado desse aplicativo.
* Capacidade da aplicação: Máximo permitido de valor de carga da aplicação.

## <a name="removing-application-capacity"></a>Remover a capacidade da aplicação
Assim que os parâmetros de capacidade da aplicação estão definidos para uma aplicação, podem ser removidos utilizando os cmdlets de APIs de aplicação de atualização ou o PowerShell. Por exemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Este comando remove todos os parâmetros de gestão de capacidade da aplicação da instância da aplicação. Isto inclui MinimumNodes MaximumNodes e métricas da aplicação, se houver. O efeito do comando é imediato. Depois de concluída este comando, o Gestor de recursos de Cluster utiliza o comportamento predefinido para a gestão de aplicações. Parâmetros da capacidade da aplicação podem ser especificados novamente `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Restrições de capacidade da aplicação
Existem várias restrições em parâmetros de capacidade da aplicação que devem ser respeitadas. Se houver erros de validação sem alterações ocorrem.

- Todos os parâmetros de número inteiro tem de ser números não negativos.
- MinimumNodes nunca deve ser superior a MaximumNodes.
- Se as capacidades para uma métrica de carga são definidas, em seguida, têm de seguir estas regras:
  - Capacidade de reserva de nós não deve ser superior a capacidade máxima de nó. Por exemplo, não é possível limitar a capacidade para a métrica "CPU" no nó para duas unidades e tentar reservar três unidades em cada nó.
  - Se MaximumNodes for especificada, em seguida, o produto de MaximumNodes e a capacidade máxima do nó não pode estar superior a capacidade Total da aplicação. Por exemplo, digamos que a capacidade máxima de nó para a métrica de carga "CPU" está definido para oito. Vamos também supor que definir o número máximo de nós para 10. Neste caso, capacidade Total da aplicação tem de ser superior a 80 para esta métrica de carga.

As restrições são impostas tanto durante a criação de aplicativos e atualizações.

## <a name="how-not-to-use-application-capacity"></a>Como não se deve utilizar a capacidade da aplicação
- Não tente usar os recursos do grupo de aplicações para restringir o aplicativo para um _específico_ subconjunto de nós. Em outras palavras, pode especificar que a aplicação é executada nos cinco nós, no máximo, mas não que específico cinco nós no cluster. Restrição de um aplicativo a nós específicos pode ser obtido com restrições de posicionamento para serviços.
- Não tente utilizar a capacidade da aplicação para se certificar de que os dois serviços a partir do mesmo aplicativo são colocados em nós do mesmo. Em vez disso, utilize [afinidade](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) ou [restrições de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Passos Seguintes
- Para obter mais informações sobre como configurar os serviços, [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para obter informações sobre como o Gestor de recursos de Cluster gere e faz o balanceamento de carga no cluster, consulte o artigo no [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- Começar do início e [obtenha uma introdução para o Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Para obter mais informações sobre como as métricas funcionam em geral, ler sobre [as métricas de carregamento de recursos de infraestrutura do serviço](service-fabric-cluster-resource-manager-metrics.md)
- O Gestor de recursos do Cluster tem muitas opções para descrever o cluster. Para obter mais informações sobre ele, visite este artigo no [descrever um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
