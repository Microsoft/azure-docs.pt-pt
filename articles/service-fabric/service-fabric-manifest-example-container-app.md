---
title: Exemplos do Azure Service Fabric contentor aplicação manifestos | Documentos da Microsoft
description: Saiba como configurar a aplicação e definições de manifestos de aplicação do Service Fabric com vários contentor do serviço.
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
ms.date: 06/08/2018
ms.author: ryanwi
ms.openlocfilehash: f358080b3bcada5515f578ad2215fa2b135c2f2d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57878334"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Exemplos de manifesto e aplicação e serviço de múltiplos contentores
Seguem-se exemplos de manifestos de aplicações e serviços para uma aplicação do Service Fabric com vários contentor. O objetivo destes exemplos é mostrar quais configurações estão disponíveis e como utilizá-los. Esses manifestos de aplicações e serviços são baseiam o [exemplo de contentor do Windows Server 2016](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) manifestos.

São apresentadas as seguintes funcionalidades:

|Manifesto|Funcionalidades|
|---|---|
|[Manifesto da aplicação](#application-manifest)| [substituir as variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurar o mapeamento de porta do contentor-a-anfitrião](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [configurar a autenticação de registo de contentor](service-fabric-get-started-containers.md#configure-container-registry-authentication), [governação de recursos](service-fabric-resource-governance.md), [definir o modo de isolamento](service-fabric-get-started-containers.md#configure-isolation-mode), [especificar imagens de contentor específicas de compilação do SO](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Manifesto do serviço de FrontEndService](#frontendservice-service-manifest)| [definir variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurar um ponto de extremidade](service-fabric-get-started-containers.md#configure-communication), passe comandos para o contentor, [importar um certificado para um contentor](service-fabric-securing-containers.md)| 
|[Manifesto do serviço de BackEndService](#backendservice-service-manifest)|[definir variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurar um ponto de extremidade](service-fabric-get-started-containers.md#configure-communication), [configurar controladores de volume](service-fabric-containers-volume-logging-drivers.md)| 

Ver [elementos de manifestos de aplicativo](#application-manifest-elements), [elementos de manifestos do serviço de FrontEndService](#frontendservice-service-manifest-elements), e [elementos de manifestos do serviço de BackEndService](#backendservice-service-manifest-elements) para obter mais informações sobre específicas Elementos XML.

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

## <a name="frontendservice-service-manifest"></a>Manifesto do serviço de FrontEndService

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

## <a name="backendservice-service-manifest"></a>Manifesto do serviço de BackEndService

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

### <a name="policies-element"></a>Elemento de políticas
Descreve as políticas (ponto de extremidade vinculação de pacote de partilha, Run, acesso e segurança) a serem aplicadas no manifesto do serviço importado. Para obter mais informações, consulte [elemento de políticas](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Define a política de governação de recursos que é aplicada no nível do pacote todo o serviço. Para obter mais informações, consulte [ServicePackageResourceGovernancePolicy elemento](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Elemento de ResourceGovernancePolicy
Especifica os limites de recursos para um pacote do código. Para obter mais informações, consulte [ResourceGovernancePolicy elemento](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>Elemento de ContainerHostPolicies
Especifica políticas para activar anfitriões de contentor. Para obter mais informações, consulte [ContainerHostPolicies elemento](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>Elemento de RepositoryCredentials
Credenciais para o repositório de imagens de contentor para imagens de pull a partir de mensagens em fila. Para obter mais informações, consulte [RepositoryCredentials elemento](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>Elemento de PortBinding
Especifica o recurso de ponto final a ligar para a porta do contentor exposta. Para obter mais informações, consulte [PortBinding elemento](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Elemento de volume
Especifica o volume em estar vinculado ao contentor. Para obter mais informações, consulte [elemento de Volume](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>Elemento de DriverOption
Opções de controlador a serem passados para o driver. Para obter mais informações, consulte [DriverOption elemento](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>Elemento de ImageOverrides
Contentores do Windows Server podem não ser compatíveis entre diferentes versões do sistema operacional.  Pode especificar várias imagens de sistema operacional por contentor e marcá-los com as versões de compilação do SO. Obtenha a versão de compilação do sistema operacional ao executar "winver" num prompt de comando do Windows. Se o sistema operacional subjacente é a compilação 16299 (Windows Server versão 1709) de versão, o Service Fabric escolhe a imagem de contentor etiquetada com sistema operacional = "16299". Uma imagem de contentor não identificada pressupõe-se a trabalhar entre todas as versões do sistema operacional e substitui a imagem especificada no manifesto do serviço. Para obter mais informações, consulte [ImageOverrides elemento](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Elemento de imagem
Imagem de contentor correspondente ao número de versão de compilação de SO para ser iniciado. Se o atributo de SO não for especificado, a imagem de contentor é assumida a trabalhar entre todas as versões do sistema operacional e substitui a imagem especificada no manifesto do serviço. Para obter mais informações, consulte [elemento Image](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>Elemento de EnvironmentOverrides
 Para obter mais informações, consulte [EnvironmentOverrides elemento](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Elemento de EnvironmentVariable
Variável de ambiente. Para obter mais informações, consulte [EnvironmentVariable elemento](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>Elemento de CertificateRef
Especifica informações sobre uma X509 certificado que é exposto para o ambiente de contentores. O certificado tem de estar instalado no arquivo LocalMachine de todos os nós de cluster.
Quando o aplicativo é iniciado, o tempo de execução lê o certificado e gera um ficheiro PFX e a palavra-passe (no Windows) ou um ficheiro PEM (no Linux).
O ficheiro PFX e a palavra-passe são acessíveis no contentor com as variáveis de ambiente Certificates_ServicePackageName_CodePackageName_CertName_PFX e Certificates_ServicePackageName_CodePackageName_CertName_Password. O ficheiro PEM está acessível no contentor com as variáveis de ambiente Certificates_ServicePackageName_CodePackageName_CertName_PEM e Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey. Para obter mais informações, consulte [CertificateRef elemento](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>Elemento de DefaultServices
Declara as instâncias de serviço que são criadas automaticamente sempre que um aplicativo é instanciado em relação a este tipo de aplicação. Para obter mais informações, consulte [DefaultServices elemento](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Elemento de serviço
Declara um serviço a ser criada automaticamente quando o aplicativo é instanciado. Para obter mais informações, consulte [elemento Service](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>Elemento de StatelessService
Define um serviço sem estado. Para obter mais informações, consulte [StatelessService elemento](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Elementos de manifestos do serviço de FrontEndService
### <a name="servicemanifest-element"></a>Elemento de ServiceManifest
Descreve de maneira declarativa o tipo de serviço e a versão. Lista os pacotes de código, configuração e dados de forma independente é atualizável que juntos compõem um pacote de serviço para dar suporte a um ou mais tipos de serviço. Recursos, as definições de diagnóstico e os metadados de serviço, como o tipo de serviço, as propriedades de estado de funcionamento e métricas de balanceamento de carga, também são especificados. Para obter mais informações, consulte [ServiceManifest elemento](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento de ServiceTypes
Define os tipos de serviço são suportados por um CodePackage nesse manifesto. Quando um serviço é instanciado em relação a um dos seguintes tipos de serviço, todos os pacotes do código declarados nesse manifesto são ativados através da execução de seus pontos de entrada. Tipos de serviço são declarados em nível de manifesto e não no nível de pacote de código. Para obter mais informações, consulte [ServiceTypes elemento](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento de StatelessServiceType
Descreve um tipo de serviço sem estado. Para obter mais informações, consulte [StatelessServiceType elemento](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento de CodePackage
Descreve um pacote de código que dá suporte a um tipo de serviço definido. Quando um serviço é instanciado em relação a um dos seguintes tipos de serviço, todos os pacotes do código declarados nesse manifesto são ativados através da execução de seus pontos de entrada. Os processos resultantes devem registrar os tipos de serviço com suporte em tempo de execução. Quando há vários pacotes de código, eles são todas ativados sempre que o sistema procura por qualquer um dos tipos de serviço declarado. Para obter mais informações, consulte [CodePackage elemento](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento de ponto de entrada
O executável especificado pelo ponto de entrada é, normalmente, o anfitrião do serviço de execução longa. A presença de um ponto de entrada de configuração individual evita ter de executar o host de serviço com privilégios elevados por longos períodos de tempo. O executável especificado pelo ponto de entrada é executado após SetupEntryPoint será encerrada com êxito. O processo resultante é monitorizado e reiniciado (início com SetupEntryPoint), se alguma vez terminar ou falhar. Para obter mais informações, consulte [elemento de ponto de entrada](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>Elemento de ContainerHost
 Para obter mais informações, consulte [ContainerHost elemento](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Elemento de ImageName
O repositório e a imagem no https://hub.docker.com ou Azure Container Registry. Para obter mais informações, consulte [ImageName elemento](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Elemento de EnvironmentVariables
Passe variáveis de ambiente para o contentor ou exe.  Para obter mais informações, consulte [EnvironmentVariables elemento](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Elemento de EnvironmentVariable
Variável de ambiente. Para obter mais informações, consulte [EnvironmentVariable elemento](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Elemento de ConfigPackage
Declara uma pasta com o nome do atributo de nome, que contém um arquivo Settings XML. Este ficheiro contém seções de definições de par chave-valor, de definidas pelo utilizador que o processo pode ler voltar no tempo de execução. Durante uma atualização, se apenas a versão de ConfigPackage foi alterada, em seguida, o processo em execução não for reiniciado. Em vez disso, um retorno de chamada notifica o processo que as definições de configuração foram alterados para que possa ser recarregadas dinamicamente. Para obter mais informações, consulte [ConfigPackage elemento](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Elemento de DataPackage
Declara uma pasta, com o nome o atributo de nome, que contém ficheiros de dados estáticos. Service Fabric serão reciclar todos os EXEs e DLLHOSTs especificados nos pacotes de anfitrião e suporte quando nenhum dos pacotes de dados listados no manifesto do serviço sejam atualizados. Para obter mais informações, consulte [DataPackage elemento](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Elemento de recursos
Descreve os recursos utilizados por este serviço, que pode ser declarado sem modificar o código compilado e alterado quando o serviço é implementado. Acesso a estes recursos é controlado as seções de entidades e as políticas de manifesto do aplicativo. Para obter mais informações, consulte [elemento de recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento de pontos finais
Define os pontos finais para o serviço. Para obter mais informações, consulte [pontos finais de elemento](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento de ponto final
Para obter mais informações, consulte [elemento de ponto final](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Elementos de manifestos do serviço de BackEndService
### <a name="servicemanifest-element"></a>Elemento de ServiceManifest
Descreve de maneira declarativa o tipo de serviço e a versão. Lista os pacotes de código, configuração e dados de forma independente é atualizável que juntos compõem um pacote de serviço para dar suporte a um ou mais tipos de serviço. Recursos, as definições de diagnóstico e os metadados de serviço, como o tipo de serviço, as propriedades de estado de funcionamento e métricas de balanceamento de carga, também são especificados. Para obter mais informações, consulte [ServiceManifest elemento](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento de ServiceTypes
Define os tipos de serviço são suportados por um CodePackage nesse manifesto. Quando um serviço é instanciado em relação a um dos seguintes tipos de serviço, todos os pacotes do código declarados nesse manifesto são ativados através da execução de seus pontos de entrada. Tipos de serviço são declarados em nível de manifesto e não no nível de pacote de código. Para obter mais informações, consulte [ServiceTypes elemento](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento de StatelessServiceType
Descreve um tipo de serviço sem estado. Para obter mais informações, consulte [StatelessServiceType elemento](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento de CodePackage
Descreve um pacote de código que dá suporte a um tipo de serviço definido. Quando um serviço é instanciado em relação a um dos seguintes tipos de serviço, todos os pacotes do código declarados nesse manifesto são ativados através da execução de seus pontos de entrada. Os processos resultantes devem registrar os tipos de serviço com suporte em tempo de execução. Quando há vários pacotes de código, eles são todas ativados sempre que o sistema procura por qualquer um dos tipos de serviço declarado. Para obter mais informações, consulte [CodePackage elemento](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento de ponto de entrada
O executável especificado pelo ponto de entrada é, normalmente, o anfitrião do serviço de execução longa. A presença de um ponto de entrada de configuração individual evita ter de executar o host de serviço com privilégios elevados por longos períodos de tempo. O executável especificado pelo ponto de entrada é executado após SetupEntryPoint será encerrada com êxito. O processo resultante é monitorizado e reiniciado (início com SetupEntryPoint), se alguma vez terminar ou falhar. Para obter mais informações, consulte [elemento de ponto de entrada](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>Elemento de ContainerHost
Para obter mais informações, consulte [ContainerHost elemento](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Elemento de ImageName
O repositório e a imagem no https://hub.docker.com ou Azure Container Registry. Para obter mais informações, consulte [ImageName elemento](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Elemento de comandos
Passe uma lista delimitada por vírgulas de comandos para o contentor. Para obter mais informações, consulte [elemento de comandos](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Elemento de EnvironmentVariables
Passe variáveis de ambiente para o contentor ou exe.  Para obter mais informações, consulte [EnvironmentVariables elemento](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Elemento de EnvironmentVariable
Variável de ambiente. Para obter mais informações, consulte [EnvironmentVariable elemento](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Elemento de ConfigPackage
Declara uma pasta com o nome do atributo de nome, que contém um arquivo Settings XML. Este ficheiro contém seções de definições de par chave-valor, de definidas pelo utilizador que o processo pode ler voltar no tempo de execução. Durante uma atualização, se apenas a versão de ConfigPackage foi alterada, em seguida, o processo em execução não for reiniciado. Em vez disso, um retorno de chamada notifica o processo que as definições de configuração foram alterados para que possa ser recarregadas dinamicamente. Para obter mais informações, consulte [ConfigPackage elemento](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Elemento de recursos
Descreve os recursos utilizados por este serviço, que pode ser declarado sem modificar o código compilado e alterado quando o serviço é implementado. Acesso a estes recursos é controlado as seções de entidades e as políticas de manifesto do aplicativo. Para obter mais informações, consulte [elemento de recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento de pontos finais
Define os pontos finais para o serviço. Para obter mais informações, consulte [pontos finais de elemento](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento de ponto final
 Para obter mais informações, consulte [elemento de ponto final](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

