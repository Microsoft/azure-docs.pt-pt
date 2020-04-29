---
title: Especificar pontos finais de serviço de tecido de serviço
description: Como descrever os recursos de ponto final num manifesto de serviço, incluindo como configurar pontos finais HTTPS
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 88e71d15829e68bde635f5b4d40224b8fa914f40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417582"
---
# <a name="specify-resources-in-a-service-manifest"></a>Especificar recursos num manifesto de serviço
## <a name="overview"></a>Descrição geral
O manifesto de serviço permite que os recursos utilizados pelo serviço sejam declarados, ou alterados, sem alterar o código compilado. Serviço Tecido suporta configuração de recursos de ponto final para o serviço. O acesso aos recursos especificados no manifesto de serviço pode ser controlado através do SecurityGroup no manifesto de aplicação. A declaração de recursos permite que estes recursos sejam alterados no momento da implantação, o que significa que o serviço não precisa de introduzir um novo mecanismo de configuração. A definição de esquema para o ficheiro ServiceManifest.xml está instalada com o SDK de Tecido de Serviço e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Pontos Finais
Quando um recurso de ponto final é definido no manifesto de serviço, o Tecido de Serviço atribui portas da gama de porta de aplicação reservada quando uma porta não é especificada explicitamente. Por exemplo, veja o ponto final *do serviçoEndpoint1* especificado no manifesto corte fornecido após este parágrafo. Além disso, os serviços também podem solicitar um porto específico num recurso. As réplicas de serviço que correm em diferentes nós de cluster podem ser atribuídas diferentes números de porta, enquanto réplicas de um serviço em funcionamento no mesmo nó partilham a porta. As réplicas de serviço podem então utilizar estas portas conforme necessário para a replicação e audição para pedidos de clientes.

Ao ativar um serviço que especifica um ponto final https, o Service Fabric definirá a entrada de controlo de acesso para a porta, ligará o certificado de servidor especificado à porta, e também concederá a identidade de que o serviço está a funcionar como permissões à chave privada do certificado. O fluxo de ativação é invocado sempre que o Tecido de Serviço começa, ou quando a declaração de certificado da aplicação é alterada através de uma atualização. O certificado de ponto final também será monitorizado para alterações/renovações, e as permissões serão periodicamente reaplicadas, se necessário.

Após a cessação do serviço, o Tecido de Serviço limpará a entrada de controlo de acesso de ponto final e removerá o encadernação do certificado. No entanto, quaisquer permissões aplicadas à chave privada do certificado não serão limpas.

> [!WARNING] 
> Por desenho, as portas estáticas não devem sobrepor-se à gama de portas de aplicação especificada no ClusterManifest. Se especificar uma porta estática, atribuí-la fora do alcance da porta de aplicação, caso contrário, resultará em conflitos portuários. Com a libertação 6.5CU2 emitiremos um Aviso de **Saúde** quando detetarmos tal conflito, mas deixaremos que a implantação continue em sintonia com o comportamento 6.5 enviado. No entanto, podemos impedir a implementação da aplicação a partir das próximas grandes lançamentos.
>
> Com o lançamento 7.0 emitiremos um Aviso de **Saúde** quando detetarmos que o uso da gama de portas de aplicação vai além do HostingConfig::ApplicationPortExhaustThresholdPercentage (padrão 80%).
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

Se houver vários pacotes de código num único pacote de serviço, então o pacote de código também precisa de ser referenciado na secção **Endpoints.**  Por exemplo, se o **ServiceEndpoint2a** e o **ServiceEndpoint2b** forem pontos finais do mesmo pacote de serviço sintetizando diferentes pacotes de código, o pacote de código correspondente a cada ponto final é clarificado da seguinte forma:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Consulte a [Configuração de Serviços Fiáveis e abastados](service-fabric-reliable-services-configuration.md) para ler mais sobre pontos finais de referência do ficheiro de definições do pacote de config (definições.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemplo: especificar um ponto final http para o seu serviço
O seguinte manifesto de serviço define um recurso de ponto &lt;final&gt; do TCP e dois recursos de ponto final HTTP no elemento Recursos.

Os pontos finais http são automaticamente ACL'd by Service Fabric.

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
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemplo: especificar um ponto final HTTPS para o seu serviço
O protocolo HTTPS fornece a autenticação do servidor e também é utilizado para encriptar a comunicação cliente-servidor. Para ativar https no seu serviço De Serviço Fabric, especifique o protocolo nos *Recursos -> Pontos Finais ->* secção Endpoint do manifesto de serviço, como mostrado anteriormente para o ponto final de *serviçoEndpoint3*.

> [!NOTE]
> O protocolo de um serviço não pode ser alterado durante a atualização da aplicação. Se for alterada durante a atualização, é uma mudança de rutura.
> 

> [!WARNING] 
> Quando utilizar https, não utilize a mesma porta e certificado para diferentes instâncias de serviço (independentes da aplicação) implantadas no mesmo nó. A atualização de dois serviços diferentes utilizando a mesma porta em diferentes instâncias de aplicação resultará numa falha de atualização. Para mais informações, consulte [Atualizar várias aplicações com pontos finais HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Aqui está um exemplo ApplicationManifest demonstrando a configuração necessária para um ponto final HTTPS. O certificado servidor/ponto final pode ser declarado por impressão digital ou nome comum do sujeito, devendo ser fornecido um valor. O EndpointRef é uma referência ao EndpointResource no ServiceManifest, e cujo protocolo deve ter sido definido para o protocolo 'https'. Pode adicionar mais do que um EndpointCertificate.  

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
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
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
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Para os clusters Linux, a loja **MY** predefinido para a pasta **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Pontos finais overover em ServiceManifest.xml

Na AplicaçãoManifesto adicione uma secção ResourceOverrides, que será um irmão da secção ConfigOverrides. Nesta secção, pode especificar a sobreposição para a secção Pontos Finais na secção de recursos especificada no manifesto de Serviço. Os pontos finais dominantes são suportados no tempo de execução 5.7.217/SDK 2.7.217 e superior.

Para anular o EndPoint no ServiceManifest utilizando applicationParameters, altere o ApplicationManifest como seguinte:

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
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Nos parâmetros adicionam abaixo:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Ao implementar a aplicação pode passar nestes valores como ApplicationParameters.  Por exemplo:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Nota: Se os valores fornecidos para os ApplicationParâmetros estiverem vazios, voltamos ao valor predefinido fornecido no ServiceManifest para o nome final correspondente.

Por exemplo:

Se no ServiceManifest especificou

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

E o valor Port1 e Protocol1 para os parâmetros de aplicação é nulo ou vazio. A porta ainda é decidida pela ServiceFabric. E o Protocolo vai tcp.

Suponha que especifique um valor errado. Como no Porto especificou um valor de cadeia "Foo" em vez de um int.  O comando New-ServiceFabricApplication falhará com um erro : O parâmetro de sobreposição com o nome 'ServiceEndpoint1' atribui 'Port1' na secção 'ResourceOverrides' é inválido. O valor especificado é 'Foo' e é exigido 'int'.
