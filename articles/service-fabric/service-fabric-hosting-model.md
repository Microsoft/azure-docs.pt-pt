---
title: Modelo de hospedagem de Service Fabric do Azure | Documentos da Microsoft
description: Descreve a relação entre as réplicas (ou instâncias) de um serviço do Service Fabric implementado e o processo de host de serviço.
services: service-fabric
documentationcenter: .net
author: harahma
manager: chackdan
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: d2d958a89bff40483e1cd473538f7d1a6971d266
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663286"
---
# <a name="azure-service-fabric-hosting-model"></a>Modelo de hospedagem do Azure Service Fabric
Este artigo fornece uma visão geral dos modelos fornecidos pelo Azure Service Fabric de hospedagem de aplicativos e descreve as diferenças entre o **processo partilhado** e **processo exclusivo** modelos. Ele descreve a aparência de um aplicativo implantado num nó do Service Fabric e a relação entre as réplicas (ou instâncias) do serviço e o processo de host de serviço.

Antes de prosseguir, não se esqueça de que compreende os conceitos de várias e relações explicado [modelar uma aplicação no Service Fabric][a1]. 

> [!NOTE]
> Neste artigo, a menos que explicitamente mencionada como o que significa algo diferente:
>
> - *Réplica* refere-se a ambos os uma réplica de um serviço com estado e uma instância de um serviço sem estado.
> - *CodePackage* é tratada como equivalente a uma *ServiceHost* processo que registra um *ServiceType*e as réplicas de anfitriões dos serviços de que *ServiceType*.
>

Para compreender o modelo de hospedagem, vamos examinar um exemplo. Vamos supor que temos uma *ApplicationType* 'MyAppType', que tem um *ServiceType* 'MyServiceType'. "MyServiceType" é fornecido pelos *ServicePackage* 'MyServicePackage', que tem um *CodePackage* 'MyCodePackage'. 'MyCodePackage' registra *ServiceType* MyServiceType quando é executada.

Vamos supor que temos um cluster de três nós, e podemos criar uma *aplicativo* **fabric: / App1** do tipo 'MyAppType'. Dentro desta aplicação **fabric: / App1**, vamos criar um serviço **fabric: / App1/ServiceA** do tipo 'MyServiceType'. Este serviço tem duas partições (por exemplo, **P1** e **P2**) e três réplicas por partição. O diagrama seguinte mostra a vista desta aplicação como ele acaba implementado num nó.


![Diagrama de vista do nó da aplicação implementada][node-view-one]


Service Fabric ativado 'MyServicePackage', 'MyCodePackage', que está a alojar réplicas de ambas as partições a utilizar. Todos os nós do cluster tenham a mesma exibição, uma vez que escolhemos o número de réplicas por partição seja igual ao número de nós no cluster. Vamos criar outro serviço, **fabric: / App1/ServiceB**, no aplicativo **fabric: / App1**. Este serviço tem uma partição (por exemplo, **P3**) e três réplicas por partição. O diagrama seguinte mostra a nova exibição no nó:


![Diagrama de vista do nó da aplicação implementada][node-view-two]


Service Fabric colocado a nova réplica para a partição **P3** do serviço **fabric: / App1/ServiceB** na ativação existente de 'MyServicePackage'. Agora. Vamos criar outro aplicativo **fabric: / App2** do tipo 'MyAppType'. Dentro **fabric: / App2**, criar um serviço **fabric: / App2/ServiceA**. Este serviço tem duas partições (**P4** e **P5**) e três réplicas por partição. O diagrama seguinte mostra a nova exibição de nó:


![Diagrama de vista do nó da aplicação implementada][node-view-three]


Service Fabric ativa uma nova cópia do 'MyServicePackage', que inicia uma nova cópia do 'MyCodePackage'. Réplicas de ambas as partições do serviço **fabric: / App2/ServiceA** (**P4** e **P5**) são colocados nesta nova cópia 'MyCodePackage'.

## <a name="shared-process-model"></a>Partilhado do modelo de processo
A seção anterior descreve o padrão fornecido pelo Service Fabric, conhecido como o modelo de processo partilhado do modelo de alojamento. Nesse modelo, para um determinado aplicativo, apenas uma cópia de um determinado *ServicePackage* é ativado num nó (que inicia a todos a *CodePackages* contidas no mesmo). Todas as réplicas de todos os serviços de um determinado *ServiceType* são colocados no *CodePackage* que registra que *ServiceType*. Em outras palavras, todas as réplicas de todos os serviços num nó de um determinado *ServiceType* partilhar o mesmo processo.

## <a name="exclusive-process-model"></a>Modelo de processo exclusivo
O outro modelo de Host fornecido pelo Service Fabric é o modelo de processo exclusivo. Nesse modelo, num determinado nó, cada réplica reside em seu próprio processo dedicado. Service Fabric é uma nova cópia do ativado *ServicePackage* (que inicia a todos os *CodePackages* contidas no mesmo). Réplicas são colocadas no *CodePackage* que registrou o *ServiceType* do serviço ao qual pertence a réplica. 

Se estiver a utilizar o Service Fabric versão 5.6 ou posterior, pode escolher o modelo de processo exclusivo no momento criar um serviço (usando [PowerShell][p1], [REST] [ r1], ou [FabricClient][c1]). Especifique **ServicePackageActivationMode** como 'ExclusiveProcess'.

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Se tiver um serviço predefinido no seu manifesto de aplicação, pode escolher o modelo de processo exclusivo, especificando o **ServicePackageActivationMode** atributo:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Agora vamos criar outro serviço, **fabric: / App1/ServiceC**, no aplicativo **fabric: / App1**. Este serviço tem duas partições (por exemplo, **P6** e **P7**) e três réplicas por partição. Definir **ServicePackageActivationMode** para 'ExclusiveProcess'. O diagrama seguinte mostra o novo modo de exibição no nó:


![Diagrama de vista do nó da aplicação implementada][node-view-four]


Como pode ver, o Service Fabric ativado duas novas cópias dos "MyServicePackage" (um para cada réplica da partição **P6** e **P7**). Service Fabric colocados cada réplica em sua cópia dedicada da *CodePackage*. Quando utiliza o modelo de processo exclusivo, para um determinado aplicativo, diversas cópias de um determinado *ServicePackage* pode estar ativa num nó. No exemplo anterior, três cópias dos 'MyServicePackage' estão ativas para **fabric: / App1**. Cada essas cópias ativas da 'MyServicePackage' tem um **ServicePackageActivationId** associados a ele. Este ID identifica a cópia no aplicativo **fabric: / App1**.

Ao utilizar apenas o modelo de processo partilhado para um aplicativo, há apenas uma cópia ativa do *ServicePackage* num nó. O **ServicePackageActivationId** para esta ativação de *ServicePackage* é uma cadeia vazia. Esse é o caso, por exemplo, com **fabric: / App2**.

> [!NOTE]
>- O processo partilhado, modelo de hospedagem corresponde à **ServicePackageActivationMode** é igual a **SharedProcess**. Esta é a predefinição de modelo, de hospedagem e **ServicePackageActivationMode** não tem de ser especificado no momento da criação do serviço.
>
>- O processo exclusivo de modelo de hospedagem corresponde à **ServicePackageActivationMode** é igual a **ExclusiveProcess**. Para utilizar esta definição, deve especificá-lo explicitamente no momento da criação do serviço. 
>
>- Para ver o modelo de hospedagem de um serviço, consulte a [descrição do serviço][p2]e observe o valor de **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Trabalhar com um pacote de serviço implementado
Uma cópia ativa de uma *ServicePackage* num nó é referido como um [implementado o pacote de serviço][p3]. Quando utiliza o modelo de processo exclusivo para a criação de serviços, para um determinado aplicativo, podem existir vários pacotes de serviço implementado para o mesmo *ServicePackage*. Se estiver a efetuar operações específicas a um pacote de serviço implementado, deve fornecer **ServicePackageActivationId** para identificar um pacote de serviço implementado específico. Por exemplo, forneça o ID, se estiver [reporting o estado de funcionamento de um pacote de serviço implementado] [ p4] ou [reiniciar o pacote do código de um pacote de serviço implementado] [p5].

Pode descobrir o **ServicePackageActivationId** de um pacote de serviço implementado através da consulta a lista de [implementado os pacotes de serviço] [ p3] num nó. Quando estiver a consultar para o [implementado tipos de serviço][p6], [implementado réplicas][p7], e [implementado os pacotes do código ] [ p8] num nó, o resultado da consulta também contém o **ServicePackageActivationId** do pacote de serviço principal implementada.

> [!NOTE]
>- Sob o modelo de host de processo partilhado, num determinado nó, para um determinado aplicativo, apenas um copiar de uma *ServicePackage* é ativado. Ele tem um **ServicePackageActivationId** igual a *vazio cadeia*e não tem de ser especificado ao executar operações relacionadas com o pacote de serviço implementado. 
>
> - Sob o processo exclusivo de alojamento de modelo, num determinado nó, para um determinado aplicativo, um ou mais cópias de um *ServicePackage* pode estar ativa. Cada ativação tem um *não vazio* **ServicePackageActivationId**, especificado ao executar operações relacionadas com o pacote de serviço implementado. 
>
> - Se **ServicePackageActivationId** for omitido, é assumida como predefinição para *vazio cadeia*. Se um pacote de serviço implementado que foi ativado sob o modelo de processo partilhado estiver presente, será possível efetuar a operação no mesmo. Caso contrário, a operação falha.
>
> - Não consultar uma vez e cache a **ServicePackageActivationId**. O ID é gerado dinamicamente e pode alterar por vários motivos. Antes de efetuar uma operação que precisa **ServicePackageActivationId**, primeiro deve consultar a lista de [implementado os pacotes de serviço] [ p3] num nó. Em seguida, utilize o **ServicePackageActivationId** do resultado da consulta para executar a operação original.
>
>

## <a name="guest-executable-and-container-applications"></a>Aplicações de contentor e de executável de convidado
O Service Fabric trata [executável convidado] [ a2] e [contentor] [ a3] aplicativos como serviços sem estado, que são independentes. Não existe nenhum runtime do Service Fabric no *ServiceHost* (um processo ou contentor). Uma vez que estes serviços são autônomos, o número de réplicas por *ServiceHost* não é aplicável para estes serviços. A configuração mais comuns utilizada com estes serviços é a partição única, com [InstanceCount] [ c2] igual a -1 (uma cópia do código de serviço em execução em cada nó do cluster). 

A predefinição **ServicePackageActivationMode** destes serviços é **SharedProcess**, caso em que o Service Fabric ativa apenas uma cópia do *ServicePackage* num nó para um determinado aplicativo.  Isso significa que apenas uma cópia do código do serviço será executado um nó. Se pretender várias cópias do seu código de serviço para executar num nó, especifique **ServicePackageActivationMode** como **ExclusiveProcess** no momento da criação do serviço. Por exemplo, pode fazê-lo ao criar vários serviços (*Service1* ao *ServiceN*) do *ServiceType* (especificado na *ServiceManifest*), ou quando o seu serviço é particionada múltipla. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Alterar o modelo de hospedagem de um serviço existente
Atualmente, não é possível alterar o modelo de hospedagem de um serviço existente do processo partilhado para o processo exclusivos (ou vice-versa).

## <a name="choose-between-the-hosting-models"></a>Escolha entre os modelos de hospedagem
Deve avaliar qual modelo de alojamento melhor se adequa às suas necessidades. O modelo de processo partilhado utiliza recursos do sistema operativo melhor, porque os processos de menos são gerados, e várias réplicas no mesmo processo podem partilhar as portas. No entanto, se uma das réplicas tem um erro onde estes precisam de prejudicar o host de serviço, afeta todas as outras réplicas no mesmo processo.

 O modelo de processo exclusivo fornece melhor isolamento, com cada réplica em seu próprio processo. Se uma das réplicas tem um erro, ela não afeta outras réplicas. Esse modelo é útil nos casos em que compartilhamento da porta não é suportada pelo protocolo de comunicação. Isso facilita a capacidade de aplicar a governação de recursos no nível de réplica. No entanto, o processo de exclusivo consome mais recursos do sistema operativo, como ele gera um processo para cada réplica no nó.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Considerações de modelo de exclusivo modelo de processo e aplicação
Para a maioria dos aplicativos, pode modelar a aplicação no Service Fabric ao manter um *ServiceType* por *ServicePackage*. 

Para certos casos, o Service Fabric também permite que mais do que alguém *ServiceType* por *ServicePackage* (e um *CodePackage* podem registar-se mais do que um  *ServiceType*). Seguem-se alguns dos cenários em que estas configurações podem ser úteis:

- Pretende otimizar a utilização de recursos, ao gerar processos menos e ter a densidade superior de réplica por processo.
- Réplicas a partir de diferentes *ServiceTypes* precisa compartilhar alguns dados comuns que tenha uma inicialização elevada ou a memória de custos.
- Tem uma oferta de serviço gratuito, e que pretende colocar um limite na utilização de recursos ao colocar todas as réplicas do serviço no mesmo processo.

O processo exclusivo, modelo de alojamento não é coerente com um modelo de aplicativo ter várias *ServiceTypes* por *ServicePackage*. Isto acontece porque vários *ServiceTypes* por *ServicePackage* destinam-se para obter mais recursos compartilhando entre as réplicas e permite a densidade superior de réplica por processo. O modelo de processo exclusivo foi concebido para alcançar resultados diferentes.

Considere o caso de vários *ServiceTypes* por *ServicePackage*, com outro *CodePackage* registar cada *ServiceType*. Vamos supor que temos uma *ServicePackage* 'MultiTypeServicePackage', que tem dois *CodePackages*:

- "MyCodePackageA', que regista *ServiceType* 'MyServiceTypeA'.
- "MyCodePackageB', que regista *ServiceType* 'MyServiceTypeB'.

Agora, vamos supor que a criação de um aplicativo **fabric: / SpecialApp**. Dentro **fabric: / SpecialApp**, vamos criar dois serviços com o modelo de processo exclusivo a seguir:

- Serviço **fabric: / SpecialApp/ServiceA** do tipo "MyServiceTypeA", com duas partições (por exemplo, **P1** e **P2**) e três réplicas por partição.
- Serviço **fabric: / SpecialApp/ServiceB** do tipo "MyServiceTypeB", com duas partições (**P3** e **P4**) e três réplicas por partição.

Num determinado nó, ambos os serviços têm duas réplicas. Porque usamos o modelo de processo exclusivo para criar os serviços, o Service Fabric ativa uma nova cópia do "MyServicePackage" para cada réplica. Cada ativação de 'MultiTypeServicePackage' inicia uma cópia do 'MyCodePackageA' e 'MyCodePackageB'. No entanto, apenas um dos 'MyCodePackageA' ou 'MyCodePackageB' aloja a réplica para o qual "MultiTypeServicePackage" foi ativado. O diagrama seguinte mostra a vista do nó:


![Diagrama de vista de nós da aplicação implementada][node-view-five]


Na ativação de "MultiTypeServicePackage", para a réplica da partição **P1** do serviço **fabric: / SpecialApp/ServiceA**, 'MyCodePackageA' está a alojar a réplica. 'MyCodePackageB' está em execução. Da mesma forma, na ativação de 'MultiTypeServicePackage' para a réplica da partição **P3** do serviço **fabric: / SpecialApp/ServiceB**, 'MyCodePackageB' está a alojar a réplica. 'MyCodePackageA' está em execução. Por conseguinte, quanto maior o número de *CodePackages* (registar diferentes *ServiceTypes*) por *ServicePackage*, maior será a utilização de recursos com redundância. 
 
 No entanto, se criarmos os serviços **fabric: / SpecialApp/ServiceA** e **fabric: / SpecialApp/ServiceB** com o modelo de processo partilhado, o Service Fabric ativa apenas uma cópia do " MultiTypeServicePackage' para o aplicativo **fabric: / SpecialApp**. 'MyCodePackageA' aloja todas as réplicas para o serviço **fabric: / SpecialApp/ServiceA**. 'MyCodePackageB' aloja todas as réplicas para o serviço **fabric: / SpecialApp/ServiceB**. O diagrama seguinte mostra a vista do nó nesta definição: 


![Diagrama de vista de nós da aplicação implementada][node-view-six]


No exemplo anterior, pode pensar que se 'MyCodePackageA' registra 'MyServiceTypeA' e 'MyServiceTypeB', não há nenhum MyCodePackageB, e há não redundante *CodePackage* em execução. Embora seja correto, esse modelo de aplicativo não alinhar com o modelo de alojamento de processo exclusivo. Se o objetivo é colocar cada réplica em seu próprio processo dedicado, não é necessário registar ambos *ServiceTypes* do mesmo *CodePackage*. Em vez disso, simplesmente colocar cada *ServiceType* na sua própria *ServicePackage*.

## <a name="next-steps"></a>Passos Seguintes
[Empacotar um aplicativo] [ a4] e prepará-lo a implementar.

[Implantar e remover aplicativos][a5]. Este artigo descreve como utilizar o PowerShell para gerenciar as instâncias da aplicação.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
