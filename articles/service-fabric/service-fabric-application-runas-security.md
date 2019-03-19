---
title: Executar um serviço de Azure Service Fabric em sistema e contas de segurança locais | Documentos da Microsoft
description: Saiba como executar uma aplicação do Service Fabric em sistema e contas de segurança local.  Criar principais de segurança e aplicar a política Run para executar com segurança os seus serviços.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: f454ec7805db0a79f9346f252809c9d7f6869734
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871016"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Executar um serviço como uma conta de utilizador local ou a conta sistema local
Ao utilizar o Azure Service Fabric, pode proteger aplicações em execução no cluster sob diferentes contas de usuário. Por predefinição, as aplicações do Service Fabric executam sob a conta que o processo de Fabric.exe é executado em. O Service Fabric também fornece a capacidade de executar aplicativos numa conta de utilizador ou sistema local. Tipos de conta de sistema local suportados são **Utilizador_local**, **NetworkService**, **LocalService**, e **LocalSystem**.  Se estiver executando o Service Fabric num cluster autónomo do Windows, pode executar um serviço sob [contas de domínio do Active Directory](service-fabric-run-service-as-ad-user-or-group.md) ou [contas de serviço geridas de grupo](service-fabric-run-service-as-gmsa.md).

No manifesto do aplicativo, define as contas de utilizador necessárias para executar serviços ou recursos seguros na **principais** secção. Também pode definir e criar grupos de utilizadores para que um ou mais utilizadores podem ser geridos em conjunto. Isto é útil quando existirem vários utilizadores para os pontos de entrada de serviço diferentes e que precisam de privilégios comuns que estão disponíveis no nível de grupo.  Os utilizadores, em seguida, são referenciados numa política de RunAs, que é aplicada a um serviço específico ou de todos os serviços no aplicativo. 

Por predefinição, é aplicada a política de RunAs para o ponto de entrada principal.  Também pode aplicar uma política de RunAs a para o ponto de entrada de configuração, se precisar [executar determinadas operações de configuração de privilégio elevado com uma conta do sistema](service-fabric-run-script-at-service-startup.md), ou ambos os principal e dos pontos de entrada de configuração.  

> [!NOTE] 
> Se aplicar uma política de RunAs para um serviço e o manifesto do serviço declara os recursos de ponto final com o protocolo HTTP, tem de especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [atribuir uma política de acesso de segurança para pontos finais HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Executar um serviço como um utilizador local
Pode criar um utilizador local que pode ser utilizado para ajudar a proteger um serviço no aplicativo. Quando um **Utilizador_local** tipo de conta é especificada na seção de principais do manifesto da aplicação, Service Fabric cria contas de utilizador local nas máquinas em que a aplicação é implementada. Por predefinição, estas contas não têm os mesmos nomes que os especificados no manifesto do aplicativo (por exemplo, *Customer3* no seguinte exemplo de manifesto de aplicativo). Em vez disso, eles são gerados dinamicamente e tem palavras-passe aleatórias.

Na **RunAsPolicy** secção para obter um **ServiceManifestImport**, especifique a conta de utilizador do **principais** secção para executar o pacote de código do serviço.  O exemplo seguinte mostra como criar um utilizador local e aplicar uma política de RunAs para o ponto de entrada principal:

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

## <a name="create-a-local-user-group"></a>Criar um grupo de utilizadores local
Pode criar grupos de utilizadores e adicionar um ou mais utilizadores ao grupo. Isto é útil se existirem vários utilizadores para os pontos de entrada de serviço diferentes e que precisam para tem determinados privilégios comuns que estão disponíveis no nível de grupo. O exemplo de manifesto de aplicação seguinte mostra um grupo local com o nome *LocalAdminGroup* que tenha privilégios de administrador. Dois usuários, *Customer1* e *Customer2*, são feitas membros deste grupo local. Na **ServiceManifestImport** secção, um RunAs política é aplicada a executar o *Stateful1Pkg* pacote do código como *Customer2*.  Outra de RunAs de política é aplicada a executar o *Web1Pkg* pacote do código como *Customer1*.

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

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicar uma política predefinida para todos os pacotes de código de serviço
Utilizar o **DefaultRunAsPolicy** seção para especificar uma conta de usuário padrão para todo o código empacota isso não tem um específico **RunAsPolicy** definido. Se a maioria dos pacotes de código que são especificados no manifesto do serviço utilizado por uma aplicação precisa ser executado sob o mesmo utilizador, a aplicação apenas pode definir uma política de RunAs predefinida com essa conta de utilizador. O exemplo seguinte especifica que, se um pacote do código não tem um **RunAsPolicy** especificado, o pacote do código deve ser executado sob a **MyDefaultAccount** especificado na seção de principais de utilizador.  Tipos de conta suportados são Utilizador_local, NetworkService, LocalSystem e LocalService.  Se utilizar um utilizador local ou serviço, também especifica o nome da conta e palavra-passe.

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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Depurar um pacote do código localmente utilizando o redirecionamento de consola
Ocasionalmente, é útil para fins de depuração ver o resultado da consola de um serviço em execução. Pode definir uma política de redirecionamento de consola no ponto de entrada no manifesto do serviço, que grava a saída num arquivo. O resultado de ficheiro é escrito para a pasta de aplicativo chamada **log** no nó de cluster em que a aplicação é implementada e executar. 

> [!WARNING]
> Nunca use a política de redirecionamento de consola num aplicativo que é implementado em produção, porque isso pode afetar o failover de aplicativos. *Apenas* usá-lo para o desenvolvimento local e fins de depuração.  
> 
> 

O seguinte serviço de exemplo mostra que habilita o redirecionamento de consola com um valor de FileRetentionCount manifesto:

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
## <a name="next-steps"></a>Passos Seguintes
* [Compreender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos num manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
