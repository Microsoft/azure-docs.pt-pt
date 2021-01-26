---
title: Modelo de hospedagem de tecido de serviço Azure
description: Descreve a relação entre réplicas (ou instâncias) de um serviço de Tecido de Serviço implantado e o processo de anfitrião de serviço.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.custom: devx-track-csharp
ms.openlocfilehash: 9932c11332a616928d59c213d4f4806feb81cfe2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791650"
---
# <a name="azure-service-fabric-hosting-model"></a>Modelo de hospedagem de tecido de serviço Azure
Este artigo fornece uma visão geral dos modelos de hospedagem de aplicações fornecidos pela Azure Service Fabric, e descreve as diferenças entre os modelos **processo partilhado** e **processo exclusivo.** Descreve como uma aplicação implementada se apresenta num nó de Tecido de Serviço e a relação entre réplicas (ou instâncias) do serviço e o processo de anfitrião de serviço.

Antes de prosseguir, certifique-se de que compreende os vários conceitos e relações explicados no [Modelo de Uma aplicação em Tecido de Serviço.][a1] 

> [!NOTE]
> Neste artigo, a menos que explicitamente mencionado como algo diferente:
>
> - *Réplica* refere-se tanto a uma réplica de um serviço estatal como a um caso de um serviço apátrida.
> - *O CodePackage* é tratado como equivalente a um processo *ServiceHost* que regista um *ServiceType,* e acolhe réplicas de serviços desse *ServiceType*.
>

Para entender o modelo de hospedagem, vamos percorrer um exemplo. Digamos que temos um 'MyAppType' *de AplicaçãoType,* que tem um 'MyServiceType' de *serviço.* O 'MyServiceType' é fornecido pelo *ServicePackage* 'MyServicePackage', que tem um *CodePackage* 'MyCodePackage'. 'MyCodePackage' regista *o ServiceType* 'MyServiceType' quando é executado.

Digamos que temos um cluster de três nós, e criamos um tecido de **aplicação:/App1** do tipo 'MyAppType'.  Dentro deste tecido de **aplicação:/App1,** criamos um tecido de **serviço:/App1/ServiceA** do tipo 'MyServiceType'. Este serviço tem duas divisórias (por exemplo, **P1** e **P2),** e três réplicas por partição. O diagrama seguinte mostra a visão desta aplicação à medida que acaba implantada num nó.


![Diagrama que mostra a visão desta aplicação à medida que acaba implantada num nó.][node-view-one]


O Service Fabric ativou o 'MyServicePackage', que iniciou o 'MyCodePackage', que está a alojar réplicas de ambas as divisórias. Todos os nós do cluster têm a mesma visão, porque escolhemos o número de réplicas por partição para ser igual ao número de nós no cluster. Vamos criar outro serviço, **tecido:/App1/ServiceB,** no tecido da **aplicação:/App1**. Este serviço tem uma divisória (por exemplo, **P3),** e três réplicas por partição. O seguinte diagrama mostra a nova vista sobre o nó:


![Diagrama que mostra a nova vista no nó.][node-view-two]


O Tecido de Serviço colocou a nova réplica para partição **P3** do tecido de **serviço:/App1/ServiceB** na ativação existente de 'MyServicePackage'. Agora, vamos para a 20 vamos criar outro tecido de **aplicação:/App2** do tipo 'MyAppType'. Dentro **do tecido:/App2,** crie um tecido de **serviço:/App2/ServiceA**. Este serviço tem duas divisórias **(P4** e **P5),** e três réplicas por partição. O diagrama a seguir mostra a nova vista do nó:


![Diagrama que mostra a nova vista do nó.][node-view-three]


O Service Fabric ativa uma nova cópia do 'MyServicePackage', que inicia uma nova cópia do 'MyCodePackage'. Réplicas de ambos os tecidos de **serviço:/App2/ServiceA** **(P4** e **P5**) são colocadas nesta nova cópia 'MyCodePackage'.

## <a name="shared-process-model"></a>Modelo de processo compartilhado
A secção anterior descreve o modelo de hospedagem predefinido fornecido pela Service Fabric, referido como o modelo processo partilhado. Neste modelo, para uma determinada aplicação, apenas uma cópia de um dado *ServicePackage* é ativada num nó (que inicia todos os *CodePackages nele contidos).* Todas as réplicas de todos os serviços de um determinado *ServiceType* são colocadas no *CodePackage* que regista esse *ServiceType*. Por outras palavras, todas as réplicas de todos os serviços num nó de um dado *ServiceType* partilham o mesmo processo.

## <a name="exclusive-process-model"></a>Modelo de processo exclusivo
O outro modelo de hospedagem fornecido pela Service Fabric é o modelo De Processo Exclusivo. Neste modelo, num dado nó, cada réplica vive no seu próprio processo dedicado. O Service Fabric ativa uma nova cópia do *ServicePackage* (que inicia todos os *CodePackages nele contidos).* As réplicas são colocadas no *CodePackage* que registou o *ServiceType* do serviço ao qual a réplica pertence. 

Se estiver a utilizar a versão 5.6 ou posterior do Service Fabric, pode escolher o modelo Processo Exclusivo no momento em que criar um serviço (utilizando [o PowerShell,][p1] [REST][r1]ou [FabricClient).][c1] Especifique **o ServicePackageActivationMode** como 'ExclusiveProcess'.

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

Se tiver um serviço predefinido no seu manifesto de aplicação, pode escolher o modelo de Processo Exclusivo especificando o atributo **ServicePackageActivationMode:**

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Agora vamos criar outro serviço, **tecido:/App1/ServiceC,** em tecido de **aplicação:/App1**. Este serviço tem duas divisórias (por exemplo, **P6** e **P7),** e três réplicas por partição. Definiu **o ServicePackageActivationMode** para 'ExclusiveProcess'. O seguinte diagrama mostra uma nova visão sobre o nó:


![Diagrama de vista do nó da aplicação implantada][node-view-four]


Como pode ver, o Service Fabric ativou duas novas cópias de 'MyServicePackage' (uma para cada réplica da partição **P6** e **P7).** O Tecido de Serviço colocou cada réplica na sua cópia dedicada do *CodePackage.* Quando utiliza o modelo Processo Exclusivo, para uma determinada aplicação, várias cópias de um dado *ServicePackage* podem estar ativas num nó. No exemplo anterior, três cópias de 'MyServicePackage' estão ativas para **tecido:/App1**. Cada uma destas cópias ativas do 'MyServicePackage' tem um **ServicePackageActivationId** associado. Este ID identifica essa cópia dentro do tecido da **aplicação:/App1**.

Quando utiliza apenas o modelo processo partilhado para uma aplicação, existe apenas uma cópia ativa do *ServicePackage* num nó. O **ServicePackageActivationId** para esta ativação do *ServicePackage* é uma cadeia vazia. É o caso, por exemplo, do **tecido:/App2**.

> [!NOTE]
>- O modelo de hospedagem de processo partilhado corresponde ao **ServicePackageActivationMode** igual **ao SharedProcess**. Este é o modelo de hospedagem predefinido e **o ServicePackageActivationMode** não precisa de ser especificado no momento da criação do serviço.
>
>- O modelo de hospedagem de processo exclusivo corresponde ao **ServicePackageActivationMode** igual **a ExclusiveProcess**. Para utilizar esta definição, deve especificá-la explicitamente no momento da criação do serviço. 
>
>- Para visualizar o modelo de hospedagem de um serviço, consulte a descrição do [serviço,][p2]e veja o valor do **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Trabalhar com um pacote de serviço implantado
Uma cópia ativa de um *ServicePackage* num nó é referida como um [pacote de serviço implantado.][p3] Quando utiliza o modelo de Processo Exclusivo para criar serviços, para uma determinada aplicação, poderá existir vários pacotes de serviços implantados para o mesmo *ServicePackage.* Se estiver a realizar operações específicas de um pacote de serviços implantado, deverá fornecer **ServicePackageActivationId** para identificar um pacote de serviços implantado específico. Por exemplo, forneça o ID se estiver [a reportar a saúde de um pacote de serviços implantado][p4] ou reiniciar o pacote de código de um pacote de [serviços implantado][p5].

Pode descobrir o **ServicePackageActivationId** de um pacote de serviço implantado consultando a lista de pacotes de [serviço implantados][p3] num nó. Quando está a consultar os tipos de serviço [implantados, réplicas implantadas][p7]e [pacotes de código implantados][p8] num nó, o resultado da consulta também contém o **ServicePackageActivationId** do pacote de serviços implantado pelos pais. [][p6]

> [!NOTE]
>- No modelo de hospedagem do Processo Partilhado, num dado nó, para uma determinada aplicação, apenas é ativada uma cópia de um *ServicePackage.* Tem um **ServicePackageActivationId** igual a *uma corda vazia,* e não precisa de ser especificado durante a realização de operações relacionadas com o pacote de serviço implantado. 
>
> - No âmbito do modelo de hospedagem de Processo Exclusivo, num dado nó, para uma determinada aplicação, uma ou mais cópias de um *ServicePackage* podem estar ativas. Cada ativação tem um **ServicePackageActivationId** *não vazio,* especificado durante a realização de operações relacionadas com o pacote de serviços implantado. 
>
> - Se **o ServicePackageActivationId** for omitido, por defeito de *cadeia vazia*. Se estiver presente um pacote de serviço implantado que foi ativado no âmbito do modelo Processo Partilhado, a operação será efetuada no mesmo. Caso contrário, a operação falha.
>
> - Não questione uma única vez e cache o **ServicePackageActivationId**. O ID é gerado dinamicamente, e pode mudar por várias razões. Antes de efetuar uma operação que **necessite de ServicePackageActivationId,** deverá consultar primeiro a lista de pacotes de [serviço implantados][p3] num nó. Em seguida, utilize o **ServicePackageActivationId** a partir do resultado da consulta para executar a operação original.
>
>

## <a name="guest-executable-and-container-applications"></a>Aplicações executáveis e contentores de hóspedes
A Service Fabric trata as aplicações [de hóspedes executáveis][a2] e [contentores][a3] como serviços apátridas, que são autossuficientes. Não existe tempo de funcionamento do Tecido de Serviço no *ServiceHost* (um processo ou recipiente). Como estes serviços são independentes, o número de réplicas por *ServiceHost* não é aplicável a estes serviços. A configuração mais comum utilizada com estes serviços é a partilha única, com [InstanceCount][c2] igual a -1 (uma cópia do código de serviço em execução em cada nó do cluster). 

O **ServicePackageActivationMode** predefinido para estes serviços é **SharedProcess,** caso em que o Service Fabric apenas ativa uma cópia do *ServicePackage* num nó para uma determinada aplicação.  Isto significa que apenas uma cópia do código de serviço irá executar um nó. Se pretender que várias cópias do seu código de serviço seja executada num nó, especifique **o ServicePackageActivationMode** como **ExclusiveProcess** no momento da criação do serviço. Por exemplo, pode fazê-lo quando criar vários serviços *(Service1* a *ServiceN*) de *ServiceType* (especificado no *ServiceManifest),* ou quando o seu serviço é multipartido. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Alterar o modelo de hospedagem de um serviço existente
Neste momento, não é possível alterar o modelo de hospedagem de um serviço existente de Processo Partilhado para Processo Exclusivo (ou vice-versa).

## <a name="choose-between-the-hosting-models"></a>Escolha entre os modelos de hospedagem
Deve avaliar qual o modelo de hospedagem que melhor se adequa às suas necessidades. O modelo processo partilhado utiliza melhor os recursos do sistema operativo, porque menos processos são gerados, e várias réplicas no mesmo processo podem partilhar portas. No entanto, se uma das réplicas tiver um erro em que precisa de derrubar o hospedeiro de serviço, impacta todas as outras réplicas no mesmo processo.

 O modelo Processo Exclusivo proporciona um melhor isolamento, com cada réplica no seu próprio processo. Se uma das réplicas tiver um erro, não afeta outras réplicas. Este modelo é útil para casos em que a partilha portuária não é suportada pelo protocolo de comunicação. Facilita a capacidade de aplicar a governação de recursos a nível de réplicas. No entanto, o Processo Exclusivo consome mais recursos do sistema operativo, uma vez que gera um processo para cada réplica no nó.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Considerações exclusivas do modelo de processo e do modelo de aplicação
Para a maioria das aplicações, pode modelar a sua aplicação em Service Fabric mantendo um *ServiceType* por *ServicePackage.* 

Em certos casos, o Service Fabric também permite mais de um *ServiceType* por *ServicePackage* (e um *CodePackage* pode registar mais de um *ServiceType).* Seguem-se alguns dos cenários em que estas configurações podem ser úteis:

- Pretende otimizar a utilização de recursos desovando menos processos e tendo maior densidade de réplica por processo.
- Réplicas de *diferentesTipos* de Serviço precisam de partilhar alguns dados comuns que têm um alto custo de inicialização ou memória.
- Você tem uma oferta de serviço gratuito, e você quer colocar um limite na utilização de recursos colocando todas as réplicas do serviço no mesmo processo.

O modelo de hospedagem de processo exclusivo não é coerente com um modelo de aplicação com *múltiplosTipos de Serviço* por *ServicePackage*. Isto porque *váriosTipéis* de Serviço por *ServicePackage* são projetados para alcançar uma maior partilha de recursos entre réplicas, e permite uma maior densidade de replicação por processo. O modelo De Processo Exclusivo foi concebido para alcançar resultados diferentes.

Considere o caso de *múltiplosTipos* de Serviço por *ServicePackage,* com um *CodePackage* diferente registando cada *ServiceType*. Digamos que temos um *ServicePackage* 'MultiTypeServicePackage', que tem dois *CodePackages:*

- 'MyCodePackageA', que regista *o ServiceType* 'MyServiceTypeA'.
- 'MyCodePackageB', que regista *o ServiceType* 'MyServiceTypeB'.

Agora, digamos que criamos uma aplicação, **tecido:/SpecialApp**. Dentro **do tecido:/SpecialApp,** criamos dois serviços com o modelo de Processo Exclusivo:

- **Tecido de serviço:/SpecialApp/ServiceA** do tipo 'MyServiceTypeA', com duas divisórias (por exemplo, **P1** e **P2),** e três réplicas por partição.
- **Tecido de serviço:/SpecialApp/ServiceB** do tipo 'MyServiceTypeB', com duas divisórias **(P3** e **P4**), e três réplicas por partição.

Num dado nó, ambos os serviços têm duas réplicas cada. Como utilizamos o modelo De Processo Exclusivo para criar os serviços, o Service Fabric ativa uma nova cópia do 'MyServicePackage' para cada réplica. Cada ativação de 'MultiTypeServicePackage' inicia uma cópia de 'MyCodePackageA' e 'MyCodePackageB'. No entanto, apenas um dos 'MyCodePackageA' ou 'MyCodePackageB' acolhe a réplica para a qual foi ativada 'MultiTypeServicePackage'. O diagrama a seguir mostra a vista do nó:


![Diagrama que mostra a vista do nó.][node-view-five]


Na ativação de 'MultiTypeServicePackage' para a réplica da partição **P1** do tecido de **serviço:/SpecialApp/ServiceA,**'MyCodePackageA' está a alojar a réplica. 'MyCodePackageB' está a funcionar. Da mesma forma, na ativação de 'MultiTypeServicePackage' para a réplica da partição **P3** do tecido de **serviço:/SpecialApp/ServiceB**, 'MyCodePackageB' está a alojar a réplica. 'MyCodePackageA' está em execução. Assim, quanto maior for o número de *CodePackages* (registando *diferentesTipos de Serviço)* por *ServicePackage,* maior é a utilização de recursos redundantes. 
 
 No entanto, se criarmos o tecido de **serviços:/SpecialApp/ServiceA** e **tecido:/SpecialApp/ServiceB** com o modelo de Processo Partilhado, o Service Fabric ativa apenas uma cópia do 'MultiTypeServicePackage' para o tecido da **aplicação:/SpecialApp**. 'MyCodePackageA' acolhe todas as réplicas para o tecido de **serviço:/SpecialApp/ServiceA**. 'MyCodePackageB' acolhe todas as réplicas do tecido de **serviço:/SpecialApp/ServiceB**. O diagrama a seguir mostra a vista do nó neste ajuste: 


![Diagrama da visão do nó da aplicação implantada][node-view-six]


No exemplo anterior, pode pensar-se que se o 'MyCodePackageA' registar tanto 'MyServiceTypeA' como 'MyServiceTypeB', e não houver 'MyCodePackageB', então não existe *um CodePackage* redundante em execução. Apesar de estar correto, este modelo de aplicação não está alinhado com o modelo de hospedagem de Processo Exclusivo. Se o objetivo é colocar cada réplica no seu próprio processo dedicado, não precisa de registar ambos os *ServiceTypes* a partir do mesmo *CodePackage.* Em vez disso, basta colocar cada *ServiceType* no seu próprio *ServicePackage*.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Serviços fiáveis e subprocessamentos de forca de ator

A Service Fabric não suporta serviços fiáveis e, posteriormente, atores fiáveis que falsificam subprocessos. Um exemplo do motivo pelo qual o seu não suportado é [o CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) não pode ser utilizado para registar um subprocesso não suportado, e as fichas de cancelamento são enviadas apenas para processos registados; resultando em todo o tipo de problemas, tais como falhas de upgrade, quando os subprocessos não fecham após o processo dos pais ter recebido um token de cancelamento.

## <a name="next-steps"></a>Próximos passos
[Embale uma aplicação][a4] e prepare-a para ser implantada.

[Implementar e remover aplicações][a5]. Este artigo descreve como usar o PowerShell para gerir instâncias de aplicação.

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

[r1]: /rest/api/servicefabric/sfclient-api-createservice

[c1]: /dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: /dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: /powershell/module/servicefabric/new-servicefabricservice
[p2]: /powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[p4]: /powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: /powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: /powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: /powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
