---
title: Exemplos de aplicativos de serviços fiáveis manifestam exemplos
description: Saiba como configurar as definições de formulário de aplicação e de serviço para uma aplicação de serviços fiáveis Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382776"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Exemplos de manifesto e aplicação e serviço do Reliable Services
Seguem-se exemplos da aplicação e dos manifestos de serviço para uma aplicação de Tecido de Serviço com um ASP.NET frontal web core e um back-end imponente. O objetivo destes exemplos é mostrar quais as configurações disponíveis e como usá-las. Estes manifestos de aplicação e serviço baseiam-se nos manifestos [de Quickstart service Fabric .NET.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)

As seguintes características são mostradas:

|Manifesto|Funcionalidades|
|---|---|
|[Manifesto da aplicação](#application-manifest)| [governação de recursos](service-fabric-resource-governance.md), [executar um serviço como uma conta de administração local](service-fabric-application-runas-security.md), aplicar uma política de incumprimento a todos os pacotes de [códigode serviço,](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages) [criar os principais utilizadores e grupos,](service-fabric-application-runas-security.md)partilhar um pacote de dados entre instâncias de serviço, [anular pontos finais](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml) de serviço| 
|Manifesto de serviço FrontEndService| [Executar um script no arranque](service-fabric-run-script-at-service-startup.md)de serviço, definir um ponto final [HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|Manifesto de serviço BackEndService| [Declare um pacote de config,](service-fabric-application-and-service-manifests.md) [declare um pacote de dados,](service-fabric-application-and-service-manifests.md) [configure um ponto final](service-fabric-service-manifest-resources.md)| 

Consulte [elementos manifestos de aplicação](#application-manifest-elements), [elementos manifestos do serviço VotingWeb](#votingweb-service-manifest-elements)e elementos manifestos do serviço [VotingData](#votingdata-service-manifest-elements) para obter mais informações sobre elementos XML específicos.

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
      <PackageSharingPolicy PackageRef="Data"/>

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

## <a name="votingweb-service-manifest"></a>Manifesto de serviço votingWeb

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

## <a name="votingdata-service-manifest"></a>Manifesto de serviço de Data de Voto

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

## <a name="application-manifest-elements"></a>Elementos manifestos de aplicação
### <a name="applicationmanifest-element"></a>Elemento de manifestação de aplicações
Declarativamente descreve o tipo e a versão da aplicação. Um ou mais manifestos de serviço dos serviços constituintes são referenciados para compor um tipo de aplicação. As definições de configuração dos serviços constitutivos podem ser sobreractivadas utilizando as definições de aplicação parametrizadas. Serviços predefinidos, modelos de serviço, diretores, políticas, configuração de diagnósticos e certificados também podem ser declarados ao nível da aplicação. Para mais informações, consulte [ApplicationManifest Element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Elemento de Parâmetros
Declara os parâmetros que são usados neste manifesto de aplicação. O valor destes parâmetros pode ser fornecido quando a aplicação é instantânea e pode ser usada para anular as definições de aplicação ou configuração do serviço. Para mais informações, consulte [Parametros Elemento](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Elemento parâmetro
Um parâmetro de aplicação a ser utilizado neste manifesto. O valor do parâmetro pode ser alterado durante a instantânea da aplicação, ou, se não for fornecido qualquer valor, o valor predefinido é utilizado. Para mais informações, consulte [Parameter Element](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>Elemento ServiceManifestImport
Importa um manifesto de serviço criado pelo desenvolvedor de serviços. Deve ser importado um manifesto de serviço para cada serviço constituinte do pedido. As substituições e políticas de configuração podem ser declaradas para o manifesto de serviço. Para mais informações, consulte [ServiceManifestImport Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>Elemento ServiceManifestRef
Importa o manifesto de serviço por referência. Atualmente, o ficheiro manifesto de serviço (ServiceManifest.xml) deve estar presente no pacote de construção. Para mais informações, consulte [ServiceManifestRef Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>Elemento de sobreutilização de recursos
Especifica as sobreposições de recursos para os pontos finais declarados em recursos manifestos de serviço. Para mais informações, consulte [ResourceOverrides Element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Elemento pontos finais
O ponto final para anular. Para mais informações, consulte [Endpoints Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Elemento endpoint
O ponto final, declarado no manifesto de serviço, para anular. Para mais informações, consulte [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Elemento de Políticas
Descreve as políticas (encadernação de ponto final, partilha de pacotes, execução e acesso à segurança) a aplicar no manifesto de serviço importado. Para mais informações, consulte [Elemento de Políticas](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Define a política de governação de recursos que é aplicada ao nível de todo o pacote de serviços. Para mais informações, consulte [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Elemento de Política de Governança de Recursos
Especifica os limites de recursos para um pacote de códigos. Para mais informações, consulte Elemento de [Política de Governança de Recursos](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>Elemento de política de partilha de pacotes
Indica se um código, config ou pacote de dados deve ser partilhado em instâncias de serviço do mesmo tipo de serviço. Para mais informações, consulte [PackageSharingPolicy Element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>Elemento de Política de Acesso de Segurança
Concede permissões de acesso a um principal num recurso (como um ponto final) definido num manifesto de serviço. Tipicamente, é muito útil controlar e restringir o acesso dos serviços a diferentes recursos de forma a minimizar os riscos de segurança. Isto é especialmente importante quando a aplicação é construída a partir de uma coleção de serviços de um mercado que são desenvolvidos por diferentes desenvolvedores. Para mais informações, consulte [SecurityAccessPolicy Element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>Elemento RunasPolicy
Especifica a conta do utilizador local ou do sistema local que um pacote de código de serviço será executado como. As contas de domínio são suportadas nas implementações do Windows Server onde o Azure Ative Directory está disponível. Por predefinição, as aplicações são executadas sob a conta que o processo Fabric.exe é executado. Os pedidos também podem ser executados como outras contas, que devem ser declaradas na secção de Principados. Se aplicar uma política de RunAs a um serviço, e o manifesto de serviço declarar recursos de ponto final com o protocolo HTTP, também deve especificar uma Política de Acesso de Segurança para garantir que as portas atribuídas a estes pontos finais são corretamente controlo de acesso listado para os RunAs conta utilizador que o serviço é executado abaixo. Para um ponto final HTTPS, também tem de definir uma Política EndpointBinding para indicar o nome do certificado para devolver ao cliente. Para mais informações, consulte [RunAsPolicy Element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>Elemento de Serviços Predefinidos
Declara instâncias de serviço que são automaticamente criadas sempre que uma aplicação é instantaneamente contra este tipo de aplicação. Para mais informações, consulte [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Elemento de Serviço
Declara um serviço a ser criado automaticamente quando a aplicação é instantânea. Para mais informações, consulte [Elemento de Serviço](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>Elemento StatefulService
Define um serviço imponente. Para mais informações, consulte [StatefulService Element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>Elemento apátrida
Define um serviço apátrida. Para mais informações, consulte [ApátonaService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Elemento principal
Descreve os principais de segurança (utilizadores, grupos) necessários para esta aplicação executar serviços e recursos seguros. Os diretores são referenciados nas secções de políticas. Para mais informações, consulte [O Elemento](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType) principal

### <a name="groups-element"></a>Elemento de Grupos
Declara um conjunto de grupos como diretores de segurança, que podem ser referenciados em políticas. Os grupos são úteis se houver vários utilizadores para diferentes pontos de entrada de serviço e precisam de ter certos privilégios comuns disponíveis a nível do grupo. Para mais informações, consulte [Grupos Elemento](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Elemento de Grupo
Declara um grupo como um diretor de segurança, que pode ser referenciado em políticas. Para mais informações, consulte [Grupo Elemento](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Elemento de Adesão
 Para mais informações, consulte [Elemento de Adesão](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>Elemento systemgroup
 Para mais informações, consulte [SystemGroup Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Elemento de Utilizadores
Declara um conjunto de utilizadores como diretores de segurança, que podem ser referenciados em políticas. Para mais informações, consulte [O Elemento dos Utilizadores](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Elemento do utilizador
Declara um utilizador como um diretor de segurança, que pode ser referenciado em políticas. Para mais informações, consulte [User Element](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>Elemento membro do elemento
Os utilizadores podem ser adicionados a qualquer grupo de membros existente, para que possa herdar todas as propriedades e configurações de segurança desse grupo de membros. O grupo de membros pode ser utilizado para garantir recursos externos que precisam de ser acedidos por diferentes serviços ou pelo mesmo serviço (numa máquina diferente). Para mais informações, consulte [MemberOf Element](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>Elemento systemgroup
O grupo do sistema para adicionar o utilizador.  O grupo do sistema deve ser definido na secção Grupos. Para mais informações, consulte [SystemGroup Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Elemento de Grupo
O grupo para adicionar o utilizador.  O grupo deve ser definido na secção Grupos. Para mais informações, consulte [Grupo Elemento](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Elemento de Políticas
Descreve as políticas (recolha de registos, execução padrão, saúde e acesso à segurança) a aplicar ao nível da aplicação. Para mais informações, consulte [Elemento de Políticas](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>Elemento de política de defaultrunas
Especifique uma conta de utilizador predefinida para todos os pacotes de códigode serviço que não tenham uma RunAsPolicy específica definida na secção ServiceManifestImport. Para mais informações, consulte [DefaultRunAsPolicy Element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Elementos manifestos do serviço VotingWeb
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Declarativamente descreve o tipo de serviço e a versão. Ele lista o código, configuração e pacotes de dados atualizados independentemente que, em conjunto, compõem um pacote de serviço para suportar um ou mais tipos de serviço. Os recursos, configurações de diagnóstico e metadados de serviço, tais como tipo de serviço, propriedades sanitárias e métricas de equilíbrio de carga, também são especificados. Para mais informações, consulte [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento de tipos de serviço
Define quais os tipos de serviço suportados por um CodePackage neste manifesto. Quando um serviço é instantaneamente contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Os tipos de serviço são declarados ao nível manifesto e não ao nível do pacote de código. Para mais informações, consulte [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento ApátridaServiceType
Descreve um tipo de serviço apátrida. Para mais informações, consulte [ApátonaServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento código pacote
Descreve um pacote de código que suporta um tipo de serviço definido. Quando um serviço é instantaneamente contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Espera-se que os processos resultantes registem os tipos de serviço suportados no tempo de execução. Quando existem vários pacotes de código, todos são ativados sempre que o sistema procura qualquer um dos tipos de serviço declarados. Para mais informações, consulte [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>Elemento De SetupEntryPoint
Um ponto de entrada privilegiado que, por padrão, corre com as mesmas credenciais que o Tecido de Serviço (normalmente a conta NETWORKSERVICE) antes de qualquer outro ponto de entrada. O executível especificado pelo EntryPoint é tipicamente o anfitrião de serviço de longa duração. A presença de um ponto de entrada separado evita ter de executar o anfitrião do serviço com privilégios elevados por longos períodos de tempo. Para mais informações, consulte [ConfiguraçãoElementoEntryPoint](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Para mais informações, consulte [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Elemento do programa
O nome executável.  Por exemplo, "MySetup.bat" ou "MyServiceHost.exe". Para mais informações, consulte [O Elemento](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType) do Programa

### <a name="arguments-element"></a>Elemento de Argumentos
 Para mais informações, consulte [Arguments Element](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Elemento da pasta de trabalho
O diretório de trabalho para o processo no pacote de código no nó de cluster onde a aplicação é implementada. Pode especificar três valores: Trabalhar (o padrão), CodePackage ou CodeBase. A CodeBase especifica que o diretório de trabalho está definido para o diretório em que o EXE é definido no pacote de código. O CodePackage define o diretório de trabalho como a raiz do pacote de código, independentemente do local onde o EXE é definido no diretório do pacote de código. O trabalho define o diretório de trabalho para uma pasta única criada no nó.  Esta pasta é a mesma para toda a instância de aplicação. Por predefinição, o diretório de trabalho de todos os processos da aplicação está definido para a pasta de trabalho da aplicação. É aqui que os processos podem escrever os dados. Não é recomendado escrever dados no pacote de códigoou base de código, uma vez que essas pastas podem ser partilhadas entre diferentes instâncias de aplicação e podem ser eliminadas. Para mais informações, consulte [WorkingFolder Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>Elemento de Reorientação da Consola

> [!WARNING]
> Não utilize a reorientação da consola numa aplicação de produção, utilize-a apenas para o desenvolvimento local e depuração. Redireciona a saída da consola do script de arranque para um ficheiro de saída na pasta de aplicação chamada "log" no nó de cluster onde a aplicação é implementada e executada. Para mais informações, consulte [ConsoleRedirection Element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>Elemento ponto de entrada
O executível especificado pelo EntryPoint é tipicamente o anfitrião de serviço de longa duração. A presença de um ponto de entrada separado evita ter de executar o anfitrião do serviço com privilégios elevados por longos períodos de tempo. O executível especificado pelo EntryPoint é executado após a saída de SetupEntryPoint com sucesso. O processo resultante é monitorizado e reiniciado (recomeçando novamente com SetupEntryPoint) se alguma vez terminar ou se despenhar. Para mais informações, consulte [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Para mais informações, consulte [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta, nomeada pelo atributo nome, em PackageRoot que contém um ficheiro Definições.xml. Este ficheiro contém secções de definições de par de valor chave definidos pelo utilizador que o processo pode ler no tempo de execução. Durante uma atualização, se apenas a versão ConfigPackage tiver mudado, então o processo de execução não é reiniciado. Em vez disso, um backback de chamada notifica o processo que as definições de configuração mudaram para que possam ser recarregadas dinamicamente. Para mais informações, consulte [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Elemento de Recursos
Descreve os recursos utilizados por este serviço, que podem ser declarados sem modificar o código compilado e alterados quando o serviço é implementado. O acesso a estes recursos é controlado através das secções de Principados e Políticas do manifesto de candidatura. Para mais informações, consulte [Elemento de Recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento pontos finais
Define pontos finais para o serviço. Para mais informações, consulte [Endpoints Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento endpoint
O ponto final, declarado no manifesto de serviço, para anular. Para mais informações, consulte [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Elementos manifestos do serviço De votoData
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Declarativamente descreve o tipo de serviço e a versão. Ele lista o código, configuração e pacotes de dados atualizados independentemente que, em conjunto, compõem um pacote de serviço para suportar um ou mais tipos de serviço. Os recursos, configurações de diagnóstico e metadados de serviço, tais como tipo de serviço, propriedades sanitárias e métricas de equilíbrio de carga, também são especificados. Para mais informações, consulte [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento de tipos de serviço
Define quais os tipos de serviço suportados por um CodePackage neste manifesto. Quando um serviço é instantaneamente contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Os tipos de serviço são declarados ao nível manifesto e não ao nível do pacote de código. Para mais informações, consulte [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>Elemento StatefulServiceType
Descreve um tipo de serviço imponente. Para mais informações, consulte [StatefulServiceType Element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento código pacote
Descreve um pacote de código que suporta um tipo de serviço definido. Quando um serviço é instantaneamente contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Espera-se que os processos resultantes registem os tipos de serviço suportados no tempo de execução. Quando existem vários pacotes de código, todos são ativados sempre que o sistema procura qualquer um dos tipos de serviço declarados. Para mais informações, consulte [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento ponto de entrada
O executível especificado pelo EntryPoint é tipicamente o anfitrião de serviço de longa duração. A presença de um ponto de entrada separado evita ter de executar o anfitrião do serviço com privilégios elevados por longos períodos de tempo. O executível especificado pelo EntryPoint é executado após a saída de SetupEntryPoint com sucesso. O processo resultante é monitorizado e reiniciado (recomeçando novamente com SetupEntryPoint) se alguma vez terminar ou se despenhar. Para mais informações, consulte [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Para mais informações, consulte [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Elemento do programa
O nome executável.  Por exemplo, "MySetup.bat" ou "MyServiceHost.exe". Para mais informações, consulte [O Elemento](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType) do Programa

### <a name="workingfolder-element"></a>Elemento da pasta de trabalho
O diretório de trabalho para o processo no pacote de código no nó de cluster onde a aplicação é implementada. Pode especificar três valores: Trabalhar (o padrão), CodePackage ou CodeBase. A CodeBase especifica que o diretório de trabalho está definido para o diretório em que o EXE é definido no pacote de código. O CodePackage define o diretório de trabalho como a raiz do pacote de código, independentemente do local onde o EXE é definido no diretório do pacote de código. O trabalho define o diretório de trabalho para uma pasta única criada no nó.  Esta pasta é a mesma para toda a instância de aplicação. Por predefinição, o diretório de trabalho de todos os processos da aplicação está definido para a pasta de trabalho da aplicação. É aqui que os processos podem escrever os dados. Não é recomendado escrever dados no pacote de códigoou base de código, uma vez que essas pastas podem ser partilhadas entre diferentes instâncias de aplicação e podem ser eliminadas. Para mais informações, consulte [WorkingFolder Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta, nomeada pelo atributo nome, em PackageRoot que contém um ficheiro Definições.xml. Este ficheiro contém secções de definições de par de valor chave definidos pelo utilizador que o processo pode ler no tempo de execução. Durante uma atualização, se apenas a versão ConfigPackage tiver mudado, então o processo de execução não é reiniciado. Em vez disso, um backback de chamada notifica o processo que as definições de configuração mudaram para que possam ser recarregadas dinamicamente. Para mais informações, consulte [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Elemento datapackage
Declara uma pasta, nomeada pelo atributo nome, no âmbito do PackageRoot, que contém ficheiros de dados estáticos a serem consumidos pelo processo no momento da execução. O Tecido de Serviço reciclará todos os EX Ese e DLLHOSTs especificados nos pacotes de hospedaria e suporte quando qualquer um dos pacotes de dados listados no manifesto de serviço for atualizado. Para mais informações, consulte [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Elemento de Recursos
Descreve os recursos utilizados por este serviço, que podem ser declarados sem modificar o código compilado e alterados quando o serviço é implementado. O acesso a estes recursos é controlado através das secções de Principados e Políticas do manifesto de candidatura. Para mais informações, consulte [Elemento de Recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento pontos finais
Define pontos finais para o serviço. Para mais informações, consulte [Endpoints Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento endpoint
O ponto final, declarado no manifesto de serviço, para anular. Para mais informações, consulte [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

