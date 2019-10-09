---
title: Proteger as comunicações de comunicação C# remota do serviço com o no Azure Service Fabric | Microsoft Docs
description: Saiba como proteger a comunicação baseada em Remoting do C# serviço para Reliable Services que estão em execução em um cluster de Service Fabric do Azure.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: c252ec31a64fa3a11973db7a8de0a440d8eed6f5
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166565"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Proteger comunicações remotas de serviço C# em um serviço
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java em Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

A segurança é um dos aspectos mais importantes da comunicação. A estrutura de aplicativo Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que você pode usar para aprimorar a segurança. Este artigo discute como melhorar a segurança quando você está usando a comunicação remota do serviço C# em um serviço. Ele se baseia em um [exemplo](service-fabric-reliable-services-communication-remoting.md) existente que explica como configurar a comunicação remota para Reliable Services C#gravados no. 

Para ajudar a proteger um serviço quando você estiver usando a comunicação C# remota do serviço com serviços, siga estas etapas:

1. Crie uma interface, `IHelloWorldStateful`, que define os métodos que estarão disponíveis para uma chamada de procedimento remoto em seu serviço. Seu serviço usará `FabricTransportServiceRemotingListener`, que é declarado no namespace `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime`. Essa é uma implementação `ICommunicationListener` que fornece recursos de comunicação remota.

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
2. Adicionar configurações de ouvinte e credenciais de segurança.

    Verifique se o certificado que você deseja usar para ajudar a proteger a comunicação do serviço está instalado em todos os nós no cluster. 
    
    > [!NOTE]
    > Em nós do Linux, o certificado deve estar presente como arquivos formatados por PEM no diretório */var/lib/sfcerts* Para saber mais, confira [localização e formato dos certificados X. 509 em nós do Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Há duas maneiras de fornecer configurações de ouvinte e credenciais de segurança:

   1. Forneça-os diretamente no código do serviço:

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
   2. Forneça-os usando um [pacote de configuração](service-fabric-application-and-service-manifests.md):

       Adicione uma seção `TransportSettings` nomeada no arquivo Settings. xml.

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

       Nesse caso, o método `CreateServiceReplicaListeners` terá a seguinte aparência:

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

        Se você adicionar uma seção `TransportSettings` no arquivo Settings. xml, `FabricTransportRemotingListenerSettings` carregará todas as configurações desta seção por padrão.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Nesse caso, o método `CreateServiceReplicaListeners` terá a seguinte aparência:

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
3. Quando você chama métodos em um serviço protegido usando a pilha de comunicação remota, em vez de usar a classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` para criar um proxy de serviço, use `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Passe `FabricTransportRemotingSettings`, que contém `SecurityCredentials`.

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

    Se o código do cliente estiver sendo executado como parte de um serviço, você poderá carregar `FabricTransportRemotingSettings` do arquivo Settings. xml. Crie uma seção HelloWorldClientTransportSettings semelhante ao código de serviço, conforme mostrado anteriormente. Faça as seguintes alterações no código do cliente:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o cliente não estiver sendo executado como parte de um serviço, você poderá criar um arquivo client_name. Settings. xml no mesmo local em que o client_name. exe é. Em seguida, crie uma seção TransportSettings nesse arquivo.

    Semelhante ao serviço, se você adicionar uma seção `TransportSettings` em configurações do cliente. xml/client_name. Settings. xml, `FabricTransportRemotingSettings` carregará todas as configurações desta seção por padrão.

    Nesse caso, o código anterior é ainda mais simplificado:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Como uma próxima etapa, leia [API da Web com OWIN em Reliable Services](service-fabric-reliable-services-communication-webapi.md).
