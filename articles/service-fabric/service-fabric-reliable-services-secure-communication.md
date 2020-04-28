---
title: 'Serviço seguro de remo de comunicações com C #'
description: Aprenda a garantir a comunicação baseada em remo do serviço para serviços fiáveis C# que estão a funcionar num cluster Azure Service Fabric.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ee2f1d70f4094ccc7d80edbfaf16509b5124f607
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75609626"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Serviço seguro de remo comunicações em um serviço C#
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java em Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

A segurança é um dos aspetos mais importantes da comunicação. O quadro de aplicação de Serviços Fiáveis fornece algumas pilhas e ferramentas de comunicação pré-construídas que pode usar para melhorar a segurança. Este artigo discute como melhorar a segurança quando está a usar o serviço de remoing num serviço C#. Baseia-se num [exemplo](service-fabric-reliable-services-communication-remoting.md) existente que explica como configurar o remoing para serviços fiáveis escritos em C#. 

Para ajudar a garantir um serviço quando estiver a utilizar o serviço de remoing com os serviços C#, siga estes passos:

1. Crie uma `IHelloWorldStateful`interface, que defina os métodos que estarão disponíveis para uma chamada de procedimento remoto no seu serviço. O seu `FabricTransportServiceRemotingListener`serviço será utilizado, `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` que é declarado no espaço de nome. Esta é `ICommunicationListener` uma implementação que fornece capacidades de remo.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Adicione as definições do ouvinte e as credenciais de segurança.

    Certifique-se de que o certificado que pretende utilizar para ajudar a proteger a comunicação de serviço está instalado em todos os nós do cluster. 
    
    > [!NOTE]
    > Nos nós linux, o certificado deve estar presente como ficheiros formados em PEM no diretório */var/lib/sfcerts.* Para saber mais, consulte [localização e formato de certificados X.509 nos nós linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Existem duas formas de fornecer configurações de ouvintes e credenciais de segurança:

   1. Forneça-os diretamente no código de serviço:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Forneça-as utilizando uma [embalagem de config:](service-fabric-application-and-service-manifests.md)

       Adicione uma `TransportSettings` secção nomeada no ficheiro definições.xml.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       Neste caso, `CreateServiceReplicaListeners` o método será assim:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Se adicionar `TransportSettings` uma secção no ficheiro definições.xml, `FabricTransportRemotingListenerSettings` carregará todas as definições desta secção por padrão.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Neste caso, `CreateServiceReplicaListeners` o método será assim:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Quando ligar para métodos num serviço seguro utilizando a pilha `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` de remo, em `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`vez de utilizar a classe para criar um representante de serviço, utilize . `FabricTransportRemotingSettings`Passe, que `SecurityCredentials`contém.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o código do cliente estiver a funcionar `FabricTransportRemotingSettings` como parte de um serviço, pode carregar a partir do ficheiro definições.xml. Crie uma secção HelloWorldClientTransportSettings semelhante ao código de serviço, como mostrado anteriormente. Faça as seguintes alterações ao código do cliente:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o cliente não estiver a funcionar como parte de um serviço, pode criar um ficheiro client_name.definições.xml no mesmo local onde está o client_name.exe. Em seguida, crie uma secção De TransporteS Nesse ficheiro.

    Semelhante ao serviço, se `TransportSettings` adicionar uma secção nas definições do cliente.xml/client_name.xml, `FabricTransportRemotingSettings` carrega todas as definições desta secção por padrão.

    Nesse caso, o código anterior é ainda mais simplificado:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Como próximo passo, leia [a Web API com o OWIN em Serviços Fiáveis](service-fabric-reliable-services-communication-webapi.md).
