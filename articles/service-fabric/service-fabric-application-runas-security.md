---
title: Executar um serviço sob sistema e contas de segurança locais
description: Saiba como executar uma aplicação de Tecido de Serviço sob sistema e contas de segurança locais.  Crie diretores de segurança e aplique a política Run-As para gerir de forma segura os seus serviços.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75610131"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Executar um serviço como conta de utilizador local ou conta do sistema local
Ao utilizar o Tecido de Serviço Azure, pode proteger aplicações que estão a ser realizadas no cluster sob diferentes contas de utilizador. Por predefinição, as aplicações de Tecido de Serviço são executadas sob a conta que o processo Fabric.exe é executado. O Service Fabric também fornece a capacidade de executar aplicações sob uma conta de utilizador ou sistema local. Os tipos de conta de sistema local suportados são **LocalUser,** **NetworkService,** **LocalService**e **LocalSystem**.  Se estiver a executar o Service Fabric num cluster autónomo do Windows, pode executar um serviço sob contas de [domínio active'd'maneio](service-fabric-run-service-as-ad-user-or-group.md) ou contas de [serviço geridas pelo grupo](service-fabric-run-service-as-gmsa.md).

No manifesto de aplicação, define as contas de utilizador necessárias para executar serviços ou recursos seguros na secção **principais.** Também pode definir e criar grupos de utilizadores para que um ou mais utilizadores possam ser geridos em conjunto. Isto é útil quando existem vários utilizadores para diferentes pontos de entrada de serviço e precisam de privilégios comuns que estão disponíveis a nível de grupo.  Os utilizadores são então referenciados numa política de RunAs, que é aplicada a um serviço específico ou a todos os serviços da aplicação. 

Por predefinição, a política RunAs é aplicada ao ponto de entrada principal.  Também pode aplicar uma política de RunAs no ponto de entrada de configuração, se precisar [de executar determinadas operações de configuração de alto privilégio sob uma conta](service-fabric-run-script-at-service-startup.md)de sistema , ou ambos os principais e os pontos de entrada de configuração.  

> [!NOTE] 
> Se aplicar uma política de RunAs a um serviço e o manifesto de serviço declarar recursos de ponto final com o protocolo HTTP, deve especificar uma Política de **Acesso de Segurança**.  Para mais informações, consulte [Atribuir uma política de acesso à segurança para os pontos finais HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Executar um serviço como um utilizador local
Pode criar um utilizador local que possa ser usado para ajudar a garantir um serviço dentro da aplicação. Quando um tipo de conta **LocalUser** é especificado na secção de principais do manifesto de aplicação, o Tecido de Serviço cria contas de utilizador locais em máquinas onde a aplicação é implementada. Por predefinição, estas contas não têm os mesmos nomes especificados no manifesto de aplicação (por exemplo, *Cliente3* no exemplo manifesto de aplicação seguinte). Em vez disso, são gerados dinamicamente e têm senhas aleatórias.

Na secção **RunAsPolicy** para um **ServiceManifestImport,** especifique a conta de utilizador da secção **de principados** para executar o pacote de código de serviço.  O exemplo que se segue mostra como criar um utilizador local e aplicar uma política de RunAs no ponto de entrada principal:

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
Pode criar grupos de utilizadores e adicionar um ou mais utilizadores ao grupo. Isto é útil se houver vários utilizadores para diferentes pontos de entrada de serviço e eles precisam de ter certos privilégios comuns que estão disponíveis a nível de grupo. O exemplo manifesto de aplicação seguinte mostra um grupo local chamado *LocalAdminGroup* que tem privilégios de administrador. Dois utilizadores, *Cliente1* e *Cliente2,* são membros deste grupo local. Na secção **ServiceManifestImport,** é aplicada uma política RunAs para executar o pacote de código *Stateful1Pkg* como *Cliente2*.  Outra política de RunAs é aplicada para executar o pacote de código *Web1Pkg* como *Cliente1*.

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

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicar uma política predefinida a todos os pacotes de código de serviço
Utilize a secção **DefaultRunAsPolicy** para especificar uma conta de utilizador predefinida para todos os pacotes de código que não tenham uma **RunAsPolicy** específica definida. Se a maioria dos pacotes de código especificados no manifesto de serviço utilizado por uma aplicação necessitarem de ser executados sob o mesmo utilizador, a aplicação pode apenas definir uma política de RunAs padrão com essa conta de utilizador. O exemplo que se segue especifica que se um pacote de código não tiver um **RunAsPolicy** especificado, o pacote de código deve ser executado sob o utilizador **MyDefaultAccount** especificado na secção de principais.  Os tipos de conta suportados são LocaisUser, NetworkService, LocalSystem e LocalService.  Se utilizar um utilizador ou serviço local, especifique também o nome da conta e a palavra-passe.

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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Depurar um pacote de código localmente usando a redirecção da consola
Ocasionalmente, é útil para depurar propósitos para ver a saída da consola a partir de um serviço de corrida. Pode definir uma política de reorientação da consola no ponto de entrada no manifesto de serviço, que escreve a saída para um ficheiro. A saída do ficheiro está escrita na pasta de aplicação chamada **log** no nó do cluster onde a aplicação é implementada e executada. 

> [!WARNING]
> Nunca utilize a política de reorientação da consola numa aplicação que é implantada na produção porque isso pode afetar o failover da aplicação. *Utilize-o apenas* para fins de desenvolvimento local e depuração.  
> 
> 

O seguinte exemplo manifesto de serviço mostra que permite a reorientação da consola com um valor FileRetentionCount:

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
