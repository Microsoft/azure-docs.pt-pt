---
title: Exemplos de manifesto de aplicativo de serviços do Azure Service Fabric reliable | Documentos da Microsoft
description: Saiba como configurar a aplicação e serviço manifestos definições para uma aplicação de Service Fabric reliable services.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: ryanwi
ms.openlocfilehash: 1d0a0c2c6a965ac14002257484c5f840666e6fc1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885271"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Exemplos de manifesto e aplicação e serviço do Reliable Services
Seguem-se exemplos de manifestos de aplicações e serviços para uma aplicação do Service Fabric com um front-end de web de ASP.NET Core e um back-end com monitorização de estado. O objetivo destes exemplos é mostrar quais configurações estão disponíveis e como utilizá-los. Esses manifestos de aplicações e serviços são baseiam o [início rápido de .NET do Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) manifestos.

São apresentadas as seguintes funcionalidades:

|Manifesto|Funcionalidades|
|---|---|
|[Manifesto da aplicação](#application-manifest)| [governação de recursos](service-fabric-resource-governance.md), [executar um serviço como uma conta de administrador local](service-fabric-application-runas-security.md), [aplicar uma política predefinida para todos os pacotes de código de serviço](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [criar principais de utilizador e grupo](service-fabric-application-runas-security.md), partilhar um pacote de dados entre instâncias de serviço, [substituir pontos finais de serviço](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|Manifesto do serviço de FrontEndService| [Executar um script na inicialização](service-fabric-run-script-at-service-startup.md), [definir um ponto de final HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|Manifesto do serviço de BackEndService| [Declarar um pacote de configuração](service-fabric-application-and-service-manifests.md), [declarar um pacote de dados](service-fabric-application-and-service-manifests.md), [configurar um ponto final](service-fabric-service-manifest-resources.md)| 

Ver [elementos de manifestos de aplicativo](#application-manifest-elements), [elementos de manifestos de serviço VotingWeb](#votingweb-service-manifest-elements), e [elementos de manifestos do serviço de VotingData](#votingdata-service-manifest-elements) para obter mais informações sobre XML específico elementos.

## <a name="application-manifest"></a>Manifesto de aplicação

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>Manifesto do serviço VotingWeb

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>Manifesto do serviço de VotingData

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>Elementos de manifestos de aplicação
### <a name="applicationmanifest-element"></a>Elemento de ApplicationManifest
Descreve de maneira declarativa o tipo de aplicação e a versão. Manifestos de serviço de um ou mais dos serviços constituintes são referenciados para compor um tipo de aplicação. Definições de configuração dos serviços que constituem podem ser substituídas com as definições da aplicação parametrizadas. Padrão de serviços, modelos de serviço, entidades de segurança, políticas, configuração de diagnósticos e certificados pode também declarado no nível do aplicativo. Para obter mais informações, consulte [ApplicationManifest elemento](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Elemento de parâmetros
Declara os parâmetros que são utilizados no manifesto do aplicativo. O valor desses parâmetros pode ser fornecido quando o aplicativo é instanciado e pode ser utilizado para substituir a aplicação ou definições de configuração do serviço. Para obter mais informações, consulte [elemento de parâmetros](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Elemento de parâmetro
Um parâmetro de aplicativo a ser utilizado nesse manifesto. O valor do parâmetro pode ser alterado durante a instanciação do aplicativo, ou, não se for fornecido nenhum valor é utilizado o valor predefinido. Para obter mais informações, consulte [elemento de parâmetro](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>Elemento de ServiceManifestImport
Importa um manifesto de serviço criado pelo desenvolvedor de serviço. Um manifesto de serviço tem de ser importado para cada serviço constituinte no aplicativo. Substituições de configuração e políticas podem ser declaradas para o manifesto do serviço. Para obter mais informações, consulte [ServiceManifestImport elemento](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>Elemento de ServiceManifestRef
Importa o manifesto do serviço por referência. Atualmente o arquivo de manifesto do serviço (servicemanifest. xml) tem de estar presente no pacote de compilação. Para obter mais informações, consulte [ServiceManifestRef elemento](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>Elemento de ResourceOverrides
Especifica as substituições de recurso para pontos de extremidade declarados em recursos do manifesto do serviço. Para obter mais informações, consulte [ResourceOverrides elemento](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Elemento de pontos finais
O ais para substituir. Para obter mais informações, consulte [pontos finais de elemento](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Elemento de ponto final
O ponto final, declarados no manifesto do serviço, para substituir. Para obter mais informações, consulte [elemento de ponto final](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Elemento de políticas
Descreve as políticas (ponto de extremidade vinculação de pacote de partilha, Run, acesso e segurança) a serem aplicadas no manifesto do serviço importado. Para obter mais informações, consulte [elemento de políticas](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Define a política de governação de recursos que é aplicada no nível do pacote todo o serviço. Para obter mais informações, consulte [ServicePackageResourceGovernancePolicy elemento](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Elemento de ResourceGovernancePolicy
Especifica os limites de recursos para um codepackage. Para obter mais informações, consulte [ResourceGovernancePolicy elemento](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy elemento
Indica se um pacote de código, configuração ou dados deve ser partilhado entre instâncias de serviço do mesmo tipo de serviço. Para obter mais informações, consulte [PackageSharingPolicy elemento](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>Elemento de SecurityAccessPolicy
Concede permissões de acesso a um principal de um recurso (por exemplo, um ponto de extremidade) definidas no manifesto do serviço. Normalmente, é muito útil controlar e restringir o acesso dos serviços em recursos diferentes para minimizar os riscos de segurança. Isto é especialmente importante quando o aplicativo é criado a partir de uma coleção de serviços a partir de um mercado que são desenvolvidas pelos programadores diferentes. Para obter mais informações, consulte [SecurityAccessPolicy elemento](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>Elemento de RunAsPolicy
Especifica o utilizador local ou a conta de sistema local que um pacote de código do serviço será executado como. Contas de domínio são suportadas em implementações do Windows Server onde o Azure Active Directory está disponível. Por predefinição, os aplicativos são executados sob a conta que o processo de Fabric.exe é executado. Aplicativos também podem ser executados como outras contas, que tem de ser declaradas na secção de entidades de segurança. Se aplicar uma política de RunAs para um serviço e o manifesto do serviço declara os recursos de ponto final com o protocolo HTTP, também tem de especificar um SecurityAccessPolicy para garantir que as portas alocadas para estes pontos finais estão corretamente listado para os RunAs de controlo de acesso conta de utilizador que o serviço é executado. Para um ponto final HTTPS, também tem de definir um EndpointBindingPolicy para indicar o nome do certificado para devolver ao cliente. Para obter mais informações, consulte [RunAsPolicy elemento](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>Elemento de DefaultServices
Declara as instâncias de serviço que são criadas automaticamente sempre que um aplicativo é instanciado em relação a este tipo de aplicação. Para obter mais informações, consulte [DefaultServices elemento](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Elemento de serviço
Declara um serviço a ser criada automaticamente quando o aplicativo é instanciado. Para obter mais informações, consulte [elemento Service](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>Elemento de StatefulService
Define um serviço com estado. Para obter mais informações, consulte [StatefulService elemento](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>Elemento de StatelessService
Define um serviço sem estado. Para obter mais informações, consulte [StatelessService elemento](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Elemento de entidades de segurança
Descreve os princípios de segurança (utilizadores, grupos) necessários para esta aplicação executar serviços e recursos seguros. Principais são referenciados nas secções de políticas. Para obter mais informações, consulte [principais de elemento](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Elemento de grupos
Declara um conjunto de grupos como segurança principais, que podem ser referenciados nas políticas. Os grupos são úteis se existirem vários utilizadores para os pontos de entrada de serviço diferentes e que precisam para tem determinados privilégios comuns que estão disponíveis no nível de grupo. Para obter mais informações, consulte [elemento de grupos](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Elemento de grupo
Declara um grupo como uma entidade de segurança, que pode ser referenciado nas políticas. Para obter mais informações, consulte [elemento de grupo](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Elemento de associação
 Para obter mais informações, consulte [elemento de associação](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>Elemento de SystemGroup
 Para obter mais informações, consulte [SystemGroup elemento](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Elemento de utilizadores
Declara um conjunto de utilizadores como segurança principais, que podem ser referenciados nas políticas. Para obter mais informações, consulte [elemento de utilizadores](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Elemento de utilizador
Declara um utilizador como uma entidade de segurança, que pode ser referenciado nas políticas. Para obter mais informações, consulte [elemento de utilizador](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>Elemento de MemberOf
Os utilizadores podem ser adicionados a qualquer grupo de associação existente, pelo que pode herdar todas as propriedades e definições de segurança desse grupo de associação. O grupo de associação pode ser utilizado para proteger os recursos externos que têm de ser acedido por diferentes serviços ou o mesmo serviço (num computador diferente). Para obter mais informações, consulte [MemberOf elemento](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>Elemento de SystemGroup
O grupo de sistema para adicionar o utilizador.  O grupo de sistema tem de ser definido na secção de grupos. Para obter mais informações, consulte [SystemGroup elemento](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Elemento de grupo
O grupo para adicionar o utilizador.  O grupo tem de ser definido na secção de grupos. Para obter mais informações, consulte [elemento de grupo](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Elemento de políticas
Descreve as políticas (recolha de registos, predefinição Run, estado de funcionamento e acesso de segurança) a ser aplicado ao nível da aplicação. Para obter mais informações, consulte [elemento de políticas](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>Elemento de DefaultRunAsPolicy
Especifique uma conta de usuário padrão para todos os pacotes de código de serviço que não têm um RunAsPolicy específico definido na secção ServiceManifestImport. Para obter mais informações, consulte [DefaultRunAsPolicy elemento](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Elementos de manifestos de serviço VotingWeb
### <a name="servicemanifest-element"></a>Elemento de ServiceManifest
Descreve de maneira declarativa o tipo de serviço e a versão. Lista os pacotes de código, configuração e dados de forma independente é atualizável que juntos compõem um pacote de serviço para dar suporte a um ou mais tipos de serviço. Recursos, as definições de diagnóstico e os metadados de serviço, como o tipo de serviço, as propriedades de estado de funcionamento e métricas de balanceamento de carga, também são especificados. Para obter mais informações, consulte [ServiceManifest elemento](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento de ServiceTypes
Define os tipos de serviço são suportados por um CodePackage nesse manifesto. Quando um serviço é instanciado em relação a um dos seguintes tipos de serviço, todos os pacotes do código declarados nesse manifesto são ativados através da execução de seus pontos de entrada. Tipos de serviço são declarados em nível de manifesto e não no nível de pacote de código. Para obter mais informações, consulte [ServiceTypes elemento](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento de StatelessServiceType
Descreve um tipo de serviço sem estado. Para obter mais informações, consulte [StatelessServiceType elemento](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento de CodePackage
Descreve um pacote de código que dá suporte a um tipo de serviço definido. Quando um serviço é instanciado em relação a um dos seguintes tipos de serviço, todos os pacotes do código declarados nesse manifesto são ativados através da execução de seus pontos de entrada. Os processos resultantes devem registrar os tipos de serviço com suporte em tempo de execução. Quando há vários pacotes de código, eles são todas ativados sempre que o sistema procura por qualquer um dos tipos de serviço declarado. Para obter mais informações, consulte [CodePackage elemento](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>Elemento de SetupEntryPoint
Um ponto de entrada com privilégios que, por predefinição, é executado com as mesmas credenciais do Service Fabric (normalmente, a conta NETWORKSERVICE) antes de qualquer outro ponto de entrada. O executável especificado pelo ponto de entrada é, normalmente, o anfitrião do serviço de execução longa. A presença de um ponto de entrada de configuração individual evita ter de executar o host de serviço com privilégios elevados por longos períodos de tempo. Para obter mais informações, consulte [SetupEntryPoint elemento](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento de ExeHost
 Para obter mais informações, consulte [ExeHost elemento](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Elemento de programa
O nome executável.  Por exemplo, "MySetup.bat" ou "MyServiceHost.exe". Para obter mais informações, consulte [elemento do programa](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Elemento de argumentos
 Para obter mais informações, consulte [argumentos elemento](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Elemento de WorkingFolder
O diretório de trabalho para o processo do pacote de código no nó de cluster onde a aplicação é implementada. Pode especificar três valores: O trabalho (predefinição), CodePackage ou base de código. Base de código especifica que o diretório de trabalho está definido para o diretório no qual o EXE é definido no pacote de código. CodePackage define o diretório de trabalho para ser a raiz do pacote de código, independentemente de onde o EXE está definido no diretório do pacote de código. Trabalho define o diretório de trabalho para uma pasta exclusiva criada no nó.  Esta pasta é o mesmo para a instância de todo o aplicativo. Por predefinição, o diretório de trabalho de todos os processos do aplicativo está definido para a pasta de trabalho de aplicação. Isso é onde os processos podem escrever os dados. Não é recomendada a escrita de dados no pacote do código ou na base de código, essas pastas poderão ser compartilhadas entre diferentes instâncias da aplicação e podem obter eliminadas. Para obter mais informações, consulte [WorkingFolder elemento](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>Elemento de ConsoleRedirection

> [!WARNING]
> Não utilizar o redirecionamento de consola num aplicativo de produção, utilize apenas para desenvolvimento local e depuração. Redireciona saída da consola do script de arranque para um ficheiro de saída na pasta denominada "log" no nó de cluster em que a aplicação é implementada e executar. Para obter mais informações, consulte [ConsoleRedirection elemento](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>Elemento de ponto de entrada
O executável especificado pelo ponto de entrada é, normalmente, o anfitrião do serviço de execução longa. A presença de um ponto de entrada de configuração individual evita ter de executar o host de serviço com privilégios elevados por longos períodos de tempo. O executável especificado pelo ponto de entrada é executado após SetupEntryPoint será encerrada com êxito. O processo resultante é monitorizado e reiniciado (início com SetupEntryPoint), se alguma vez terminar ou falhar. Para obter mais informações, consulte [elemento de ponto de entrada](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento de ExeHost
 Para obter mais informações, consulte [ExeHost elemento](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>Elemento de ConfigPackage
Declara uma pasta, com o nome o atributo de nome, em PackageRoot que contém um arquivo Settings XML. Este ficheiro contém seções de definições de par chave-valor, de definidas pelo utilizador que o processo pode ler voltar no tempo de execução. Durante uma atualização, se apenas a versão de ConfigPackage foi alterada, em seguida, o processo em execução não for reiniciado. Em vez disso, um retorno de chamada notifica o processo que as definições de configuração foram alterados para que possa ser recarregadas dinamicamente. Para obter mais informações, consulte [ConfigPackage elemento](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Elemento de recursos
Descreve os recursos utilizados por este serviço, que pode ser declarado sem modificar o código compilado e alterado quando o serviço é implementado. Acesso a estes recursos é controlado as seções de entidades e as políticas de manifesto do aplicativo. Para obter mais informações, consulte [elemento de recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento de pontos finais
Define os pontos finais para o serviço. Para obter mais informações, consulte [pontos finais de elemento](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento de ponto final
O ponto final, declarados no manifesto do serviço, para substituir. Para obter mais informações, consulte [elemento de ponto final](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Elementos de manifestos do serviço de VotingData
### <a name="servicemanifest-element"></a>Elemento de ServiceManifest
Descreve de maneira declarativa o tipo de serviço e a versão. Lista os pacotes de código, configuração e dados de forma independente é atualizável que juntos compõem um pacote de serviço para dar suporte a um ou mais tipos de serviço. Recursos, as definições de diagnóstico e os metadados de serviço, como o tipo de serviço, as propriedades de estado de funcionamento e métricas de balanceamento de carga, também são especificados. Para obter mais informações, consulte [ServiceManifest elemento](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento de ServiceTypes
Define os tipos de serviço são suportados por um CodePackage nesse manifesto. Quando um serviço é instanciado em relação a um dos seguintes tipos de serviço, todos os pacotes do código declarados nesse manifesto são ativados através da execução de seus pontos de entrada. Tipos de serviço são declarados em nível de manifesto e não no nível de pacote de código. Para obter mais informações, consulte [ServiceTypes elemento](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>Elemento de StatefulServiceType
Descreve um tipo de serviço com estado. Para obter mais informações, consulte [StatefulServiceType elemento](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento de CodePackage
Descreve um pacote de código que dá suporte a um tipo de serviço definido. Quando um serviço é instanciado em relação a um dos seguintes tipos de serviço, todos os pacotes do código declarados nesse manifesto são ativados através da execução de seus pontos de entrada. Os processos resultantes devem registrar os tipos de serviço com suporte em tempo de execução. Quando há vários pacotes de código, eles são todas ativados sempre que o sistema procura por qualquer um dos tipos de serviço declarado. Para obter mais informações, consulte [CodePackage elemento](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento de ponto de entrada
O executável especificado pelo ponto de entrada é, normalmente, o anfitrião do serviço de execução longa. A presença de um ponto de entrada de configuração individual evita ter de executar o host de serviço com privilégios elevados por longos períodos de tempo. O executável especificado pelo ponto de entrada é executado após SetupEntryPoint será encerrada com êxito. O processo resultante é monitorizado e reiniciado (início com SetupEntryPoint), se alguma vez terminar ou falhar. Para obter mais informações, consulte [elemento de ponto de entrada](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento de ExeHost
 Para obter mais informações, consulte [ExeHost elemento](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Elemento de programa
O nome executável.  Por exemplo, "MySetup.bat" ou "MyServiceHost.exe". Para obter mais informações, consulte [elemento do programa](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Elemento de WorkingFolder
O diretório de trabalho para o processo do pacote de código no nó de cluster onde a aplicação é implementada. Pode especificar três valores: O trabalho (predefinição), CodePackage ou base de código. Base de código especifica que o diretório de trabalho está definido para o diretório no qual o EXE é definido no pacote de código. CodePackage define o diretório de trabalho para ser a raiz do pacote de código, independentemente de onde o EXE está definido no diretório do pacote de código. Trabalho define o diretório de trabalho para uma pasta exclusiva criada no nó.  Esta pasta é o mesmo para a instância de todo o aplicativo. Por predefinição, o diretório de trabalho de todos os processos do aplicativo está definido para a pasta de trabalho de aplicação. Isso é onde os processos podem escrever os dados. Não é recomendada a escrita de dados no pacote do código ou na base de código, essas pastas poderão ser compartilhadas entre diferentes instâncias da aplicação e podem obter eliminadas. Para obter mais informações, consulte [WorkingFolder elemento](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>Elemento de ConfigPackage
Declara uma pasta, com o nome o atributo de nome, em PackageRoot que contém um arquivo Settings XML. Este ficheiro contém seções de definições de par chave-valor, de definidas pelo utilizador que o processo pode ler voltar no tempo de execução. Durante uma atualização, se apenas a versão de ConfigPackage foi alterada, em seguida, o processo em execução não for reiniciado. Em vez disso, um retorno de chamada notifica o processo que as definições de configuração foram alterados para que possa ser recarregadas dinamicamente. Para obter mais informações, consulte [ConfigPackage elemento](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Elemento de DataPackage
Declara uma pasta, com o nome do atributo de nome, em PackageRoot que contém ficheiros de dados estáticos para ser consumida pelo processo de tempo de execução. Service Fabric serão reciclar todos os EXEs e DLLHOSTs especificados nos pacotes de anfitrião e suporte quando nenhum dos pacotes de dados listados no manifesto do serviço sejam atualizados. Para obter mais informações, consulte [DataPackage elemento](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Elemento de recursos
Descreve os recursos utilizados por este serviço, que pode ser declarado sem modificar o código compilado e alterado quando o serviço é implementado. Acesso a estes recursos é controlado as seções de entidades e as políticas de manifesto do aplicativo. Para obter mais informações, consulte [elemento de recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento de pontos finais
Define os pontos finais para o serviço. Para obter mais informações, consulte [pontos finais de elemento](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento de ponto final
O ponto final, declarados no manifesto do serviço, para substituir. Para obter mais informações, consulte [elemento de ponto final](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

