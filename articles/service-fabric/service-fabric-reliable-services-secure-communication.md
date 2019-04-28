---
title: Proteger as comunicações de comunicação remota do serviço com C# no Azure Service Fabric | Documentos da Microsoft
description: Saiba como proteger a comunicação do serviço de comunicação remota com base para C# reliable services que estão em execução num cluster do Azure Service Fabric.
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
ms.author: suchiagicha
ms.openlocfilehash: f247142f26490e1899256917b64fbec7308fb281
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107675"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Proteger as comunicações de comunicação remota do serviço num C# serviço
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java em Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

A segurança é um dos aspectos mais importantes de comunicação. A estrutura da aplicação de Reliable Services fornece algumas ferramentas que pode utilizar para melhorar a segurança e pilhas de comunicação pré-criados. Este artigo discute como melhorar a segurança, quando estiver usando a comunicação remota do serviço num C# serviço. Ele se baseia no existente [exemplo](service-fabric-reliable-services-communication-remoting.md) que explica como configurar a comunicação remota de serviços fiáveis escritos em C#. 

Para ajudar a proteger um serviço, quando estiver usando a comunicação remota do serviço com o C# serviços, siga estes passos:

1. Criar uma interface, `IHelloWorldStateful`, que define os métodos que estarão disponíveis para uma chamada de procedimento remoto no seu serviço. O serviço irá utilizar `FabricTransportServiceRemotingListener`, que é declarado no `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` espaço de nomes. Este é um `ICommunicationListener` implementação que fornece capacidades de comunicação remota.

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
2. Adicione credenciais de segurança e as definições de escuta.

    Certificar-se de que o certificado que pretende utilizar para ajudar a proteger a comunicação de serviço está instalado em todos os nós do cluster. 
    
    > [!NOTE]
    > Em nós do Linux, o certificado tem de estar presente como arquivos no formato PEM na */var/lib/sfcerts* diretório. Para obter mais informações, consulte [local e o formato de certificados X.509 em nós do Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Há duas maneiras que pode fornecer as definições de escuta e credenciais de segurança:

   1. Disponibilize-lhes diretamente com o código do serviço:

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
   2. Disponibilizar-lhes através de um [pacote de configuração](service-fabric-application-and-service-manifests.md):

       Adicionar uma determinada `TransportSettings` seção no arquivo Settings.

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

       Neste caso, o `CreateServiceReplicaListeners` método terá esta aparência:

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

        Se adicionar um `TransportSettings` secção do arquivo Settings XML, `FabricTransportRemotingListenerSettings` carregará todas as definições nesta seção por predefinição.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Neste caso, o `CreateServiceReplicaListeners` método terá esta aparência:

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
3. Quando chamar métodos num serviço protegido utilizando a pilha de comunicação remota, em vez de utilizar o `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` classe para criar um proxy de serviço, utilize `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Passar `FabricTransportRemotingSettings`, que contém `SecurityCredentials`.

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

    Se estiver a executar o código de cliente como parte de um serviço, pode carregar `FabricTransportRemotingSettings` do arquivo Settings. Crie uma seção de HelloWorldClientTransportSettings é semelhante ao código de serviço, conforme mostrado anteriormente. Efetue as seguintes alterações para o código de cliente:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o cliente não está em execução como parte de um serviço, pode criar um ficheiro de client_name.settings.xml na mesma localização em que é o client_name.exe. Em seguida, crie uma seção de TransportSettings nesse ficheiro.

    Semelhante ao serviço, se adicionar um `TransportSettings` secção cliente settings.xml/client_name.settings.xml, `FabricTransportRemotingSettings` carrega todas as definições nesta seção, por predefinição.

    Nesse caso, o código anterior ainda é simplificado:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Como passo seguinte, leia [API da Web com o OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md).
