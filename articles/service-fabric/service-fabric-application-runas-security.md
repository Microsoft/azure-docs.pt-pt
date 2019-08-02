---
title: Executar um serviço de Service Fabric do Azure em contas de segurança local e do sistema | Microsoft Docs
description: Saiba como executar um aplicativo Service Fabric em contas de segurança do sistema e locais.  Crie entidades de segurança e aplique a política executar como para executar seus serviços com segurança.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: atsenthi
ms.openlocfilehash: 8b0ddc619a7e840b0379a790bd21e7beae812109
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600047"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Executar um serviço como uma conta de usuário local ou uma conta de sistema local
Usando o Azure Service Fabric, você pode proteger aplicativos que estão em execução no cluster em contas de usuário diferentes. Por padrão, Service Fabric aplicativos são executados sob a conta sob a qual o processo Fabric. exe é executado. O Service Fabric também fornece a capacidade de executar aplicativos em uma conta de sistema ou usuário local. Os tipos de conta do sistema local com suporte são **LocalUser**, **NetworkService**, **LocalService**e **LocalSystem**.  Se você estiver executando Service Fabric em um cluster autônomo do Windows, poderá executar um serviço em [Active Directory contas de domínio](service-fabric-run-service-as-ad-user-or-group.md) ou [contas de serviço gerenciado de grupo](service-fabric-run-service-as-gmsa.md).

No manifesto do aplicativo, você define as contas de usuário necessárias para executar serviços ou recursos de segurança na seção de **entidades** . Você também pode definir e criar grupos de usuários para que um ou mais usuários possam ser gerenciados juntos. Isso é útil quando há vários usuários para diferentes pontos de entrada de serviço e eles precisam de privilégios comuns que estão disponíveis no nível de grupo.  Os usuários são então referenciados em uma política RunAs, que é aplicada a um serviço específico ou a todos os serviços no aplicativo. 

Por padrão, a política RunAs é aplicada ao ponto de entrada principal.  Você também pode aplicar uma política RunAs ao ponto de entrada de instalação, se precisar [executar determinadas operações de configuração de alto privilégio em uma conta do sistema](service-fabric-run-script-at-service-startup.md)ou nos pontos de entrada principal e de instalação.  

> [!NOTE] 
> Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, você deverá especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [atribuir uma política de acesso de segurança para pontos de extremidade http e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Executar um serviço como um usuário local
Você pode criar um usuário local que pode ser usado para ajudar a proteger um serviço dentro do aplicativo. Quando um tipo de conta **LocalUser** é especificado na seção Principals do manifesto do aplicativo, Service Fabric cria contas de usuário local em computadores em que o aplicativo é implantado. Por padrão, essas contas não têm os mesmos nomes que aquelas especificadas no manifesto do aplicativo (por exemplo, *customer3* no exemplo de manifesto do aplicativo a seguir). Em vez disso, eles são gerados dinamicamente e têm senhas aleatórias.

Na seção **RunAsPolicy** de um **ServiceManifestImport**, especifique a conta de usuário da seção de **entidades de segurança** para executar o pacote de códigos de serviço.  O exemplo a seguir mostra como criar um usuário local e aplicar uma política RunAs ao ponto de entrada principal:

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

## <a name="create-a-local-user-group"></a>Criar um grupo de usuários local
Você pode criar grupos de usuários e adicionar um ou mais usuários ao grupo. Isso será útil se houver vários usuários para diferentes pontos de entrada de serviço e eles precisarem ter determinados privilégios comuns disponíveis no nível do grupo. O exemplo de manifesto de aplicativo a seguir mostra um grupo local chamado *LocalAdminGroup* que tem privilégios de administrador. Dois usuários, *Customer1* e *customer2*, se tornaram membros desse grupo local. Na seção **ServiceManifestImport** , uma política runas é aplicada para executar o pacote de códigos *Stateful1Pkg* como *customer2*.  Outra política RunAs é aplicada para executar o pacote de códigos *Web1Pkg* como *Customer1*.

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
Use a seção **DefaultRunAsPolicy** para especificar uma conta de usuário padrão para todos os pacotes de código que não têm um **RunAsPolicy** específico definido. Se a maioria dos pacotes de código especificados no manifesto do serviço usado por um aplicativo precisar ser executada sob o mesmo usuário, o aplicativo poderá definir apenas uma política RunAs padrão com essa conta de usuário. O exemplo a seguir especifica que se um pacote de códigos não tiver um **RunAsPolicy** especificado, o pacote de códigos deverá ser executado sob o usuário **MyDefaultAccount** especificado na seção entidades.  Os tipos de conta com suporte são LocalUser, NetworkService, LocalSystem e LocalService.  Se você estiver usando um usuário ou serviço local, especifique também o nome da conta e a senha.

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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Depurar um pacote de códigos localmente usando o redirecionamento de console
Ocasionalmente, é útil para fins de depuração para ver a saída do console de um serviço em execução. Você pode definir uma política de redirecionamento de console no ponto de entrada no manifesto do serviço, que grava a saída em um arquivo. A saída do arquivo é gravada na pasta do aplicativo chamada **log** no nó do cluster em que o aplicativo é implantado e executado. 

> [!WARNING]
> Nunca use a política de redirecionamento de console em um aplicativo implantado em produção porque isso pode afetar o failover do aplicativo. Use isso *apenas* para fins de depuração e desenvolvimento locais.  
> 
> 

O exemplo de manifesto de serviço a seguir mostra como habilitar o redirecionamento de console com um valor de FileRetentionCount:

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
* [Entender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
