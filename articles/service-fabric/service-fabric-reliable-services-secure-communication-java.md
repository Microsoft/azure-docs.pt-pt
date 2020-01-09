---
title: Proteger comunicações remotas de serviço com Java
description: Saiba como proteger a comunicação baseada em comunicação remota do serviço para os serviços confiáveis do Java que estão em execução em um cluster de Service Fabric do Azure.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: adefeadf939d398268624343d82c18cbf5ec87cd
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609643"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Proteger comunicações remotas de serviço em um serviço Java
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java em Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

A segurança é um dos aspectos mais importantes da comunicação. A estrutura de aplicativo Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que você pode usar para aprimorar a segurança. Este artigo discute como melhorar a segurança quando você está usando a comunicação remota do serviço em um serviço Java. Ele se baseia em um [exemplo](service-fabric-reliable-services-communication-remoting-java.md) existente que explica como configurar a comunicação remota para Reliable Services escritos em Java. 

Para ajudar a proteger um serviço quando você estiver usando a comunicação remota do serviço com os serviços Java, siga estas etapas:

1. Crie uma interface, `HelloWorldStateless`, que define os métodos que estarão disponíveis para uma chamada de procedimento remoto em seu serviço. Seu serviço usará `FabricTransportServiceRemotingListener`, que é declarado no pacote de `microsoft.serviceFabric.services.remoting.fabricTransport.runtime`. Essa é uma implementação `CommunicationListener` que fornece recursos de comunicação remota.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Adicionar configurações de ouvinte e credenciais de segurança.

    Verifique se o certificado que você deseja usar para ajudar a proteger a comunicação do serviço está instalado em todos os nós no cluster. Para serviços em execução no Linux, o certificado deve estar disponível como um arquivo PEM-formmatted; um arquivo de `.pem` que contém o certificado e a chave privada ou um arquivo de `.crt` que contém o certificado e um arquivo de `.key` que contém a chave privada. Para saber mais, confira [localização e formato dos certificados X. 509 em nós do Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Há duas maneiras de fornecer configurações de ouvinte e credenciais de segurança:

   1. Forneça-os usando um [pacote de configuração](service-fabric-application-and-service-manifests.md):

       Adicione uma seção `TransportSettings` nomeada no arquivo Settings. xml.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       Nesse caso, o método `createServiceInstanceListeners` terá a seguinte aparência:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Se você adicionar um `TransportSettings` seção no arquivo Settings. XML sem nenhum prefixo, `FabricTransportListenerSettings` carregará todas as configurações desta seção por padrão.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Nesse caso, o método `CreateServiceInstanceListeners` terá a seguinte aparência:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Quando você chama métodos em um serviço protegido usando a pilha de comunicação remota, em vez de usar a classe `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` para criar um proxy de serviço, use `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Se o código do cliente estiver sendo executado como parte de um serviço, você poderá carregar `FabricTransportSettings` do arquivo Settings. xml. Crie uma seção TransportSettings semelhante ao código de serviço, conforme mostrado anteriormente. Faça as seguintes alterações no código do cliente:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
