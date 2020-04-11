---
title: Modelo de hospedagem de tecido de serviço Azure
description: Descreve a relação entre réplicas (ou instâncias) de um serviço de tecido de serviço implantado e o processo de hospedamento de serviço.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 82bc5068be651b05eb24efa3b05e46c1e7c1e24d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115043"
---
# <a name="azure-service-fabric-hosting-model"></a>Modelo de hospedagem de tecido de serviço Azure
Este artigo fornece uma visão geral dos modelos de hospedagem de aplicações fornecidos pela Azure Service Fabric, e descreve as diferenças entre os modelos **De processo partilhado** e processo **exclusivo.** Descreve como uma aplicação implantada se parece num nó de Tecido de Serviço, e a relação entre réplicas (ou instâncias) do serviço e o processo de hospedamento de serviço.

Antes de prosseguir, certifique-se de que compreende os vários conceitos e relações explicados no [Modelo de uma aplicação em Tecido de Serviço.][a1] 

> [!NOTE]
> Neste artigo, a menos que explicitamente mencionado como algo diferente:
>
> - *A réplica* refere-se tanto a uma réplica de um serviço apátrida como a um caso de um serviço apátrida.
> - *O CodePackage* é tratado como equivalente a um processo *serviceHost* que regista um *ServiceType*, e acolhe réplicas de serviços desse *ServiceType*.
>

Para entender o modelo de hospedagem, vamos dar um exemplo. Digamos que temos um Tipo de *Aplicação* 'MyAppType', que tem um *ServiceType* 'MyServiceType'. 'MyServiceType' é fornecido pelo *ServicePackage* 'MyServicePackage', que tem um *CódigoPacote* 'MyCodePackage'. 'MyCodePackage' regista *serviceType* 'MyServiceType' quando funciona.

Digamos que temos um cluster de três nós, e criamos um tecido de **aplicação:/App1** do tipo 'MyAppType'. *application* Dentro deste tecido de **aplicação:/App1,** criamos um tecido de **serviço:/App1/ServiceA** do tipo 'MyServiceType'. Este serviço tem duas divisórias (por exemplo, **P1** e **P2)** e três réplicas por partição. O diagrama seguinte mostra a visão desta aplicação à medida que acaba implantada num nó.


![Diagrama da visão do nó da aplicação implantada][node-view-one]


O Tecido de Serviço ativou o 'MyServicePackage', que iniciou o 'MyCodePackage', que está a alojar réplicas de ambas as divisórias. Todos os nós do cluster têm a mesma visão, porque escolhemos o número de réplicas por partição para ser igual ao número de nós no cluster. Vamos criar outro serviço, **tecido:/App1/ServiceB,** no tecido da **aplicação:/App1**. Este serviço tem uma partição (por exemplo, **P3**) e três réplicas por partição. O diagrama que se segue mostra a nova vista no nó:


![Diagrama da visão do nó da aplicação implantada][node-view-two]


O Tecido de Serviço colocou a nova réplica para a partição **P3** do tecido de **serviço:/App1/ServiceB** na ativação existente de 'MyServicePackage'. Agora, o que é que se passa vamos criar outro tecido de **aplicação:/App2** do tipo 'MyAppType'. Dentro **do tecido:/App2,** crie um tecido de **serviço:/App2/ServiceA**. Este serviço tem duas divisórias (**P4** e **P5)** e três réplicas por partição. O diagrama que se segue mostra a nova vista do nó:


![Diagrama da visão do nó da aplicação implantada][node-view-three]


Service Fabric ativa uma nova cópia do 'MyServicePackage', que inicia uma nova cópia do 'MyCodePackage'. As réplicas de ambas as divisórias do tecido de **serviço:/App2/ServiceA** **(P4** e **P5**) são colocadas nesta nova cópia 'MyCodePackage'.

## <a name="shared-process-model"></a>Modelo de processo partilhado
A secção anterior descreve o modelo de hospedagem predefinido fornecido pela Service Fabric, referido como o modelo de Processo Partilhado. Neste modelo, para uma determinada aplicação, apenas uma cópia de um determinado *ServicePackage* é ativada num nó (que inicia todos os *Códigos Embalados nele contidos).* Todas as réplicas de todos os serviços de um determinado *ServiceType* são colocadas no *CodePackage* que regista esse *ServiceType*. Por outras palavras, todas as réplicas de todos os serviços num nó de um determinado *ServiceType* partilham o mesmo processo.

## <a name="exclusive-process-model"></a>Modelo de Processo Exclusivo
O outro modelo de hospedagem fornecido pela Service Fabric é o modelo Processo Exclusivo. Neste modelo, num dado nó, cada réplica vive no seu próprio processo dedicado. O Serviço Fabric ativa uma nova cópia do *ServicePackage* (que inicia todos os *CódigoS Embalados nele contidos).* As réplicas são colocadas no *CodePackage* que registou o Tipo de *Serviço* do serviço a que pertence a réplica. 

Se estiver a utilizar o Service Fabric versão 5.6 ou posterior, pode escolher o modelo Processo Exclusivo no momento em que criar um serviço (utilizando [PowerShell,][p1] [REST][r1]ou [FabricClient).][c1] Especifique o Modo de Ativação do **ServicePackage** como 'ExclusiveProcess'.

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

Se tiver um serviço predefinido no manifesto de aplicação, pode escolher o modelo De Processo Exclusivo especificando o atributo **ServicePackageActivationMode:**

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Agora vamos criar outro serviço, **tecido:/App1/ServiceC,** no tecido da **aplicação:/App1**. Este serviço tem duas divisórias (por exemplo, **P6** e **P7)** e três réplicas por partição. Definiu o **ServicePackageActivationMode** para 'ExclusiveProcess'. O diagrama seguinte mostra uma nova vista no nó:


![Diagrama da visão do nó da aplicação implantada][node-view-four]


Como pode ver, o Service Fabric ativou duas novas cópias de 'MyServicePackage' (uma para cada réplica da partição **P6** e **P7**). O Tecido de Serviço colocou cada réplica na sua cópia dedicada do *CodePackage*. Quando utiliza o modelo Processo Exclusivo, para uma determinada aplicação, várias cópias de um determinado *ServicePackage* podem estar ativas num nó. No exemplo anterior, três cópias de 'MyServicePackage' estão ativas para **tecido:/App1**. Cada uma destas cópias ativas do 'MyServicePackage' tem um **ServicePackageActivationId** associado ao mesmo. Este ID identifica essa cópia dentro do tecido da **aplicação:/App1**.

Quando utiliza apenas o modelo De processo partilhado para uma aplicação, existe apenas uma cópia ativa do *ServicePackage* num nó. O **ServicePackageActivationId** para esta ativação do *ServicePackage* é uma cadeia vazia. É o caso, por exemplo, com **tecido:/App2**.

> [!NOTE]
>- O modelo de hospedagem do Processo Partilhado corresponde ao **ServicePackageActivationMode** igual ao **SharedProcess**. Este é o modelo de hospedagem predefinido, e o **ServicePackageActivationMode** não precisa de ser especificado no momento da criação do serviço.
>
>- O modelo de hospedagem do Processo Exclusivo corresponde ao **ServicePackageActivationMode** igual a **ExclusiveProcess**. Para utilizar esta definição, deve especificá-la explicitamente no momento da criação do serviço. 
>
>- Para ver o modelo de hospedagem de um serviço, consulte a [descrição][p2]do serviço , e veja o valor do **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Trabalhar com um pacote de serviço sintetizado
Uma cópia ativa de um *ServicePackage* num nó é referida como um pacote de [serviço implantado][p3]. Quando utiliza o modelo De Processo Exclusivo para a criação de serviços, para uma determinada aplicação, pode haver vários pacotes de serviços implantados para o mesmo *ServicePackage*. Se estiver a realizar operações específicas de um pacote de serviços implantado, deve fornecer o **ServicePackageActivationId** para identificar um pacote de serviço si. Por exemplo, forneça o ID se estiver [a reportar a saúde de um pacote de serviços implantado][p4] ou a reiniciar o pacote de código de um pacote de [serviços implantado][p5].

Pode descobrir o **ServicePackageActivationId** de um pacote de serviço sionante consultando a lista de pacotes de [serviços implantados][p3] num nó. Quando está a consultar os tipos de serviço [implantados, réplicas implantadas][p7]e pacotes de [código implantados][p8] num nó, o resultado da consulta também contém o **ServicePackageActivationId** do pacote de serviço si. [deployed service types][p6]

> [!NOTE]
>- No âmbito do modelo de hospedagem do Processo Partilhado, num dado nó, para uma determinada aplicação, apenas é ativada uma cópia de um *ServicePackage.* Possui um **ServicePackageActivationId** igual a *uma corda vazia,* e não precisa de ser especificado durante a realização de operações relacionadas com o pacote de serviços implantado. 
>
> - No âmbito do modelo de hospedagem do Processo Exclusivo, num dado nó, para uma determinada aplicação, uma ou mais cópias de um *ServicePackage* podem estar ativas. Cada ativação tem um **ServicePackageActivationId** *não vazio,* especificado durante a realização de operações relacionadas com o pacote de serviços implantado. 
>
> - Se o **ServicePackageActivationId** for omitido, não se desfaz da *cadeia vazia*. Se estiver presente um pacote de serviço implantado que tenha sido ativado no âmbito do modelo De processo partilhado, a operação será executada no mesmo. Caso contrário, a operação falha.
>
> - Não faça consulta uma única vez e cache o **ServicePackageActivationId**. O ID é gerado dinamicamente, e pode mudar por várias razões. Antes de realizar uma operação que necessite de **ServicePackageActivationId,** deve primeiro consultar a lista de pacotes de [serviços implantados][p3] num nó. Em seguida, utilize o **ServicePackageActivationId** a partir do resultado da consulta para executar a operação original.
>
>

## <a name="guest-executable-and-container-applications"></a>Aplicações executáveis e contentores de hóspedes
Service Fabric trata as aplicações executáveis e [contentores][a3] dos [hóspedes][a2] como serviços apátridas, que são autossuficientes. Não existe tempo de execução do Tecido de Serviço no *ServiceHost* (um processo ou recipiente). Como estes serviços são autossuficientes, o número de réplicas por *ServiceHost* não é aplicável a estes serviços. A configuração mais comum usada com estes serviços é a partição única, com o [InstanceCount][c2] igual a -1 (uma cópia do código de serviço em execução em cada nó do cluster). 

O **Serviço PadrãoPacoteActivationMode** para estes serviços é **SharedProcess**, caso em que o Tecido de Serviço apenas ativa uma cópia do *ServicePackage* num nó para uma determinada aplicação.  Isto significa que apenas uma cópia do código de serviço executará um nó. Se pretender que várias cópias do seu código de serviço sejam executadas num nó, especifique o **ServicePackageActivationMode** como **ExclusiveProcess** no momento da criação do serviço. Por exemplo, pode fazê-lo quando cria vários serviços *(Service1* to *ServiceN*) do *ServiceType* (especificado no *ServiceManifest),* ou quando o seu serviço é multi-dividido. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Alterar o modelo de hospedagem de um serviço existente
Neste momento, não é possível alterar o modelo de hospedagem de um serviço existente de Processo Partilhado para Processo Exclusivo (ou vice-versa).

## <a name="choose-between-the-hosting-models"></a>Escolha entre os modelos de hospedagem
Deve avaliar qual o modelo de hospedagem que melhor se adequa às suas necessidades. O modelo De Processo Partilhado utiliza melhor os recursos do sistema operativo, porque menos processos são gerados, e várias réplicas no mesmo processo podem partilhar portas. No entanto, se uma das réplicas tiver um erro em que precisa de derrubar o hospedeiro de serviço, isso impacta todas as outras réplicas no mesmo processo.

 O modelo Processo Exclusivo proporciona um melhor isolamento, com cada réplica no seu próprio processo. Se uma das réplicas tiver um erro, não impacta outras réplicas. Este modelo é útil para casos em que a partilha de portas não é suportada pelo protocolo de comunicação. Facilita a capacidade de aplicar a governação dos recursos a nível de réplica. No entanto, o Processo Exclusivo consome mais recursos do sistema operativo, uma vez que gera um processo para cada réplica no nó.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Considerações de modelo de processo exclusivo e modelo de aplicação
Para a maioria das aplicações, pode modelar a sua aplicação em Tecido de Serviço mantendo um *ServiceType* por *ServicePackage*. 

Para certos casos, o Tecido de Serviço também permite mais de um *ServiceType* por *ServicePackage* (e um *CodePackage* pode registar mais do que um *ServiceType*). Seguem-se alguns dos cenários em que estas configurações podem ser úteis:

- Pretende otimizar a utilização de recursos gerando menos processos e tendo uma maior densidade de réplica por processo.
- Réplicas de diferentes *ServiceTypes* precisam de partilhar alguns dados comuns que têm uma alta inicialização ou custo de memória.
- Você tem uma oferta de serviço gratuito, e você quer colocar um limite na utilização de recursos colocando todas as réplicas do serviço no mesmo processo.

O modelo de hospedagem de processo exclusivo não é coerente com um modelo de aplicação com vários *ServiceTypes* por *ServicePackage*. Isto porque vários *ServiceTypes* por *ServicePackage* são projetados para obter uma maior partilha de recursos entre réplicas, e permite uma maior densidade de réplica por processo. O modelo Processo Exclusivo foi concebido para obter resultados diferentes.

Considere o caso de vários *ServiceTypes* por *ServicePackage,* com um *Código Pacote* diferente a registar cada *ServiceType*. Digamos que temos um Pacote de *Serviços* 'MultiTypeServicePackage', que tem dois *Códigos:*

- 'MyCodePackageA', que regista o *ServiceType* 'MyServiceTypeA'.
- 'MyCodePackageB', que regista o *ServiceType* 'MyServiceTypeB'.

Agora, digamos que criamos uma aplicação, **tecido:/SpecialApp**. Dentro **do tecido:/SpecialApp,** criamos dois serviços com o modelo Processo Exclusivo:

- **Tecido de serviço:/SpecialApp/ServiceA** do tipo 'MyServiceTypeA', com duas divisórias (por exemplo, **P1** e **P2**), e três réplicas por partição.
- **Tecido de serviço:/SpecialApp/ServiceB** do tipo 'MyServiceTypeB', com duas divisórias **(P3** e **P4**) e três réplicas por partição.

Num dado nó, ambos os serviços têm duas réplicas cada. Como usamos o modelo Processo Exclusivo para criar os serviços, o Service Fabric ativa uma nova cópia de 'MyServicePackage' para cada réplica. Cada ativação do 'MultiTypeServicePackage' inicia uma cópia de 'MyCodePackageA' e 'MyCodePackageB'. No entanto, apenas um dos 'MyCodePackageA' ou 'MyCodePackageB' acolhe a réplica para a qual foi ativado o 'MultiTypeServicePackage'. O diagrama seguinte mostra a vista do nó:


![Diagrama da visão do nó da aplicação implantada][node-view-five]


Na ativação do 'MultiTypeServicePackage' para a réplica da partição **P1** do tecido de **serviço:/SpecialApp/ServiceA,**'MyCodePackageA' está a acolher a réplica. 'MyCodePackageB' está a decorrer. Da mesma forma, na ativação do 'MultiTypeServicePackage' para a réplica da partição **P3** do tecido de **serviço:/SpecialApp/ServiceB**, 'MyCodePackageB' está hospedando a réplica. 'MyCodePackageA' está a decorrer. Assim, quanto maior for o número de *Códigos Embalados* (registando diferentes Tipos de *Serviços)* por *ServicePackage,* maior é o uso de recursos redundantes. 
 
 No entanto, se criarmos o tecido dos **serviços:/SpecialApp/ServiceA** e **tecido:/SpecialApp/ServiceB** com o modelo de Processo Partilhado, o Tecido de Serviço ativa apenas uma cópia do 'MultiTypeServicePackage' para o tecido da **aplicação:/SpecialApp**. 'MyCodePackageA' acolhe todas as réplicas para o tecido de **serviço:/SpecialApp/ServiceA**. 'MyCodePackageB' acolhe todas as réplicas para o tecido de **serviço:/SpecialApp/ServiceB**. O diagrama seguinte mostra a vista do nó nesta definição: 


![Diagrama da visão do nó da aplicação implantada][node-view-six]


No exemplo anterior, pode pensar-se que se o 'MyCodePackageA' registar tanto 'MyServiceTypeA' como 'MyServiceTypeB', e não existir 'MyCodePackageB', não existe um *Código Desativado.* Embora isto esteja correto, este modelo de aplicação não se alinha com o modelo de hospedagem do Processo Exclusivo. Se o objetivo é colocar cada réplica no seu próprio processo dedicado, não precisa de registar ambos os Tipos de *Serviços* do mesmo *CódigoPacote*. Em vez disso, basta colocar cada *ServiceType* no seu próprio *ServicePackage*.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Serviços fiáveis e ator forking subprocessos

O Service Fabric não suporta serviços fiáveis e, posteriormente, atores fiáveis que forem para subprocessos. Um exemplo do porquê do seu não suportado é [o CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) não pode ser usado para registar um subprocesso não suportado, e fichas de cancelamento são enviadas apenas para processos registados; resultando em todo o tipo de problemas, tais como falhas de upgrade, quando os subprocessos não fecham após o processo dos pais ter recebido um sinal de cancelamento.

## <a name="next-steps"></a>Passos seguintes
[Embala uma aplicação][a4] e prepara-a para ser implantada.

[Implementar e remover aplicações][a5]. Este artigo descreve como usar o PowerShell para gerir casos de aplicações.

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
