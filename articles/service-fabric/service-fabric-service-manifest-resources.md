---
title: Especificando Service Fabric pontos de extremidade de serviço | Microsoft Docs
description: Como descrever os recursos do ponto de extremidade em um manifesto do serviço, incluindo como configurar pontos de extremidades HTTPS
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: bb3fd77df60be68408fceea683ee4b8b74d77427
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242903"
---
# <a name="specify-resources-in-a-service-manifest"></a>Especificar recursos num manifesto do serviço
## <a name="overview"></a>Visão geral
O manifesto do serviço permite que os recursos usados pelo serviço sejam declarados/alterados sem alterar o código compilado. O Azure Service Fabric dá suporte à configuração de recursos de ponto de extremidade para o serviço. O acesso aos recursos especificados no manifesto do serviço pode ser controlado por meio do The de segurança no manifesto do aplicativo. A declaração de recursos permite que esses recursos sejam alterados no momento da implantação, o que significa que o serviço não precisa introduzir um novo mecanismo de configuração. A definição de esquema para o arquivo de manifesto. xml é instalada com o SDK do Service Fabric e ferramentas para *c:\Arquivos de Programas\microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Pontos Finais
Quando um recurso de ponto de extremidade é definido no manifesto do serviço, Service Fabric atribui portas do intervalo de portas do aplicativo reservado quando uma porta não é especificada explicitamente. Por exemplo, examine o ponto de extremidade *ServiceEndpoint1* especificado no trecho de manifesto fornecido após este parágrafo. Além disso, os serviços também podem solicitar uma porta específica em um recurso. As réplicas de serviço em execução em nós de cluster diferentes podem receber números de porta diferentes, enquanto as réplicas de um serviço em execução no mesmo nó compartilham a porta. As réplicas de serviço podem usar essas portas conforme necessário para replicação e escuta de solicitações de cliente.

> [!WARNING] 
> As portas estáticas de design não devem se sobrepor ao intervalo de portas de aplicativo especificado em ClusterManifest. Se você especificar uma porta estática, atribua-a fora do intervalo de portas do aplicativo, caso contrário, isso resultará em conflitos de porta. Com a versão 6.5 CU2, emitiremos um **aviso de integridade** quando detectarmos esse conflito, mas permitirá que a implantação continue em sincronia com o comportamento 6,5 enviado. No entanto, poderemos impedir a implantação do aplicativo nas próximas versões principais.
>
> Com a versão 7,0, emiteremos um **aviso de integridade** quando detectamos que o uso do intervalo de portas do aplicativo vai além de HostingConfig:: ApplicationPortExhaustThresholdPercentage (padrão 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Se houver vários pacotes de código em um único pacote de serviço, o pacote de códigos também precisará ser referenciado na seção **pontos de extremidade** .  Por exemplo, se **ServiceEndpoint2a** e **ServiceEndpoint2b** forem pontos de extremidade do mesmo pacote de serviço referenciando diferentes pacotes de código, o pacote de código correspondente a cada ponto de extremidade será esclarecido da seguinte maneira:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Consulte [Configurando Reliable Services com estado](service-fabric-reliable-services-configuration.md) para ler mais sobre como referenciar pontos de extremidade do arquivo de configurações do pacote de configuração (Settings. xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemplo: especificando um ponto de extremidade HTTP para seu serviço
O manifesto de serviço a seguir define um recurso de ponto de extremidade TCP e dois recursos de ponto de extremidade HTTP no elemento &lt;Resources&gt;.

Os pontos de extremidade HTTP são automaticamente ACL por Service Fabric.

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

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemplo: especificando um ponto de extremidade HTTPS para seu serviço
O protocolo HTTPS fornece autenticação de servidor e também é usado para criptografar a comunicação cliente-servidor. Para habilitar o HTTPS em seu serviço de Service Fabric, especifique o protocolo na seção *recursos-> pontos de extremidade do > ponto de extremidades* do manifesto do serviço, conforme mostrado anteriormente para o ponto de extremidade *ServiceEndpoint3*.

> [!NOTE]
> O protocolo de um serviço não pode ser alterado durante a atualização do aplicativo. Se ele for alterado durante a atualização, será uma alteração significativa.
> 

> [!WARNING] 
> Ao usar HTTPS, não use a mesma porta e o mesmo certificado para diferentes instâncias de serviço (independente do aplicativo) implantadas no mesmo nó. A atualização de dois serviços diferentes usando a mesma porta em diferentes instâncias de aplicativo resultará em uma falha de atualização. Para obter mais informações, consulte [Atualizando vários aplicativos com pontos de extremidade https ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Aqui está um exemplo de ApplicationManifest que você precisa definir para HTTPS. A impressão digital do certificado deve ser fornecida. O EndpointRef é uma referência a EndpointResource no inmanifest, para o qual você define o protocolo HTTPS. Você pode adicionar mais de um EndpointCertificate.  

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

Para clusters do Linux, o **meu** repositório usa como padrão a pasta **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Substituindo pontos de extremidade no manifest. xml

No ApplicationManifest, adicione uma seção ResourceOverrides, que será um irmão à seção ConfigOverrides. Nesta seção, você pode especificar a substituição para a seção pontos de extremidade na seção recursos especificada no manifesto do serviço. Há suporte para a substituição de pontos de extremidade em tempo de execução 5.7.217/SDK 2.7.217 e superior.

Para substituir o ponto de extremidade no inmanifest usando Applicationparameters, altere o ApplicationManifest da seguinte maneira:

Na seção ServiceManifestImport, adicione uma nova seção "ResourceOverrides".

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

Nos parâmetros, adicione abaixo:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Ao implantar o aplicativo, você pode passar esses valores como Applicationparameters.  Por exemplo:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Observação: se os valores fornecidos para o Applicationparameters estiverem vazios, voltaremos ao valor padrão fornecido no manifesto para o EndpointName correspondente.

Por exemplo:

Se estiver no próprio manifesto especificado

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

E o valor de Port1 e Protocol1 para parâmetros de aplicativo é nulo ou vazio. A porta ainda é decidida pelo ServicePortal. E o protocolo será TCP.

Suponha que você especifique um valor errado. Assim como para a porta, você especificou um valor de cadeia de caracteres "foo" em vez de um int.  O comando New-ServiceFabricApplication falhará com um erro: o parâmetro de substituição com o nome ' ServiceEndpoint1 ', atributo ' Port1 ' na seção ' ResourceOverrides ' é inválido. O valor especificado é ' foo ' e obrigatório é ' int '.
