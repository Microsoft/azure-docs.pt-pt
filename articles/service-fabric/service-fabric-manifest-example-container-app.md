---
title: Aplicação de contentores de tecido de serviço Azure manifestam exemplos
description: Saiba como configurar as configurações de aplicação e manifesto de serviço para uma aplicação de tecido de serviço multi-contentores.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84701171"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Exemplos de manifesto e aplicação e serviço de múltiplos contentores
Seguem-se exemplos da aplicação e dos manifestos de serviço para uma aplicação de tecido de serviço multi-contentores. O objetivo destes exemplos é mostrar quais as configurações disponíveis e como usá-las. Estes manifestos de aplicação e serviço baseiam-se nos manifestos da [Amostra de Contentores do Windows Server 2016.](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows)

São mostradas as seguintes características:

|Manifesto|Funcionalidades|
|---|---|
|[Manifesto de aplicação](#application-manifest)| [sobreposição de variáveis ambientais](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [configurar o mapeamento porta-a-hospedeiro](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)do contentor, [configurar a autenticação do registo de contentores,](service-fabric-get-started-containers.md#configure-container-repository-authentication) [a governação dos recursos,](service-fabric-resource-governance.md)definir o modo de [isolamento,](service-fabric-get-started-containers.md#configure-isolation-mode) [especificar as imagens específicas do recipiente de construção de OS](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Manifesto de serviço FrontEndService](#frontendservice-service-manifest)| [definir variáveis ambientais,](service-fabric-get-started-containers.md#configure-and-set-environment-variables) [configurar um ponto final,](service-fabric-get-started-containers.md#configure-communication)passar comandos ao contentor, [importar um certificado num recipiente](service-fabric-securing-containers.md)| 
|[Manifesto de serviço backEndService](#backendservice-service-manifest)|[definir variáveis ambientais,](service-fabric-get-started-containers.md#configure-and-set-environment-variables) [configurar um ponto final,](service-fabric-get-started-containers.md#configure-communication) [configurar o controlador de volume](service-fabric-containers-volume-logging-drivers.md)| 

Consulte [elementos manifestos de aplicação,](#application-manifest-elements) [elementos manifestos de serviço FrontEndService](#frontendservice-service-manifest-elements)e [elementos manifestos de serviço BackEndService](#backendservice-service-manifest-elements) para obter mais informações sobre elementos XML específicos.

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

## <a name="backendservice-service-manifest"></a>Manifesto de serviço backEndService

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

### <a name="policies-element"></a>Elemento de Políticas
Descreve políticas (ligação de ponto final, partilha de pacotes, run-as e acesso à segurança) a aplicar no manifesto de serviço importado. Para mais informações, consulte [Elemento de Políticas](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>Elemento de política de administração da ServicePackageResource
Define a política de governação de recursos que é aplicada ao nível de todo o pacote de serviços. Para mais informações, consulte [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>Elemento de política de governo de recursos
Especifica limites de recursos para um pacote de código. Para mais informações, consulte Elemento de Política [de Administração de Recursos](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>Elemento DePolícias ContainerHost
Especifica políticas de ativação de hospedeiros de contentores. Para mais informações, consulte [o Elemento ContainerHostPolicies](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>Elemento deCredentais do Repositório
Credenciais para repositório de imagem de contentor para extrair imagens de. Para mais informações, consulte [o Elemento Repositórios](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>Elemento De Porção
Especifica qual recurso de ponto final para ligar à porta do recipiente exposta. Para mais informações, consulte [Elemento PortBinding](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Elemento de volume
Especifica o volume a ser ligado ao recipiente. Para mais informações, consulte [o Elemento volume](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>Elemento de Opção do Condutor
Opções de motorista a passar para o motorista. Para mais informações, consulte [DriverOption Element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>Elemento ImageOverrides
Os recipientes do Windows Server podem não ser compatíveis em diferentes versões do SISTEMA.  Pode especificar várias imagens de SO por contentor e marcá-las com as versões de construção do SISTEMA. Obtenha a versão de construção do SO executando "winver" num pedido de comando do Windows. Se o SISTEMA subjacente for a versão 16299 (versão 1709 do Windows Server), o Service Fabric escolhe a imagem do contentor marcada com Os="16299". Presume-se que uma imagem de contentor não composição funciona em todas as versões do SO e sobrepõe-se à imagem especificada no manifesto de serviço. Para mais informações, consulte [Elemento ImageOverrides](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Elemento de imagem
Imagem de recipiente correspondente ao número da versão de construção de SO a ser lançada. Se o atributo Os não for especificado, presume-se que a imagem do recipiente funcione em todas as versões do SO e substitua a imagem especificada no manifesto de serviço. Para mais informações, consulte [Elemento de Imagem](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>Elemento EnvironmentOverrides
 Para mais informações, consulte [Elemento EnvironmentOverrides](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Elemento Variavel do Ambiente
Variável ambiental. Para mais informações, consulte [Elemento Variavel ambiente](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>Elemento DesferimentoRef
Especifica informações sobre um certificado X509 que deve ser exposto ao ambiente do recipiente. O certificado deve ser instalado na loja LocalMachine de todos os nós do cluster.
Quando a aplicação começa, o tempo de execução lê o certificado e gera um ficheiro e senha PFX (no Windows) ou um ficheiro PEM (no Linux).
O ficheiro PFX e a palavra-passe estão acessíveis no recipiente utilizando as variáveis de ambiente Certificates_ServicePackageName_CodePackageName_CertName_PFX e Certificates_ServicePackageName_CodePackageName_CertName_Password. O ficheiro PEM é acessível no recipiente utilizando as variáveis ambiente Certificates_ServicePackageName_CodePackageName_CertName_PEM e Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey. Para mais informações, consulte [CertificateRef Element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>Elemento de Serviços Padrão
Declara casos de serviço que são automaticamente criados sempre que uma aplicação é instantânea contra este tipo de aplicação. Para mais informações, consulte [o Elemento DefaultServices](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Elemento de serviço
Declara um serviço a ser criado automaticamente quando a aplicação é instantânea. Para mais informações, consulte [Elemento de Serviço](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>Elemento de Serviço apátrida
Define um serviço apátrida. Para mais informações, consulte [elemento statelessService](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Elementos manifestos de serviço FrontEndService
### <a name="servicemanifest-element"></a>Elemento De ServiçoManifest
Declarativamente descreve o tipo de serviço e a versão. Ele lista os pacotes de código, configuração e dados de forma independente que, em conjunto, compõem um pacote de serviços para suportar um ou mais tipos de serviço. São também especificados recursos, definições de diagnóstico e metadados de serviço, tais como tipo de serviço, propriedades sanitárias e métricas de equilíbrio de carga. Para mais informações, consulte [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento deTipos de Serviço
Define quais os tipos de serviço suportados por um CodePackage neste manifesto. Quando um serviço é instantâneo contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Os tipos de serviço são declarados ao nível manifesto e não ao nível do pacote de código. Para mais informações, consulte [o ElementoTips de Serviço](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento de Tipo de Serviço Apátrida
Descreve um tipo de serviço apátrida. Para mais informações, consulte [elemento Apátrida](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento codepackage
Descreve um pacote de código que suporta um tipo de serviço definido. Quando um serviço é instantâneo contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Espera-se que os processos resultantes registem os tipos de serviço suportados no tempo de execução. Quando existem vários pacotes de código, todos são ativados sempre que o sistema procura qualquer um dos tipos de serviço declarados. Para mais informações, consulte [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento ponto de entrada
O executável especificado pelo EntryPoint é tipicamente o anfitrião de serviço de longa duração. A presença de um ponto de entrada de configuração separado evita ter de executar o hospedeiro de serviço com privilégios elevados por longos períodos de tempo. O executável especificado pelo EntryPoint é executado após a saída de SetupEntryPoint com sucesso. O processo resultante é monitorizado e reiniciado (começando novamente com o SetupEntryPoint) se alguma vez terminar ou falhar. Para mais informações, consulte [o Elemento Ponto de Entrada](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>Elemento De ContentorHost
 Para mais informações, consulte [o Elemento ContentorHost](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Elemento de nome de imagem
O repo e a imagem no [https://hub.docker.com](https://hub.docker.com) registo do contentor Azure. Para mais informações, consulte [o Elemento Nome de Imagem](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Elemento DeVariáveis ambientais
Passe variáveis ambientais para o seu recipiente ou exe.  Para mais informações, consulte [Elemento Variabilidades ambientais](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Elemento Variavel do Ambiente
Variável ambiental. Para mais informações, consulte [Elemento Variavel ambiente](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta, nomeada pelo atributo Nome, que contém um ficheiro Settings.xml. Este ficheiro contém secções de definições de pares de valor chave definidas pelo utilizador que o processo pode ler no tempo de execução. Durante uma atualização, se apenas a versão ConfigPackage tiver mudado, então o processo de execução não é reiniciado. Em vez disso, uma chamada notifica o processo que as definições de configuração mudaram para que possam ser recarregadas dinamicamente. Para mais informações, consulte [o Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Elemento de Embalagem de Dados
Declara uma pasta, nomeada pelo atributo Nome, que contém ficheiros de dados estáticos. O Service Fabric reciclará todos os EXEs e DLLHOSTs especificados nos pacotes de anfitrião e suporte quando qualquer um dos pacotes de dados listados no manifesto de serviço for atualizado. Para mais informações, consulte [o Elemento DataPackage](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Elemento de Recursos
Descreve os recursos utilizados por este serviço, que podem ser declarados sem modificar código compilado e alterados quando o serviço é implantado. O acesso a estes recursos é controlado através das secções principais e políticas do manifesto de candidatura. Para mais informações, consulte [Elemento recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento pontos finais
Define pontos finais para o serviço. Para mais informações, consulte [o Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento de ponto final
Para mais informações, consulte [o Elemento Ponto final](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Elementos manifestos de serviço backEndService
### <a name="servicemanifest-element"></a>Elemento De ServiçoManifest
Declarativamente descreve o tipo de serviço e a versão. Ele lista os pacotes de código, configuração e dados de forma independente que, em conjunto, compõem um pacote de serviços para suportar um ou mais tipos de serviço. São também especificados recursos, definições de diagnóstico e metadados de serviço, tais como tipo de serviço, propriedades sanitárias e métricas de equilíbrio de carga. Para mais informações, consulte [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elemento deTipos de Serviço
Define quais os tipos de serviço suportados por um CodePackage neste manifesto. Quando um serviço é instantâneo contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Os tipos de serviço são declarados ao nível manifesto e não ao nível do pacote de código. Para mais informações, consulte [o ElementoTips de Serviço](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Elemento de Tipo de Serviço Apátrida
Descreve um tipo de serviço apátrida. Para mais informações, consulte [elemento Apátrida](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>Elemento codepackage
Descreve um pacote de código que suporta um tipo de serviço definido. Quando um serviço é instantâneo contra um destes tipos de serviço, todos os pacotes de código declarados neste manifesto são ativados executando os seus pontos de entrada. Espera-se que os processos resultantes registem os tipos de serviço suportados no tempo de execução. Quando existem vários pacotes de código, todos são ativados sempre que o sistema procura qualquer um dos tipos de serviço declarados. Para mais informações, consulte [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>Elemento ponto de entrada
O executável especificado pelo EntryPoint é tipicamente o anfitrião de serviço de longa duração. A presença de um ponto de entrada de configuração separado evita ter de executar o hospedeiro de serviço com privilégios elevados por longos períodos de tempo. O executável especificado pelo EntryPoint é executado após a saída de SetupEntryPoint com sucesso. O processo resultante é monitorizado e reiniciado (começando novamente com o SetupEntryPoint) se alguma vez terminar ou falhar. Para mais informações, consulte [o Elemento Ponto de Entrada](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>Elemento De ContentorHost
Para mais informações, consulte [o Elemento ContentorHost](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>Elemento de nome de imagem
O repo e a imagem no [https://hub.docker.com](https://hub.docker.com) registo do contentor Azure. Para mais informações, consulte [o Elemento Nome de Imagem](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Elemento de Comandos
Passe uma vírgula delimitada lista de comandos para o contentor. Para mais informações, consulte [o Elemento comandos](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>Elemento DeVariáveis ambientais
Passe variáveis ambientais para o seu recipiente ou exe.  Para mais informações, consulte [Elemento Variabilidades ambientais](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Elemento Variavel do Ambiente
Variável ambiental. Para mais informações, consulte [Elemento Variavel ambiente](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>Elemento ConfigPackage
Declara uma pasta, nomeada pelo atributo Nome, que contém um ficheiro Settings.xml. Este ficheiro contém secções de definições de pares de valor chave definidas pelo utilizador que o processo pode ler no tempo de execução. Durante uma atualização, se apenas a versão ConfigPackage tiver mudado, então o processo de execução não é reiniciado. Em vez disso, uma chamada notifica o processo que as definições de configuração mudaram para que possam ser recarregadas dinamicamente. Para mais informações, consulte [o Elemento ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Elemento de Recursos
Descreve os recursos utilizados por este serviço, que podem ser declarados sem modificar código compilado e alterados quando o serviço é implantado. O acesso a estes recursos é controlado através das secções principais e políticas do manifesto de candidatura. Para mais informações, consulte [Elemento recursos](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Elemento pontos finais
Define pontos finais para o serviço. Para mais informações, consulte [o Elemento Endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Elemento de ponto final
 Para mais informações, consulte [o Elemento Ponto final](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

