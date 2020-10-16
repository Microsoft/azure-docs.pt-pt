---
title: Comunicações de remoing de serviço seguro com a Java
description: Saiba como garantir a comunicação baseada em remoing de serviço para os serviços fiáveis da Java que estão a funcionar num cluster de tecidos de serviço Azure.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: 01a64fbcfef9f56abb0e1aa6cf7f5d821dd3763b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87325717"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Comunicações de remoing de serviço seguro num serviço Java
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java em Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

A segurança é um dos aspetos mais importantes da comunicação. O quadro de aplicações Reliable Services fornece algumas pilhas de comunicação pré-construídas e ferramentas que você pode usar para melhorar a segurança. Este artigo discute como melhorar a segurança quando se está a utilizar o remoing de serviço num serviço Java. Baseia-se num [exemplo](service-fabric-reliable-services-communication-remoting-java.md) existente que explica como se configurar a remoagem para serviços fiáveis escritos em Java. 

Para ajudar a garantir um serviço quando estiver a utilizar o serviço de remoing com os serviços Java, siga estes passos:

1. Crie uma `HelloWorldStateless` interface, que defina os métodos que estarão disponíveis para uma chamada de procedimento remoto no seu serviço. O seu serviço utilizará `FabricTransportServiceRemotingListener` , que está declarado no `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` pacote. Esta é uma `CommunicationListener` implementação que fornece capacidades de remoing.

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
2. Adicione as definições do ouvinte e as credenciais de segurança.

    Certifique-se de que o certificado que pretende utilizar para ajudar a proteger a sua comunicação de serviço está instalado em todos os nós do cluster. Para os serviços em funcionamento no Linux, o certificado deve estar disponível como ficheiro em forma de PEM; ou um `.pem` ficheiro que contenha o certificado e a chave privada ou um `.crt` ficheiro que contenha o certificado e um `.key` ficheiro que contenha a chave privada. Para saber mais, consulte [localização e formato de certificados X.509 nos nós Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Existem duas formas de fornecer configurações de ouvintes e credenciais de segurança:

   1. Forneça-os utilizando um [pacote config:](service-fabric-application-and-service-manifests.md)

       Adicione uma secção nomeada `TransportSettings` no ficheiro settings.xml.

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

       Neste caso, o `createServiceInstanceListeners` método será assim:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Se adicionar uma `TransportSettings` secção no ficheiro settings.xml sem qualquer prefixo, `FabricTransportListenerSettings` carregará por predefinição todas as definições desta secção.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Neste caso, o `CreateServiceInstanceListeners` método será assim:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Quando ligar para um serviço seguro, utilizando a pilha de remoting, em vez de utilizar a `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` classe para criar um representante de serviço, use `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory` .

    Se o código do cliente estiver a funcionar como parte de um serviço, pode carregar `FabricTransportSettings` a partir do ficheiro settings.xml. Crie uma secção TransportSettings semelhante ao código de serviço, como mostrado anteriormente. Esco faça as seguintes alterações ao código do cliente:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
