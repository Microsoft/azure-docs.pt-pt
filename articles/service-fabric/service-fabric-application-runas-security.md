---
title: Executar um serviço sob sistema e contas de segurança local
description: Saiba como executar uma aplicação de Tecido de Serviço sob sistema e contas de segurança locais.  Crie princípios de segurança e aplique a política de Run-As para gerir de forma segura os seus serviços.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75610131"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Executar um serviço como uma conta de utilizador local ou conta do sistema local
Ao utilizar o Azure Service Fabric, pode proteger aplicações que estão a ser executadas no cluster sob diferentes contas de utilizador. Por predefinição, as aplicações do Service Fabric são executadas sob a conta que o processo de Fabric.exe é executado. O Service Fabric também fornece a capacidade de executar aplicações numa conta de utilizador ou sistema local. Os tipos de conta de sistema local suportados são **LocalUser,** **NetworkService,** **LocalService**e **LocalSystem.**  Se estiver a executar o Service Fabric num cluster autónomo do Windows, pode executar um serviço em [contas de domínio do Ative Directory](service-fabric-run-service-as-ad-user-or-group.md) ou contas de serviço [geridas pelo grupo.](service-fabric-run-service-as-gmsa.md)

No manifesto de aplicação, define as contas de utilizador necessárias para executar serviços ou recursos seguros na secção **Principais.** Também pode definir e criar grupos de utilizadores para que um ou mais utilizadores possam ser geridos em conjunto. Isto é útil quando há vários utilizadores para diferentes pontos de entrada de serviço e eles precisam de privilégios comuns que estão disponíveis a nível de grupo.  Os utilizadores são então referenciados numa política de RunAs, que é aplicada a um serviço específico ou a todos os serviços da aplicação. 

Por predefinição, a política RunAs é aplicada ao ponto de entrada principal.  Também pode aplicar uma política RunAs no ponto de entrada de configuração, se precisar de [executar certas operações de configuração de alto privilégio numa conta do sistema](service-fabric-run-script-at-service-startup.md), ou tanto os pontos de entrada principais como os pontos de entrada de configuração.  

> [!NOTE] 
> Se aplicar uma política runAs a um serviço e o manifesto de serviço declarar recursos de ponto final com o protocolo HTTP, deve especificar uma **SegurançaAccessPolicy**.  Para obter mais informações, consulte [atribuir uma política de acesso à segurança para pontos finais HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Executar um serviço como utilizador local
Pode criar um utilizador local que possa ser usado para ajudar a garantir um serviço dentro da aplicação. Quando um tipo de conta **LocalUser** é especificado na secção de princípios do manifesto de aplicação, o Service Fabric cria contas de utilizador locais em máquinas onde a aplicação é implementada. Por padrão, estas contas não têm os mesmos nomes que os especificados no manifesto de aplicação (por exemplo, *Cliente3* no exemplo manifesto de aplicação a seguir indicado). Em vez disso, são gerados dinamicamente e têm senhas aleatórias.

Na secção **RunAsPolicy** para um **ServiceManifestImport,** especifique a conta de utilizador da secção **Principais** para executar o pacote de código de serviço.  O exemplo a seguir mostra como criar um utilizador local e aplicar uma política RunAs no ponto de entrada principal:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Criar um grupo de utilizadores locais
Pode criar grupos de utilizadores e adicionar um ou mais utilizadores ao grupo. Isto é útil se houver vários utilizadores para diferentes pontos de entrada de serviço e eles precisam ter certos privilégios comuns que estão disponíveis a nível de grupo. O exemplo manifesto da aplicação que se segue mostra um grupo local chamado *LocalAdminGroup* que tem privilégios de administrador. Dois utilizadores, *Cliente1* e *Cliente2,* são membros deste grupo local. Na secção **ServiceManifestImport,** é aplicada uma política RunAs para executar o pacote de código *Stateful1Pkg* como *Cliente2*.  Outra política runAs é aplicada para executar o pacote de código *Web1Pkg* como *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicar uma política padrão a todos os pacotes de código de serviço
Utiliza a secção **DefaultRunAsPolicy** para especificar uma conta de utilizador predefinida para todos os pacotes de código que não tenham uma **Configuração específica do RunAsPolicy.** Se a maioria dos pacotes de código especificados no manifesto de serviço utilizado por uma aplicação precisar de ser executado sob o mesmo utilizador, a aplicação pode apenas definir uma política runAs predefinida com essa conta de utilizador. O exemplo a seguir especifica que, se um pacote de código não tiver uma **RunAsPolicy** especificada, o pacote de código deve ser executado sob o utilizador **MyDefaultAccount** especificado na secção de princípios.  Os tipos de conta suportados são LocalUser, NetworkService, LocalSystem e LocalService.  Se utilizar um utilizador ou serviço local, especifique também o nome da conta e a palavra-passe.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Depurar um pacote de código localmente usando a reorientação da consola
Ocasionalmente, é útil para depurar propósitos para ver a saída da consola a partir de um serviço de execução. Pode definir uma política de redirecionamento da consola no ponto de entrada no manifesto de serviço, que escreve a saída para um ficheiro. A saída do ficheiro é escrita para a pasta de aplicação chamada **log** no nó de cluster onde a aplicação é implantada e executada. 

> [!WARNING]
> Nunca utilize a política de redirecionamento da consola numa aplicação que seja implementada na produção porque isso pode afetar o failover da aplicação. *Utilize-o apenas* para fins de desenvolvimento local e depuragem.  
> 
> 

O exemplo manifesto de serviço que se segue mostra a reorientação da consola com um valor FileRetentionCount:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Compreender o modelo de aplicação](service-fabric-application-model.md)
* [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
