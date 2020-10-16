---
title: Especificar pontos finais de serviço de tecido de serviço
description: Como descrever os recursos de ponto final num manifesto de serviço, incluindo como configurar pontos finais HTTPS
ms.topic: conceptual
ms.date: 09/16/2020
ms.custom: contperfq1
ms.openlocfilehash: 775e554128b9828915ce7dafaf4bccf597911912
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017595"
---
# <a name="specify-resources-in-a-service-manifest"></a>Especificar recursos num manifesto de serviço
## <a name="overview"></a>Descrição geral
As aplicações e serviços do Tecido de Serviço são definidos e versados utilizando ficheiros manifestos. Para uma visão mais alta dos ServiceManifest.xml e ApplicationManifest.xml, consulte [a aplicação e manifestos](service-fabric-application-and-service-manifests.md)de serviço do Service Fabric .

O manifesto de serviço permite que os recursos utilizados pelo serviço sejam declarados, ou alterados, sem alterar o código compilado. O Service Fabric suporta a configuração dos recursos do ponto final para o serviço. O acesso aos recursos especificados no manifesto de serviço pode ser controlado através do SecurityGroup no manifesto de aplicação. A declaração de recursos permite que estes recursos sejam alterados no tempo de implementação, o que significa que o serviço não precisa de introduzir um novo mecanismo de configuração. A definição de esquema para o ficheiro ServiceManifest.xml é instalada com o Service Fabric SDK e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*, e está documentado na [documentação do esquema do ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

## <a name="endpoints"></a>Pontos Finais
Quando um recurso de ponto final é definido no manifesto de serviço, o Service Fabric atribui portas da gama portuária de aplicações reservadas quando uma porta não é especificada explicitamente. Por exemplo, veja o ponto final *do ServiceEndpoint1* especificado no manifesto snippet fornecido após este parágrafo. Além disso, os serviços também podem solicitar uma porta específica num recurso. As réplicas de serviço em execução em diferentes nós de cluster podem ser atribuídas diferentes números de porta, enquanto réplicas de um serviço que funciona no mesmo nó partilham a porta. As réplicas de serviço podem então usar estas portas conforme necessário para replicação e audição de pedidos do cliente.

Ao ativar um serviço que especifica um ponto final https, o Service Fabric definirá a entrada de controlo de acesso para a porta, ligará o certificado de servidor especificado à porta, e também concederá a identidade de que o serviço está a funcionar como permissões à chave privada do certificado. O fluxo de ativação é invocado sempre que o Tecido de Serviço começa, ou quando a declaração de certificado do pedido é alterada através de uma atualização. O certificado de ponto final também será monitorizado para alterações/renovações, e as permissões serão recandidaturas periodicamente, se necessário.

Após o encerramento do serviço, a Service Fabric limpará a entrada do controlo de acesso ao ponto final e removerá a encadernação do certificado. No entanto, quaisquer permissões aplicadas à chave privada do certificado não serão limpas.

> [!WARNING] 
> Por conceção, as portas estáticas não devem sobrepor-se à gama de portas de aplicação especificada no ClusterManifest. Se especificar uma porta estática, atribua-a fora da gama de portas de aplicação, caso contrário resultará em conflitos portuários. Com o lançamento 6.5CU2 emitiremos um **Aviso sanitário** quando detetarmos tal conflito, mas deixaremos que a implantação continue em sintonia com o comportamento enviado 6.5. No entanto, podemos impedir a implementação da aplicação das próximas grandes versões.
>
> Com o lançamento 7.0 emitiremos um **Aviso de Saúde** quando detetarmos que o uso da gama portuária de aplicações vai além do HostingConfig::ApplicationPortExhaustThresholdPercentage (padrão 80%).
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

Se houver vários pacotes de código num único pacote de serviço, o pacote de código também deve ser referenciado na secção **Endpoints.**  Por exemplo, se **o ServiceEndpoint2a** e **o ServiceEndpoint2b** forem pontos finais do mesmo pacote de serviço que refere diferentes pacotes de código, o pacote de código correspondente a cada ponto final é clarificado da seguinte forma:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Consulte a [Configuração de Serviços Fidedignódeos para](service-fabric-reliable-services-configuration.md) ler mais sobre referências a pontos finais do ficheiro de definições de pacotes config (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemplo: especificar um ponto final HTTP para o seu serviço
O manifesto de serviço que se segue define um recurso de ponto final TCP e dois recursos de ponto final HTTP no &lt; &gt; elemento Recursos.

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
O protocolo HTTPS fornece autenticação do servidor e também é utilizado para encriptar a comunicação do servidor cliente. Para ativar HTTPS no seu serviço Service Fabric, especifique o protocolo na secção *De ponto final de > Recursos ->* do manifesto de serviço, como mostrado anteriormente para o ponto de serviço endpoint3 . *ServiceEndpoint3*

> [!NOTE]
> O protocolo de um serviço não pode ser alterado durante a atualização da aplicação. Se for alterada durante a atualização, é uma mudança de rutura.
> 

> [!WARNING] 
> Ao utilizar HTTPS, não utilize a mesma porta e certificado para diferentes instâncias de serviço (independentes da aplicação) implantadas no mesmo nó. A atualização de dois serviços diferentes utilizando a mesma porta em diferentes instâncias de aplicação resultará numa falha de atualização. Para obter mais informações, consulte [atualizar várias aplicações com pontos finais HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Aqui está um exemplo ApplicationManifest demonstrando a configuração necessária para um ponto final HTTPS. O certificado servidor/ponto final pode ser declarado por impressão digital ou nome comum sujeito, e deve ser fornecido um valor. O EndpointRef é uma referência ao EndpointResource in ServiceManifest, e cujo protocolo deve ter sido definido para o protocolo 'https'. Pode adicionar mais do que um Certificado de Ponto final.  

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

Para os agrupamentos Linux, a loja **MY** por defeito na pasta **/var/lib/sfcerts**.

Para um exemplo de uma aplicação completa que utiliza um ponto final HTTPS, consulte [adicionar um ponto final HTTPS a um serviço frontal da API core ASP.NET utilizando o Kestrel](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-dotnet-app-enable-https-endpoint#define-an-https-endpoint-in-the-service-manifest).

## <a name="port-acling-for-http-endpoints"></a>ACLing de porta para pontos finais HTTP
O Tecido de Serviço automaticamente ACL HTTP(S) pontos finais especificados por padrão. **Não** realizará ACLing automático se um ponto final não tiver uma [SegurançaAccessPolicy](service-fabric-assign-policy-to-endpoint.md) associada a ele e o Service Fabric estiver configurado para executar usando uma conta com privilégios de Administrador.

## <a name="overriding-endpoints-in-servicemanifestxml"></a>Pontos finais dominantes em ServiceManifest.xml

Na ApplicationManifest, adicione uma secção ResourceOverrides, que será um irmão para a secção ConfigOverrides. Nesta secção, pode especificar a substituição da secção Endpoints na secção de recursos especificada no manifesto de Serviço. Os pontos finais dominantes são suportados em tempo de execução 5.7.217/SDK 2.7.217 e superior.

Para substituir o EndPoint no ServiceManifest utilizando os ApplicationParameters, altere a ApplicationManifest como tal:

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

Nos Parâmetros a seguir:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Ao implementar a aplicação, pode passar nestes valores como ApplicationParameters.  Por exemplo:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Nota: Se o valor previsto para um determinado ApplicationParameter estiver vazio, voltamos ao valor predefinido fornecido no ServiceManifest para o nome EndPointName correspondente.

Por exemplo:

Se no ServiceManifest especificado

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Assuma que o valor port1 e protocol1 para parâmetros de aplicação é nulo ou vazio. O porto será decidido pelo ServiceFabric e o Protocolo será tcp.

Suponha que especifique um valor errado. Diga para o Porto que especificou um valor de corda "Foo" em vez de um int.  New-ServiceFabricApplication comando falhará com um erro: `The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int'.`

## <a name="next-steps"></a>Passos Seguintes

Este artigo explicou como definir pontos finais no manifesto de serviço da Service Fabric. Para exemplos mais detalhados, consulte:

> [!div class="nextstepaction"]
> [Exemplos de manifesto de aplicação e do serviço](service-fabric-manifest-examples.md)

Para uma passagem de embalagens e implantação de uma aplicação existente num cluster de Tecido de Serviço, consulte:

> [!div class="nextstepaction"]
> [Pacote e implemente um executável existente para o tecido de serviço](service-fabric-deploy-existing-app.md)