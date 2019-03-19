---
title: Especificar pontos finais de serviço do Service Fabric | Documentos da Microsoft
description: Como descrever os recursos de ponto final no manifesto do serviço, incluindo como configurar pontos finais HTTPS
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: fb16e87a889c409fa6f1f5a9e12b7571ebb14d52
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857577"
---
# <a name="specify-resources-in-a-service-manifest"></a>Especificar recursos num manifesto do serviço
## <a name="overview"></a>Descrição geral
Manifesto do serviço permite que os recursos que são utilizados pelo serviço sejam declaradas/alterar sem alterar o código compilado. O Azure Service Fabric suporta a configuração dos recursos de ponto final para o serviço. O acesso aos recursos especificados no manifesto do serviço pode ser controlado por meio de SecurityGroup no manifesto do aplicativo. A declaração de recursos permite que estes recursos para ser alterada no momento da implementação, o que significa que o serviço não tem de introduzir um novo mecanismo de configuração. A definição de esquema para o ficheiro servicemanifest. xml é instalada com o Service Fabric SDK e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Pontos Finais
Quando um recurso de ponto final é definido no manifesto do serviço, o Service Fabric atribui portas o intervalo de portas reservado do aplicativo quando uma porta não for especificada explicitamente. Por exemplo, observe o ponto final *ServiceEndpoint1* especificado no fragmento de manifesto apresentado após este parágrafo. Além disso, os serviços também podem solicitar uma porta específica num recurso. Réplicas de serviço em execução em nós de cluster diferentes podem ser atribuídas a números de porta diferente, enquanto as réplicas de um serviço em execução no mesmo nó de partilham a porta. As réplicas do serviço, em seguida, podem utilizar estas portas conforme necessário para a replicação e à escuta de pedidos de cliente.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Se existirem vários pacotes de código num pacote de serviço único, em seguida, o pacote do código também tem de ser referenciado no **pontos de extremidade** secção.  Por exemplo, se **ServiceEndpoint2a** e **ServiceEndpoint2b** são pontos finais do mesmo pacote de serviço que referencia os pacotes de código diferentes, o pacote de código correspondente a cada ponto de extremidade é foi esclarecido da seguinte forma:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Consulte a [configurar a Reliable Services com estado](service-fabric-reliable-services-configuration.md) para ler mais sobre como fazer referência a pontos finais de definições de configuração de pacote de ficheiros (Settings).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemplo: especificar um ponto de final HTTP para o seu serviço
O manifesto de serviço seguintes define um recurso de ponto de extremidade TCP e dois recursos de ponto final HTTP no &lt;recursos&gt; elemento.

Pontos de extremidade HTTP são automaticamente que ACL seria ao Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemplo: especificar um ponto final HTTPS para o seu serviço
O protocolo HTTPS fornece autenticação de servidor e também é utilizado para encriptar as comunicações cliente-servidor. Para ativar o HTTPS no seu serviço do Service Fabric, especifique o protocolo no *recursos -> pontos finais -> ponto de extremidade* secção de manifesto do serviço, conforme mostrado anteriormente para o ponto final *ServiceEndpoint3*.

> [!NOTE]
> Protocolo de um serviço não pode ser alterado durante a atualização da aplicação. Se for alterada durante a atualização, é uma alteração de última hora.
> 

> [!WARNING] 
> Ao utilizar o HTTPS, não utilize a mesma porta e certificado para as instâncias de serviço diferentes (independentemente do aplicativo) implementado ao mesmo nó. A atualizar dois serviços diferentes, utilizar a mesma porta em diferentes instâncias da aplicação irá resultar numa falha de atualização. Para obter mais informações, consulte [atualizar vários aplicativos com pontos finais de HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Eis um exemplo ApplicationManifest que precisa definir para HTTPS. O thumbprint para o seu certificado deve ser fornecido. O EndpointRef é uma referência a EndpointResource no ServiceManifest, para que defina o protocolo HTTPS. Pode adicionar mais do que um EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Para clusters do Linux, o **MY** armazenar padrões para a pasta **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Substituição de pontos finais no servicemanifest. XML

O ApplicationManifest adicione uma secção de ResourceOverrides, que será um colateral ConfigOverrides seção. Nesta secção, pode especificar a substituição para a secção de pontos de extremidade na seção de recursos especificada no manifesto do serviço. Pontos finais de substituição é suportado no tempo de execução 5.7.217/SDK 2.7.217 e superior.

Para substituir o ponto final no ServiceManifest usando ApplicationParameters alteração a ApplicationManifest como o seguinte:

Na secção ServiceManifestImport, adicione uma nova secção "ResourceOverrides".

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Nos parâmetros do adicionar abaixo:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Ao implementar a aplicação pode passar esses valores como ApplicationParameters.  Por exemplo:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Nota: Se os valores de fornecem para o ApplicationParameters está vazio, voltamos para o valor predefinido fornecido no ServiceManifest para o EndPointName correspondente.

Por exemplo:

Se o servicemanifest especificado

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

E o valor de IPP/port1 e Protocol1 para parâmetros de aplicação é nulo ou estar vazio. A porta ainda é decidida por ServiceFabric. E a vontade de protocolo tcp.

Suponha que especificar um valor incorreto. Como para a porta que especificou um valor de cadeia de caracteres "Foo" em vez de um int.  Novo ServiceFabricApplication comando irá falhar com um erro: O parâmetro de substituição com o atributo de nome de 'ServiceEndpoint1' IPP/port1 na seção "ResourceOverrides" é inválido. O valor especificado é "Foo" e necessário é 'int'.
