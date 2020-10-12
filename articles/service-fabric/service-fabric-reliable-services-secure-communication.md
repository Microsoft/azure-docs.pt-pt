---
title: 'Comunicações de remoing de serviço seguro com C #'
description: Saiba como garantir a comunicação baseada em remoing de serviço para serviços c# confiáveis que estão a funcionar num cluster de tecido de serviço Azure.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ba68df53f1f21b9ff360772fe1a60c93c8df74d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86252220"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Serviço seguro de remoing comunicações em um serviço C#
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java em Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

A segurança é um dos aspetos mais importantes da comunicação. O quadro de aplicações Reliable Services fornece algumas pilhas de comunicação pré-construídas e ferramentas que você pode usar para melhorar a segurança. Este artigo discute como melhorar a segurança quando está a utilizar o serviço de remoing num serviço C#. Baseia-se num [exemplo](service-fabric-reliable-services-communication-remoting.md) existente que explica como configurar a criação de serviços fiáveis escritos em C#. 

Para ajudar a garantir um serviço quando estiver a utilizar o serviço de remoing com os serviços C#, siga estes passos:

1. Crie uma `IHelloWorldStateful` interface, que defina os métodos que estarão disponíveis para uma chamada de procedimento remoto no seu serviço. O seu serviço utilizará `FabricTransportServiceRemotingListener` , que é declarado no espaço de `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` nomes. Esta é uma `ICommunicationListener` implementação que fornece capacidades de remoing.

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

    Certifique-se de que o certificado que pretende utilizar para ajudar a proteger a sua comunicação de serviço está instalado em todos os nós do cluster. 
    
    > [!NOTE]
    > Nos nós Linux, o certificado deve estar presente como ficheiros formatados por PEM no diretório */var/lib/sfcerts.* Para saber mais, consulte [localização e formato de certificados X.509 nos nós Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Existem duas formas de fornecer configurações de ouvintes e credenciais de segurança:

   1. Fornecer-lhes diretamente no código de serviço:

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
   2. Forneça-os utilizando um [pacote config:](service-fabric-application-and-service-manifests.md)

       Adicione uma secção nomeada `TransportSettings` no ficheiro settings.xml.

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

       Neste caso, o `CreateServiceReplicaListeners` método será assim:

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

        Se adicionar uma `TransportSettings` secção no ficheiro settings.xml, `FabricTransportRemotingListenerSettings` carregará por predefinição todas as definições desta secção.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Neste caso, o `CreateServiceReplicaListeners` método será assim:

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
3. Quando ligar para um serviço seguro, utilizando a pilha de remoting, em vez de utilizar a `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` classe para criar um representante de serviço, use `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` . Passe, `FabricTransportRemotingSettings` que contém `SecurityCredentials` .

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

    Se o código do cliente estiver a funcionar como parte de um serviço, pode carregar `FabricTransportRemotingSettings` a partir do ficheiro settings.xml. Crie uma secção HelloWorldClientTransportSettings que seja semelhante ao código de serviço, como mostrado anteriormente. Esco faça as seguintes alterações ao código do cliente:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o cliente não estiver a funcionar como parte de um serviço, pode criar um ficheiro client_name.settings.xml no mesmo local onde está a client_name.exe. Em seguida, crie uma secção transportSettings nesse ficheiro.

    Semelhante ao serviço, se adicionar uma `TransportSettings` secção no settings.xml/client_name.settings.xml `FabricTransportRemotingSettings` cliente, carrega todas as definições desta secção por predefinição.

    Nesse caso, o código anterior é ainda mais simplificado:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Como passo seguinte, leia [a Web API com OWIN em Serviços Fiáveis.](./service-fabric-reliable-services-communication-aspnetcore.md)
