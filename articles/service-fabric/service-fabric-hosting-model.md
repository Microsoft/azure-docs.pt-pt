---
title: Modelo de Hospedagem de Service Fabric do Azure
description: Descreve a relação entre réplicas (ou instâncias) de um serviço de Service Fabric implantado e o processo de host de serviço.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 69c7edb08693937aad5a658e0b22b00cd2a81647
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464585"
---
# <a name="azure-service-fabric-hosting-model"></a>Modelo de Hospedagem de Service Fabric do Azure
Este artigo fornece uma visão geral dos modelos de Hospedagem de aplicativos fornecidos pelo Azure Service Fabric e descreve as diferenças entre o **processo compartilhado** e os modelos de **processo exclusivos** . Ele descreve como um aplicativo implantado procura em um nó de Service Fabric e a relação entre réplicas (ou instâncias) do serviço e o processo de host de serviço.

Antes de continuar, certifique-se de entender os vários conceitos e relações explicados em [modelar um aplicativo em Service Fabric][a1]. 

> [!NOTE]
> Neste artigo, a menos que seja explicitamente mencionado como significado de algo diferente:
>
> - A *réplica* refere-se a uma réplica de um serviço com estado e uma instância de um serviço sem estado.
> - *CodePackage* é tratado como equivalente a um processo de *ServiceHost* que registra um *ServiceType*e hospeda réplicas de serviços desse *ServiceType*.
>

Para entender o modelo de hospedagem, vamos examinar um exemplo. Digamos que tenhamos um *ApplicationType* "myapptype", que tem um *ServiceType* "myservicetype". ' Myservicetype ' é fornecido pelo *pacote* ' MyServicePackage ', que tem um *CodePackage* ' MyCodePackage '. ' MyCodePackage ' registra *ServiceType* ' myservicetype ' quando é executado.

Digamos que tenhamos um cluster de três nós e criamos um *aplicativo* **Fabric:/App1** do tipo ' myapptype '. Dentro deste aplicativo **Fabric:/App1**, criamos um Service **Fabric:/App1/servicea** do tipo ' myservicetype '. Esse serviço tem duas partições (por exemplo, **P1** e **P2**) e três réplicas por partição. O diagrama a seguir mostra a exibição desse aplicativo à medida que ele acaba sendo implantado em um nó.


![Diagrama da exibição de nó do aplicativo implantado][node-view-one]


Service Fabric ativou ' MyServicePackage ', que iniciou ' MyCodePackage ', que está hospedando réplicas de ambas as partições. Todos os nós no cluster têm a mesma exibição, porque escolhemos o número de réplicas por partição para ser igual ao número de nós no cluster. Vamos criar outro serviço, **Fabric:/App1/ServiceB**, no aplicativo **Fabric:/App1**. Esse serviço tem uma partição (por exemplo, **P3**) e três réplicas por partição. O diagrama a seguir mostra a nova exibição no nó:


![Diagrama da exibição de nó do aplicativo implantado][node-view-two]


Service Fabric colocou a nova réplica para a partição **P3** do Service **Fabric:/App1/ServiceB** na ativação existente de ' MyServicePackage '. Seguida. Vamos criar outro aplicativo **Fabric:/App2** do tipo ' myapptype '. Dentro do **Fabric:/App2**, crie um Service **Fabric:/App2/servicea**. Esse serviço tem duas partições (**P4** e **P5**) e três réplicas por partição. O diagrama a seguir mostra a nova exibição de nó:


![Diagrama da exibição de nó do aplicativo implantado][node-view-three]


Service Fabric ativa uma nova cópia de ' MyServicePackage ', que inicia uma nova cópia de ' MyCodePackage '. As réplicas de ambas as partições do Service **Fabric:/App2/servicea** (**P4** e **P5**) são colocadas nesta nova cópia ' MyCodePackage '.

## <a name="shared-process-model"></a>Modelo de processo compartilhado
A seção anterior descreve o modelo de hospedagem padrão fornecido pelo Service Fabric, chamado de modelo de processo compartilhado. Nesse modelo, para um determinado aplicativo, apenas uma cópia de um determinado *pacote* é ativada em um nó (que inicia todos os *CodePackages* contidos nele). Todas as réplicas de todos os serviços de um determinado *ServiceType* são colocadas no *CodePackage* que registra esse *ServiceType*. Em outras palavras, todas as réplicas de todos os serviços em um nó de um determinado *ServiceType* compartilham o mesmo processo.

## <a name="exclusive-process-model"></a>Modelo de processo exclusivo
O outro modelo de hospedagem fornecido pelo Service Fabric é o modelo de processo exclusivo. Nesse modelo, em um determinado nó, cada réplica reside em seu próprio processo dedicado. Service Fabric ativa uma nova cópia do *pacote de pacotes* (que inicia todos os *CodePackages* contidos nele). As réplicas são colocadas no *CodePackage* que registrou o *ServiceType* do serviço ao qual a réplica pertence. 

Se você estiver usando Service Fabric versão 5,6 ou posterior, poderá escolher o modelo de processo exclusivo no momento em que criar um serviço (usando o [PowerShell][p1], [REST][r1]ou [FabricClient][c1]). Especifique **ServicePackageActivationMode** como ' ExclusiveProcess '.

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

Se você tiver um serviço padrão no manifesto do aplicativo, poderá escolher o modelo de processo exclusivo especificando o atributo **ServicePackageActivationMode** :

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Agora, vamos criar outro serviço, **Fabric:/App1/ServiceC**, no Application **Fabric:/App1**. Esse serviço tem duas partições (por exemplo, **P6** e **P7**) e três réplicas por partição. Você define **ServicePackageActivationMode** como ' ExclusiveProcess '. O diagrama a seguir mostra a nova exibição no nó:


![Diagrama da exibição de nó do aplicativo implantado][node-view-four]


Como você pode ver, Service Fabric ativou duas novas cópias de "MyServicePackage" (uma para cada réplica da partição **P6** e **P7**). Service Fabric colocou cada réplica em sua cópia dedicada de *CodePackage*. Quando você usa o modelo de processo exclusivo, para um determinado aplicativo, várias cópias de um determinado *pacote* podem estar ativas em um nó. No exemplo anterior, três cópias de ' MyServicePackage ' estão ativas para **Fabric:/App1**. Cada uma dessas cópias ativas de ' MyServicePackage ' tem um **ServicePackageActivationId** associado a ela. Essa ID identifica a cópia no Application **Fabric:/App1**.

Quando você usa apenas o modelo de processo compartilhado para um aplicativo, há apenas uma cópia ativa do *pacote de pacotes* em um nó. O **ServicePackageActivationId** para esta ativação do *pacote de pacotes* é uma cadeia de caracteres vazia. Esse é o caso, por exemplo, com **Fabric:/App2**.

> [!NOTE]
>- O modelo de Hospedagem de processo compartilhado corresponde a **ServicePackageActivationMode** igual a **SharedProcess**. Esse é o modelo de hospedagem padrão e **ServicePackageActivationMode** não precisa ser especificado no momento da criação do serviço.
>
>- O modelo de Hospedagem de processo exclusivo corresponde a **ServicePackageActivationMode** igual a **ExclusiveProcess**. Para usar essa configuração, você deve especificá-la explicitamente no momento da criação do serviço. 
>
>- Para exibir o modelo de Hospedagem de um serviço, consulte a [Descrição do serviço][p2]e examine o valor de **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Trabalhar com um pacote de serviço implantado
Uma cópia ativa de um *pacote* de serviços em um nó é conhecida como um [pacote de serviço implantado][p3]. Quando você usa o modelo de processo exclusivo para criar serviços, para um determinado aplicativo, pode haver vários pacotes de serviço implantados para o mesmo *pacote*de serviços. Se você estiver executando operações específicas para um pacote de serviço implantado, deverá fornecer **ServicePackageActivationId** para identificar um pacote de serviço implantado específico. Por exemplo, forneça a ID se você estiver [relatando a integridade de um pacote de serviço implantado][p4] ou [reiniciando o pacote de códigos de um pacote de serviço implantado][p5].

Você pode descobrir o **ServicePackageActivationId** de um pacote de serviço implantado consultando a lista de [pacotes de serviço implantados][p3] em um nó. Quando você estiver consultando os [tipos de serviço implantados][p6], [réplicas implantadas][p7]e [pacotes de código implantados][p8] em um nó, o resultado da consulta também conterá o **ServicePackageActivationId** do pacote de serviço pai implantado.

> [!NOTE]
>- No modelo de Hospedagem de processo compartilhado, em um determinado nó, para um determinado aplicativo, apenas uma cópia de um *pacote* é ativada. Ele tem um **ServicePackageActivationId** igual à *cadeia de caracteres vazia*e não precisa ser especificado durante a execução de operações relacionadas ao pacote de serviço implantado. 
>
> - No modelo de Hospedagem de processo exclusivo, em um determinado nó, para um determinado aplicativo, uma ou mais cópias de um *pacote de pacotes* podem estar ativas. Cada ativação tem um **ServicePackageActivationId** *não vazio* , especificado durante a execução de operações relacionadas ao pacote de serviço implantado. 
>
> - Se **ServicePackageActivationId** for omitido, o padrão será uma *cadeia de caracteres vazia*. Se um pacote de serviço implantado que foi ativado no modelo de processo compartilhado estiver presente, a operação será executada nele. Caso contrário, a operação falha.
>
> - Não consulte uma vez e armazene em cache o **ServicePackageActivationId**. A ID é gerada dinamicamente e pode ser alterada por vários motivos. Antes de executar uma operação que precisa de **ServicePackageActivationId**, primeiro você deve consultar a lista de [pacotes de serviço implantados][p3] em um nó. Em seguida, use o **ServicePackageActivationId** do resultado da consulta para executar a operação original.
>
>

## <a name="guest-executable-and-container-applications"></a>Aplicativos executáveis e de contêiner convidado
Service Fabric trata os aplicativos [executáveis][a2] e de [contêiner][a3] convidado como serviços sem estado, que são independentes. Não há nenhum tempo de execução de Service Fabric em *ServiceHost* (um processo ou contêiner). Como esses serviços são independentes, o número de réplicas por *ServiceHost* não é aplicável a esses serviços. A configuração mais comum usada com esses serviços é partição única, com [InstanceCount][c2] igual a-1 (uma cópia do código de serviço em execução em cada nó do cluster). 

O **ServicePackageActivationMode** padrão para esses serviços é **SharedProcess**, nesse caso Service Fabric ativa apenas uma cópia do *pacote* de serviço em um nó para um determinado aplicativo.  Isso significa que apenas uma cópia do código de serviço executará um nó. Se você quiser que várias cópias do seu código de serviço sejam executadas em um nó, especifique **ServicePackageActivationMode** como **ExclusiveProcess** no momento da criação do serviço. Por exemplo, você pode fazer isso ao criar vários serviços (*Service1* para *servicen*) do *ServiceType* (especificado no Service *manifest*) ou quando o serviço tem várias partições. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Alterar o modelo de Hospedagem de um serviço existente
No momento, você não pode alterar o modelo de Hospedagem de um serviço existente do processo compartilhado para um processo exclusivo (ou vice-versa).

## <a name="choose-between-the-hosting-models"></a>Escolha entre os modelos de hospedagem
Você deve avaliar qual modelo de hospedagem melhor atende às suas necessidades. O modelo de processo compartilhado usa melhor os recursos do sistema operacional, porque menos processos são gerados e várias réplicas no mesmo processo podem compartilhar portas. No entanto, se uma das réplicas tiver um erro onde for necessário desativar o host de serviço, afetará todas as outras réplicas no mesmo processo.

 O modelo de processo exclusivo fornece melhor isolamento, com cada réplica em seu próprio processo. Se uma das réplicas tiver um erro, ela não afetará outras réplicas. Esse modelo é útil para casos em que o compartilhamento de porta não é suportado pelo protocolo de comunicação. Ele facilita a capacidade de aplicar a governança de recursos no nível de réplica. No entanto, o processo exclusivo consome mais recursos do sistema operacional, pois gera um processo para cada réplica no nó.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Modelo de processo exclusivo e considerações de modelo de aplicativo
Para a maioria dos aplicativos, você pode modelar seu aplicativo no Service Fabric mantendo um *ServiceType* por *pacote*. 

Para determinados casos, Service Fabric também permite mais de um *ServiceType* por *pacote* de informações (e um *CodePackage* pode registrar mais de um *ServiceType*). A seguir estão alguns dos cenários em que essas configurações podem ser úteis:

- Você deseja otimizar a utilização de recursos gerando menos processos e com maior densidade de réplica por processo.
- Réplicas de diferentes *tipos de tipo* precisam compartilhar alguns dados comuns que têm um alto custo de inicialização ou de memória.
- Você tem uma oferta de serviço gratuita e deseja colocar um limite de utilização de recursos colocando todas as réplicas do serviço no mesmo processo.

O modelo de Hospedagem de processo exclusivo não é coerente com um modelo de aplicativo com vários *tipos de tipo* por *pacote*. Isso ocorre porque vários *tipos* de recurso por *pacote de pacotes* são projetados para alcançar um compartilhamento de recursos maior entre réplicas e permite maior densidade de réplica por processo. O modelo de processo exclusivo foi projetado para alcançar resultados diferentes.

Considere o caso de vários *Autotipos* por *pacote de pacotes*, com um *CodePackage* diferente registrando cada *ServiceType*. Digamos que tenhamos um *pacote* ' MultiTypeServicePackage ', que tem dois *CodePackages*:

- ' MyCodePackageA ', que registra o *ServiceType* ' myservicetypea '.
- ' MyCodePackageB ', que registra o *ServiceType* ' MyServiceTypeB '.

Agora, digamos que criamos um aplicativo, **Fabric:/SpecialApp**. Dentro do **Fabric:/SpecialApp**, criamos dois serviços a seguir com o modelo de processo exclusivo:

- Service **Fabric:/SpecialApp/servicea** do tipo ' myservicetypea ', com duas partições (por exemplo, **P1** e **P2**) e três réplicas por partição.
- Service **Fabric:/SpecialApp/ServiceB** do tipo ' MyServiceTypeB ', com duas partições (**P3** e **P4**) e três réplicas por partição.

Em um determinado nó, ambos os serviços têm duas réplicas cada. Como usamos o modelo de processo exclusivo para criar os serviços, Service Fabric ativa uma nova cópia de "MyServicePackage" para cada réplica. Cada ativação de ' MultiTypeServicePackage ' inicia uma cópia de ' MyCodePackageA ' e ' MyCodePackageB '. No entanto, apenas um de ' MyCodePackageA ' ou ' MyCodePackageB ' hospeda a réplica para a qual ' MultiTypeServicePackage ' foi ativado. O diagrama a seguir mostra a exibição de nó:


![Diagrama da exibição de nó do aplicativo implantado][node-view-five]


Na ativação de ' MultiTypeServicePackage ' para a réplica da partição **P1** do Service **Fabric:/SpecialApp/servicea**, ' MyCodePackageA ' está hospedando a réplica. ' MyCodePackageB ' está em execução. Da mesma forma, na ativação de ' MultiTypeServicePackage ' para a réplica da partição **P3** do Service **Fabric:/SpecialApp/ServiceB**, ' MyCodePackageB ' está hospedando a réplica. ' MyCodePackageA ' está em execução. Portanto, quanto maior o número de *CodePackages* (registrando diferentes *tipos de tipo*) por *pacote*de recursos, maior será o uso do recurso redundante. 
 
 No entanto, se criarmos os serviços **Fabric:/SpecialApp/servicea** e **Fabric:/SpecialApp/ServiceB** com o modelo de processo compartilhado, Service Fabric ativará apenas uma cópia de ' MultiTypeServicePackage ' para o aplicativo **Fabric:/SpecialApp**. ' MyCodePackageA ' hospeda todas as réplicas para o Service **Fabric:/SpecialApp/servicea**. ' MyCodePackageB ' hospeda todas as réplicas para o Service **Fabric:/SpecialApp/ServiceB**. O diagrama a seguir mostra a exibição de nó nesta configuração: 


![Diagrama da exibição de nó do aplicativo implantado][node-view-six]


No exemplo anterior, você pode imaginar que, se ' MyCodePackageA ' Registrar ' myservicetypea ' e ' MyServiceTypeB ' e não houver nenhum ' MyCodePackageB ', não haverá nenhum *CodePackage* redundante em execução. Embora isso esteja correto, esse modelo de aplicativo não se alinha com o modelo de Hospedagem de processo exclusivo. Se o objetivo for colocar cada réplica em seu próprio processo dedicado, você não precisará registrar os dois *tipos* do mesmo *CodePackage*. Em vez disso, basta colocar cada *ServiceType* em seu próprio *pacote*.

## <a name="next-steps"></a>Passos seguintes
[Empacote um aplicativo][a4] e prepare-o para a implantação.

[Implantar e remover aplicativos][a5]. Este artigo descreve como usar o PowerShell para gerenciar instâncias de aplicativo.

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
