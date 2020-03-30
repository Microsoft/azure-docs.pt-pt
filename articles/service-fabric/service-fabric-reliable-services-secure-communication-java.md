---
title: Serviço seguro que remaria as comunicações com Java
description: Aprenda a garantir a comunicação baseada em remo do serviço para serviços fiáveis java que estão a funcionar num cluster Azure Service Fabric.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: adefeadf939d398268624343d82c18cbf5ec87cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609643"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Serviço seguro de remo de comunicações em um serviço Java
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java em Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

A segurança é um dos aspetos mais importantes da comunicação. O quadro de aplicação de Serviços Fiáveis fornece algumas pilhas e ferramentas de comunicação pré-construídas que pode usar para melhorar a segurança. Este artigo discute como melhorar a segurança quando se está a usar o serviço de remo ingestão num serviço Java. Baseia-se num [exemplo](service-fabric-reliable-services-communication-remoting-java.md) existente que explica como se configurar para serviços fiáveis escritos em Java. 

Para ajudar a garantir um serviço quando estiver a utilizar o serviço de remo com os serviços java, siga estes passos:

1. Crie uma `HelloWorldStateless`interface, que defina os métodos que estarão disponíveis para uma chamada de procedimento remoto no seu serviço. O seu `FabricTransportServiceRemotingListener`serviço será utilizado `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` , que está declarado no pacote. Esta é `CommunicationListener` uma implementação que fornece capacidades de remo.

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

    Certifique-se de que o certificado que pretende utilizar para ajudar a proteger a comunicação de serviço está instalado em todos os nós do cluster. Para os serviços em funcionamento no Linux, o certificado deve estar disponível como um ficheiro com formato PEM; ou `.pem` um ficheiro que contenha o `.crt` certificado e a `.key` chave privada ou um ficheiro que contenha o certificado e um ficheiro que contenha a chave privada. Para saber mais, consulte [localização e formato de certificados X.509 nos nós linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Existem duas formas de fornecer configurações de ouvintes e credenciais de segurança:

   1. Forneça-as utilizando uma [embalagem de config:](service-fabric-application-and-service-manifests.md)

       Adicione uma `TransportSettings` secção nomeada no ficheiro definições.xml.

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

       Neste caso, `createServiceInstanceListeners` o método será assim:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Se adicionar `TransportSettings` uma secção no ficheiro definições.xml `FabricTransportListenerSettings` sem qualquer prefixo, carregará todas as definições desta secção por defeito.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Neste caso, `CreateServiceInstanceListeners` o método será assim:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Quando ligar para métodos num serviço seguro utilizando a pilha `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` de remo, em `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`vez de utilizar a classe para criar um representante de serviço, utilize .

    Se o código do cliente estiver a funcionar `FabricTransportSettings` como parte de um serviço, pode carregar a partir do ficheiro definições.xml. Crie uma secção TransportSettings semelhante ao código de serviço, como mostrado anteriormente. Faça as seguintes alterações ao código do cliente:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
