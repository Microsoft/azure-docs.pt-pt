---
title: Exemplos manifestos de aplicações de serviços fiáveis
description: Saiba como configurar configurações de aplicações e manifesto de serviço para uma aplicação de tecido de serviço de serviço confiável.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84701103"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Exemplos de manifesto e aplicação e serviço do Reliable Services
Seguem-se exemplos da aplicação e manifestos de serviço para uma aplicação Service Fabric com uma ASP.NET Core web front-end e um back-end imponente. O objetivo destes exemplos é mostrar quais as configurações disponíveis e como usá-las. Estes manifestos de aplicação e serviço baseiam-se nos manifestos do [Service Fabric .NET Quickstart.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)

São mostradas as seguintes características:

|Manifesto|Funcionalidades|
|---|---|
|[Manifesto de aplicação](#application-manifest)| [governação de recursos](service-fabric-resource-governance.md), [executar um serviço como conta de administração local](service-fabric-application-runas-security.md), aplicar uma política [predefinida a todos os pacotes de código de serviço,](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)criar [princípios de utilizador e grupo,](service-fabric-application-runas-security.md)partilhar um pacote de dados entre instâncias de serviço, [sobrepor pontos finais de serviço](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|Manifesto de serviço FrontEndService| [Executar um script no arranque de serviço,](service-fabric-run-script-at-service-startup.md) [definir um ponto final HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|Manifesto de serviço backEndService| [Declare um pacote config](service-fabric-application-and-service-manifests.md), [declare um pacote de dados,](service-fabric-application-and-service-manifests.md) [configure um ponto final](service-fabric-service-manifest-resources.md)| 

Consulte [elementos manifestos de aplicação,](#application-manifest-elements) [elementos manifestos do serviço VotingWeb](#votingweb-service-manifest-elements)e [elementos manifestos do serviço VoteData](#votingdata-service-manifest-elements) para obter mais informações sobre elementos XML específicos.

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

## <a name="votingdata-service-manifest"></a>Manifesto de serviço de votoData

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
### <a name="applicationmanifest-element"></a>Elemento De AplicaçãoManifest
Declarativamente descreve o tipo de aplicação e a versão. Um ou mais manifestos de serviço dos serviços constitutivo são referenciados para compor um tipo de aplicação. As definições de configuração dos serviços constituentes podem ser ultrapassadas utilizando definições de aplicação parametrizadas. Os serviços predefinidos, modelos de serviço, principais, políticas, configuração de diagnósticos e certificados também podem ser declarados ao nível da aplicação. Para mais informações, consulte [o Elemento ApplicationManifest](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Elemento parâmetros
Declara os parâmetros utilizados neste manifesto de aplicação. O valor destes parâmetros pode ser fornecido quando a aplicação é instantânea e pode ser usada para substituir as definições de aplicação ou configuração de serviço. Para mais informações, consulte [o Elemento Parâmetros](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Elemento parâmetro
Um parâmetro de aplicação a utilizar neste manifesto. O valor do parâmetro pode ser alterado durante a instantânea da aplicação ou, se não for fornecido qualquer valor, o valor padrão é utilizado. Para mais informações, consulte [o Elemento Parâmetro](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>Elemento De ServiçoManifestImport
Importa um manifesto de serviço criado pelo promotor de serviços. Deve ser importado um manifesto de serviço para cada serviço constituinte no pedido. As substituições de configuração e as políticas podem ser declaradas para o manifesto de serviço. Para mais informações, consulte [ServiceManifestImport Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>Elemento De ServiçoManifestRef
Importa o manifesto de serviço por referência. Atualmente, o ficheiro manifesto de serviço (ServiceManifest.xml) deve estar presente no pacote de construção. Para mais informações, consulte [ServiceManifestRef Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>Elemento Desemamentos de Recursos
Especifica a sobreposição de recursos para pontos finais declarados em recursos manifestos de serviço. Para mais informações, consulte [Elemento RecursosOverrides](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Elemento pontos finais
O ponto final para anular. Para mais informações, consulte [o Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Elemento de ponto final
O ponto final, declarado no manifesto de serviço, para anular. Para mais informações, consulte [o Elemento Ponto final](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Elemento de Políticas
Descreve políticas (ligação de ponto final, partilha de pacotes, run-as e acesso à segurança) a aplicar no manifesto de serviço importado. Para mais informações, consulte [Elemento de Políticas](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>Elemento de política de administração da ServicePackageResource
Define a política de governação de recursos que é aplicada ao nível de todo o pacote de serviços. Para mais informações, consulte [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Elemento de política de governo de recursos
Especifica limites de recursos para uma codepackage. Para mais informações, consulte Elemento de Política [de Administração de Recursos](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>Elemento de política de partilha de pacotes
Indica se um pacote de código, config ou dados deve ser partilhado em todas as instâncias de serviço do mesmo tipo de serviço. Para mais informações, consulte [PackageSharingPolicy Element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>Elemento de SegurançaAccessPolicy
Concede permissões de acesso a um principal sobre um recurso (como um ponto final) definido num manifesto de serviço. Normalmente, é muito útil controlar e restringir o acesso de serviços a diferentes recursos de forma a minimizar os riscos de segurança. Isto é especialmente importante quando a aplicação é construída a partir de uma coleção de serviços de um mercado que são desenvolvidos por diferentes desenvolvedores. Para mais informações, consulte [Elemento de SegurançaAccessPolicy](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>Elemento runAsPolicy
Especifica a conta de utilizador local ou sistema local que um pacote de código de serviço será executado como. As contas de domínio são suportadas nas implementações do Windows Server onde o Azure Ative Directory está disponível. Por predefinição, as aplicações são executadas na conta que o processo de Fabric.exe é executado. As candidaturas também podem ser executadas como outras contas, que devem ser declaradas na secção principais. Se aplicar uma política RunAs a um serviço, e o manifesto de serviço declarar recursos de ponto final com o protocolo HTTP, também deve especificar uma SecurityAccessPolicy para garantir que as portas atribuídas a estes pontos finais estejam corretamente listadas para a conta de utilizador RunAs que o serviço executa. Para um ponto final HTTPS, também tem de definir uma EndpointBindingPolicy para indicar o nome do certificado para devolver ao cliente. Para mais informações, consulte [RunAsPolicy Element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>Elemento de Serviços Padrão
Declara casos de serviço que são automaticamente criados sempre que uma aplicação é instantânea contra este tipo de aplicação. Para mais informações, consulte [o Elemento DefaultServices](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Elemento de serviço
Declara um serviço a ser criado automaticamente quando a aplicação é instantânea. Para mais informações, consulte [Elemento de Serviço](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>Elemento StatefulService
Define um serviço estatal. Para mais informações, consulte [o StatefulService Element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>Elemento de Serviço apátrida
Define um serviço apátrida. Para mais informações, consulte [elemento statelessService](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Elemento principal
Descreve os princípios de segurança (utilizadores, grupos) necessários para que esta aplicação gere serviços e recursos seguros. Os principais são referenciados nas secções políticas. Para mais informações, consulte [o Elemento Principais](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Elemento de Grupos
Declara um conjunto de grupos como princípios de segurança, que podem ser referenciados em políticas. Os grupos são úteis se houver vários utilizadores para diferentes pontos de entrada de serviço e precisam de ter certos privilégios comuns que estão disponíveis a nível de grupo. Para mais informações, consulte [Elemento grupos](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Elemento de Grupo
Declara um grupo como um principal de segurança, que pode ser referenciado em políticas. Para mais informações, consulte [Elemento de Grupo](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Elemento de adesão
 Para mais informações, consulte [Elemento de Adesão](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>Elemento de Grupo de Sistema
 Para mais informações, consulte [o Elemento Do Grupo de Sistema](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Elemento dos utilizadores
Declara um conjunto de utilizadores como principais de segurança, que podem ser referenciados em políticas. Para mais informações, consulte [o Elemento de Utilizadores](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Elemento de utilizador
Declara um utilizador como um principal de segurança, que pode ser referenciado em políticas. Para mais informações, consulte [o Elemento de Utilizador](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>Elemento MembroOf
Os utilizadores podem ser adicionados a qualquer grupo de membros existente, para que possa herdar todas as propriedades e configurações de segurança desse grupo de membros. O grupo de membros pode ser utilizado para garantir recursos externos que precisam de ser acedidos por diferentes serviços ou pelo mesmo serviço (numa máquina diferente). Para mais informações, consulte [Elemento MembroOf](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>Elemento de Grupo de Sistema
O grupo de sistema para adicionar o utilizador.  O grupo de sistema deve ser definido na secção Grupos. Para mais informações, consulte [o Elemento Do Grupo de Sistema](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Elemento de Grupo
O grupo para adicionar o utilizador.  O grupo deve ser definido na secção Grupos. Para mais informações, consulte [Elemento de Grupo](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Elemento de Políticas
Descreve as políticas (recolha de registos, execução padrão, saúde e acesso à segurança) a aplicar ao nível da aplicação. Para mais informações, consulte [Elemento de Políticas](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>Elemento de política defaultRunAs
Especifique uma conta de utilizador predefinida para todos os pacotes de código de serviço que não tenham uma RunAsPolicy específica definida na secção ServiceManifestImport. Para mais informações, consulte [o Elemento DefaultRunAsPolicy](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Elementos manifestos do serviço VotingWeb
### <a name="servicemanifest-element"></a>Elemento De ServiçoManifest
Declarativamente descreve o tipo de serviço e a versão. Ele lista os pacotes de código, configuração e dados de forma independente que, em conjunto, compõem um pacote de serviços para suportar um ou mais tipos de serviço. São também especificados recursos, definições de diagnóstico e metadados de serviço, tais como tipo de serviço, propriedades sanitárias e métricas de equilíbrio de carga. Para mais informações, consulte [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento deTipos de Serviço
Define quais os tipos de serviço suportados por um CodePackage neste manifesto. Quando um serviço é instantâneo contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Os tipos de serviço são declarados ao nível manifesto e não ao nível do pacote de código. Para mais informações, consulte [o ElementoTips de Serviço](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento de Tipo de Serviço Apátrida
Descreve um tipo de serviço apátrida. Para mais informações, consulte [elemento Apátrida](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento codepackage
Descreve um pacote de código que suporta um tipo de serviço definido. Quando um serviço é instantâneo contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Espera-se que os processos resultantes registem os tipos de serviço suportados no tempo de execução. Quando existem vários pacotes de código, todos são ativados sempre que o sistema procura qualquer um dos tipos de serviço declarados. Para mais informações, consulte [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>Elemento ConfigurarEntryPoint
Um ponto de entrada privilegiado que, por padrão, funciona com as mesmas credenciais que o Service Fabric (normalmente a conta NETWORKSERVICE) antes de qualquer outro ponto de entrada. O executável especificado pelo EntryPoint é tipicamente o anfitrião de serviço de longa duração. A presença de um ponto de entrada de configuração separado evita ter de executar o hospedeiro de serviço com privilégios elevados por longos períodos de tempo. Para mais informações, consulte [Elemento ConfigurarPoint](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Para mais informações, consulte [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Elemento de Programa
O nome executável.  Por exemplo, "MySetup.bat" ou "MyServiceHost.exe". Para mais informações, consulte [Elemento de Programa](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Elemento de argumentos
 Para mais informações, consulte [Elemento de Argumentos](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Elemento WorkingFolder
O diretório de trabalho para o processo no pacote de código no nó de cluster onde a aplicação é implantada. Pode especificar três valores: Trabalho (o padrão), CodePackage ou CodeBase. A CodeBase especifica que o diretório de trabalho é definido para o diretório em que o EXE é definido no pacote de código. O CodePackage define o diretório de trabalho como a raiz do pacote de código, independentemente do local onde o EXE esteja definido no diretório de pacotes de código. O trabalho define o diretório de trabalho para uma pasta única criada no nó.  Esta pasta é a mesma para toda a aplicação. Por predefinição, o diretório de trabalho de todos os processos da aplicação está definido para a pasta de trabalho da aplicação. É aqui que os processos podem escrever os dados. A escrita de dados no pacote de código ou na base de código não é recomendada, uma vez que estas pastas podem ser partilhadas entre diferentes instâncias de aplicação e podem ser eliminadas. Para mais informações, consulte [o Elemento WorkingFolder](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>Elemento de redirecionar consola

> [!WARNING]
> Não utilize a reorientação da consola numa aplicação de produção, utilize-a apenas para desenvolvimento local e depuração. Redireciona a saída da consola do script de arranque para um ficheiro de saída na pasta de aplicação chamada "log" no nó de cluster onde a aplicação é implantada e executada. Para mais informações, consulte [o Elemento de Redirecionar consola](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>Elemento ponto de entrada
O executável especificado pelo EntryPoint é tipicamente o anfitrião de serviço de longa duração. A presença de um ponto de entrada de configuração separado evita ter de executar o hospedeiro de serviço com privilégios elevados por longos períodos de tempo. O executável especificado pelo EntryPoint é executado após a saída de SetupEntryPoint com sucesso. O processo resultante é monitorizado e reiniciado (começando novamente com o SetupEntryPoint) se alguma vez terminar ou falhar. Para mais informações, consulte [o Elemento Ponto de Entrada](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Para mais informações, consulte [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta, nomeada pelo atributo Nome, em PackageRoot que contém um ficheiro Settings.xml. Este ficheiro contém secções de definições de pares de valor chave definidas pelo utilizador que o processo pode ler no tempo de execução. Durante uma atualização, se apenas a versão ConfigPackage tiver mudado, então o processo de execução não é reiniciado. Em vez disso, uma chamada notifica o processo que as definições de configuração mudaram para que possam ser recarregadas dinamicamente. Para mais informações, consulte [o Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Elemento de Recursos
Descreve os recursos utilizados por este serviço, que podem ser declarados sem modificar código compilado e alterados quando o serviço é implantado. O acesso a estes recursos é controlado através das secções principais e políticas do manifesto de candidatura. Para mais informações, consulte [Elemento recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento pontos finais
Define pontos finais para o serviço. Para mais informações, consulte [o Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento de ponto final
O ponto final, declarado no manifesto de serviço, para anular. Para mais informações, consulte [o Elemento Ponto final](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Elementos manifestos do serviço voteData
### <a name="servicemanifest-element"></a>Elemento De ServiçoManifest
Declarativamente descreve o tipo de serviço e a versão. Ele lista os pacotes de código, configuração e dados de forma independente que, em conjunto, compõem um pacote de serviços para suportar um ou mais tipos de serviço. São também especificados recursos, definições de diagnóstico e metadados de serviço, tais como tipo de serviço, propriedades sanitárias e métricas de equilíbrio de carga. Para mais informações, consulte [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento deTipos de Serviço
Define quais os tipos de serviço suportados por um CodePackage neste manifesto. Quando um serviço é instantâneo contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Os tipos de serviço são declarados ao nível manifesto e não ao nível do pacote de código. Para mais informações, consulte [o ElementoTips de Serviço](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>Elemento StatefulServiceType
Descreve um tipo de serviço imponente. Para mais informações, consulte [o StatefulServiceType Element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento codepackage
Descreve um pacote de código que suporta um tipo de serviço definido. Quando um serviço é instantâneo contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Espera-se que os processos resultantes registem os tipos de serviço suportados no tempo de execução. Quando existem vários pacotes de código, todos são ativados sempre que o sistema procura qualquer um dos tipos de serviço declarados. Para mais informações, consulte [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento ponto de entrada
O executável especificado pelo EntryPoint é tipicamente o anfitrião de serviço de longa duração. A presença de um ponto de entrada de configuração separado evita ter de executar o hospedeiro de serviço com privilégios elevados por longos períodos de tempo. O executável especificado pelo EntryPoint é executado após a saída de SetupEntryPoint com sucesso. O processo resultante é monitorizado e reiniciado (começando novamente com o SetupEntryPoint) se alguma vez terminar ou falhar. Para mais informações, consulte [o Elemento Ponto de Entrada](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>Elemento ExeHost
 Para mais informações, consulte [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Elemento de Programa
O nome executável.  Por exemplo, "MySetup.bat" ou "MyServiceHost.exe". Para mais informações, consulte [Elemento de Programa](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Elemento WorkingFolder
O diretório de trabalho para o processo no pacote de código no nó de cluster onde a aplicação é implantada. Pode especificar três valores: Trabalho (o padrão), CodePackage ou CodeBase. A CodeBase especifica que o diretório de trabalho é definido para o diretório em que o EXE é definido no pacote de código. O CodePackage define o diretório de trabalho como a raiz do pacote de código, independentemente do local onde o EXE esteja definido no diretório de pacotes de código. O trabalho define o diretório de trabalho para uma pasta única criada no nó.  Esta pasta é a mesma para toda a aplicação. Por predefinição, o diretório de trabalho de todos os processos da aplicação está definido para a pasta de trabalho da aplicação. É aqui que os processos podem escrever os dados. A escrita de dados no pacote de código ou na base de código não é recomendada, uma vez que estas pastas podem ser partilhadas entre diferentes instâncias de aplicação e podem ser eliminadas. Para mais informações, consulte [o Elemento WorkingFolder](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta, nomeada pelo atributo Nome, em PackageRoot que contém um ficheiro Settings.xml. Este ficheiro contém secções de definições de pares de valor chave definidas pelo utilizador que o processo pode ler no tempo de execução. Durante uma atualização, se apenas a versão ConfigPackage tiver mudado, então o processo de execução não é reiniciado. Em vez disso, uma chamada notifica o processo que as definições de configuração mudaram para que possam ser recarregadas dinamicamente. Para mais informações, consulte [o Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Elemento de Embalagem de Dados
Declara uma pasta, nomeada pelo atributo Name, no PackageRoot que contém ficheiros de dados estáticos para serem consumidos pelo processo no tempo de execução. O Service Fabric reciclará todos os EXEs e DLLHOSTs especificados nos pacotes de anfitrião e suporte quando qualquer um dos pacotes de dados listados no manifesto de serviço for atualizado. Para mais informações, consulte [o Elemento DataPackage](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Elemento de Recursos
Descreve os recursos utilizados por este serviço, que podem ser declarados sem modificar código compilado e alterados quando o serviço é implantado. O acesso a estes recursos é controlado através das secções principais e políticas do manifesto de candidatura. Para mais informações, consulte [Elemento recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento pontos finais
Define pontos finais para o serviço. Para mais informações, consulte [o Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento de ponto final
O ponto final, declarado no manifesto de serviço, para anular. Para mais informações, consulte [o Elemento Ponto final](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

