---
title: Aplicativo de recipiente de tecido de serviço Azure exemplos manifesto
description: Saiba como configurar as definições de formulário de aplicação e de serviço para uma aplicação multi-contentores Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79258398"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Exemplos de manifesto e aplicação e serviço de múltiplos contentores
Seguem-se exemplos dos manifestos de aplicação e serviço para uma aplicação multi-contentores Service Fabric. O objetivo destes exemplos é mostrar quais as configurações disponíveis e como usá-las. Estes manifestos de aplicação e serviço são baseados nos manifestos de amostra de [contentores do Windows Server 2016.](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows)

As seguintes características são mostradas:

|Manifesto|Funcionalidades|
|---|---|
|[Manifesto de aplicação](#application-manifest)| [sobrepor variáveis ambientais](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurar mapeamento porta-a-hospedeiro,](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery) [configurar autenticação do registo de contentores,](service-fabric-get-started-containers.md#configure-container-repository-authentication) [governação de recursos,](service-fabric-resource-governance.md) [modo de isolamento definido,](service-fabric-get-started-containers.md#configure-isolation-mode) [especificar imagens de contentores específicos da construção de OS](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Manifesto de serviço FrontEndService](#frontendservice-service-manifest)| [definir variáveis ambientais,](service-fabric-get-started-containers.md#configure-and-set-environment-variables) [configurar um ponto final,](service-fabric-get-started-containers.md#configure-communication)passar comandos para o contentor, [importar um certificado em um recipiente](service-fabric-securing-containers.md)| 
|[Manifesto de serviço BackEndService](#backendservice-service-manifest)|[definir variáveis ambientais,](service-fabric-get-started-containers.md#configure-and-set-environment-variables) [configurar um ponto final,](service-fabric-get-started-containers.md#configure-communication) [configurar volume driver](service-fabric-containers-volume-logging-drivers.md)| 

Consulte [elementos manifestos de aplicação,](#application-manifest-elements) [elementos manifestos do serviço FrontEndService](#frontendservice-service-manifest-elements)e elementos manifestos do serviço [BackEndService](#backendservice-service-manifest-elements) para obter mais informações sobre elementos XML específicos.

## <a name="application-manifest"></a>Manifesto de aplicação

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>Manifesto de serviço FrontEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>Manifesto de serviço BackEndService

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
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

### <a name="policies-element"></a>Elemento de Políticas
Descreve as políticas (encadernação de ponto final, partilha de pacotes, execução e acesso à segurança) a aplicar no manifesto de serviço importado. Para mais informações, consulte [Elemento de Políticas](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>Elemento de política de governança de recursos de pacotede serviços
Define a política de governação de recursos que é aplicada ao nível de todo o pacote de serviços. Para mais informações, consulte [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Elemento de Política de Governança de Recursos
Especifica os limites de recursos para um pacote de código. Para mais informações, consulte Elemento de [Política de Governança de Recursos](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>Elemento de políticas de anfitriões de contentores
Especifica as políticas de ativação dos hospedeiros de contentores. Para mais informações, consulte [ContainerHostPolicies Element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>Elemento repositóriocredenciais
Credenciais para o repositório de imagem do recipiente para retirar imagens de. Para mais informações, consulte [RepositoryCredentials Element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>Elemento de encadernação de porta
Especifica qual o recurso de ponta final para se ligar à porta do contentor exposta. Para mais informações, consulte [PortaBinding Element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Elemento de volume
Especifica o volume a ser ligado ao recipiente. Para mais informações, consulte [Elemento de Volume](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>Elemento de opção de condutor
Opções de motorista a serem passadas para o motorista. Para mais informações, consulte [DriverOption Element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>Elemento de sobrepasseios de imagem
Os recipientes do Windows Server podem não ser compatíveis em diferentes versões do SISTEMA.  Pode especificar várias imagens de SO por recipiente e marcá-las com as versões de construção do SISTEMA. Obtenha a versão de construção do SISTEMA executando "winver" num pedido de comando do Windows. Se o SISTEMA subjacente for a versão 16299 (versão 1709 do Windows Server), o Service Fabric escolhe a imagem do recipiente marcada com Os="16299". Presume-se que uma imagem de recipiente não marcada funciona em todas as versões do SISTEMA e sobrepõe-se à imagem especificada no manifesto de serviço. Para mais informações, consulte [ImageOverrides Element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Elemento de imagem
Imagem de contentor correspondente ao número de versão de construção de OS a ser lançada. Se o atributo De Os não for especificado, presume-se que a imagem do recipiente funciona em todas as versões do SISTEMA e sobrepõe-se à imagem especificada no manifesto de serviço. Para mais informações, consulte [Elemento de Imagem](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>Elemento de sobreposições ambientais
 Para mais informações, consulte [EnvironmentOverrides Element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Elemento variável ambiente
Variável ambiental. Para mais informações, consulte [Elemento Variável ambiente](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>Elemento CertificateRef
Especifica informações sobre um certificado X509 que deve ser exposto ao ambiente do contentor. O certificado deve ser instalado na loja LocalMachine de todos os nós do cluster.
Quando a aplicação começa, o tempo de execução lê o certificado e gera um ficheiro PFX e uma palavra-passe (no Windows) ou um ficheiro PEM (no Linux).
O ficheiro PFX e a palavra-passe estão acessíveis no recipiente utilizando as variáveis ambiente Certificates_ServicePackageName_CodePackageName_CertName_PFX e Certificates_ServicePackageName_CodePackageName_CertName_Password. O ficheiro PEM é acessível no recipiente utilizando as variáveis ambiente Certificates_ServicePackageName_CodePackageName_CertName_PEM e Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey. Para mais informações, consulte [CertificateRef Element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>Elemento de Serviços Predefinidos
Declara instâncias de serviço que são automaticamente criadas sempre que uma aplicação é instantaneamente contra este tipo de aplicação. Para mais informações, consulte [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Elemento de Serviço
Declara um serviço a ser criado automaticamente quando a aplicação é instantânea. Para mais informações, consulte [Elemento de Serviço](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>Elemento apátrida
Define um serviço apátrida. Para mais informações, consulte [ApátonaService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Elementos manifestos de manifesto do serviço FrontEndService
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Declarativamente descreve o tipo de serviço e a versão. Ele lista o código, configuração e pacotes de dados atualizados independentemente que, em conjunto, compõem um pacote de serviço para suportar um ou mais tipos de serviço. Os recursos, configurações de diagnóstico e metadados de serviço, tais como tipo de serviço, propriedades sanitárias e métricas de equilíbrio de carga, também são especificados. Para mais informações, consulte [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento de tipos de serviço
Define quais os tipos de serviço suportados por um CodePackage neste manifesto. Quando um serviço é instantaneamente contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Os tipos de serviço são declarados ao nível manifesto e não ao nível do pacote de código. Para mais informações, consulte [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento ApátridaServiceType
Descreve um tipo de serviço apátrida. Para mais informações, consulte [ApátonaServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento código pacote
Descreve um pacote de código que suporta um tipo de serviço definido. Quando um serviço é instantaneamente contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Espera-se que os processos resultantes registem os tipos de serviço suportados no tempo de execução. Quando existem vários pacotes de código, todos são ativados sempre que o sistema procura qualquer um dos tipos de serviço declarados. Para mais informações, consulte [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento ponto de entrada
O executível especificado pelo EntryPoint é tipicamente o anfitrião de serviço de longa duração. A presença de um ponto de entrada separado evita ter de executar o anfitrião do serviço com privilégios elevados por longos períodos de tempo. O executível especificado pelo EntryPoint é executado após a saída de SetupEntryPoint com sucesso. O processo resultante é monitorizado e reiniciado (recomeçando novamente com SetupEntryPoint) se alguma vez terminar ou se despenhar. Para mais informações, consulte [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>Elemento de hospedeiro de contentores
 Para mais informações, consulte [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Elemento nome de imagem
O repo e [https://hub.docker.com](https://hub.docker.com) a imagem no registo de contentores Azure. Para mais informações, consulte [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Elemento de Variáveis Ambientais
Passe variáveis ambientais para o seu recipiente ou exe.  Para mais informações, consulte [Elemento de Variáveis Ambientais](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Elemento variável ambiente
Variável ambiental. Para mais informações, consulte [Elemento Variável ambiente](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta, nomeada pelo atributo nome, que contém um ficheiro Definições.xml. Este ficheiro contém secções de definições de par de valor chave definidos pelo utilizador que o processo pode ler no tempo de execução. Durante uma atualização, se apenas a versão ConfigPackage tiver mudado, então o processo de execução não é reiniciado. Em vez disso, um backback de chamada notifica o processo que as definições de configuração mudaram para que possam ser recarregadas dinamicamente. Para mais informações, consulte [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Elemento datapackage
Declara uma pasta, nomeada pelo atributo nome, que contém ficheiros de dados estáticos. O Tecido de Serviço reciclará todos os EX Ese e DLLHOSTs especificados nos pacotes de hospedaria e suporte quando qualquer um dos pacotes de dados listados no manifesto de serviço for atualizado. Para mais informações, consulte [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Elemento de Recursos
Descreve os recursos utilizados por este serviço, que podem ser declarados sem modificar o código compilado e alterados quando o serviço é implementado. O acesso a estes recursos é controlado através das secções de Principados e Políticas do manifesto de candidatura. Para mais informações, consulte [Elemento de Recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento pontos finais
Define pontos finais para o serviço. Para mais informações, consulte [Endpoints Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento endpoint
Para mais informações, consulte [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Elementos manifestos de manifesto do serviço BackEndService
### <a name="servicemanifest-element"></a>Elemento ServiceManifest
Declarativamente descreve o tipo de serviço e a versão. Ele lista o código, configuração e pacotes de dados atualizados independentemente que, em conjunto, compõem um pacote de serviço para suportar um ou mais tipos de serviço. Os recursos, configurações de diagnóstico e metadados de serviço, tais como tipo de serviço, propriedades sanitárias e métricas de equilíbrio de carga, também são especificados. Para mais informações, consulte [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento de tipos de serviço
Define quais os tipos de serviço suportados por um CodePackage neste manifesto. Quando um serviço é instantaneamente contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Os tipos de serviço são declarados ao nível manifesto e não ao nível do pacote de código. Para mais informações, consulte [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento ApátridaServiceType
Descreve um tipo de serviço apátrida. Para mais informações, consulte [ApátonaServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento código pacote
Descreve um pacote de código que suporta um tipo de serviço definido. Quando um serviço é instantaneamente contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Espera-se que os processos resultantes registem os tipos de serviço suportados no tempo de execução. Quando existem vários pacotes de código, todos são ativados sempre que o sistema procura qualquer um dos tipos de serviço declarados. Para mais informações, consulte [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento ponto de entrada
O executível especificado pelo EntryPoint é tipicamente o anfitrião de serviço de longa duração. A presença de um ponto de entrada separado evita ter de executar o anfitrião do serviço com privilégios elevados por longos períodos de tempo. O executível especificado pelo EntryPoint é executado após a saída de SetupEntryPoint com sucesso. O processo resultante é monitorizado e reiniciado (recomeçando novamente com SetupEntryPoint) se alguma vez terminar ou se despenhar. Para mais informações, consulte [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>Elemento de hospedeiro de contentores
Para mais informações, consulte [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Elemento nome de imagem
O repo e [https://hub.docker.com](https://hub.docker.com) a imagem no registo de contentores Azure. Para mais informações, consulte [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Elemento de comandos
Passe uma vírvia delimitada lista de comandos para o contentor. Para mais informações, consulte [Elemento de Comandos](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Elemento de Variáveis Ambientais
Passe variáveis ambientais para o seu recipiente ou exe.  Para mais informações, consulte [Elemento de Variáveis Ambientais](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Elemento variável ambiente
Variável ambiental. Para mais informações, consulte [Elemento Variável ambiente](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta, nomeada pelo atributo nome, que contém um ficheiro Definições.xml. Este ficheiro contém secções de definições de par de valor chave definidos pelo utilizador que o processo pode ler no tempo de execução. Durante uma atualização, se apenas a versão ConfigPackage tiver mudado, então o processo de execução não é reiniciado. Em vez disso, um backback de chamada notifica o processo que as definições de configuração mudaram para que possam ser recarregadas dinamicamente. Para mais informações, consulte [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Elemento de Recursos
Descreve os recursos utilizados por este serviço, que podem ser declarados sem modificar o código compilado e alterados quando o serviço é implementado. O acesso a estes recursos é controlado através das secções de Principados e Políticas do manifesto de candidatura. Para mais informações, consulte [Elemento de Recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento pontos finais
Define pontos finais para o serviço. Para mais informações, consulte [Endpoints Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento endpoint
 Para mais informações, consulte [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

