---
title: Proteger as comunicações de comunicação remota do serviço com Java no Azure Service Fabric | Documentos da Microsoft
description: Saiba como proteger a comunicação do serviço de comunicação remota com base para reliable services do Java que estão em execução num cluster do Azure Service Fabric.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: b465ab602a14285f8cf40b24ce1dfa9c763fecb8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60773354"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Proteger as comunicações de comunicação remota do serviço num serviço Java
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java em Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

A segurança é um dos aspectos mais importantes de comunicação. A estrutura da aplicação de Reliable Services fornece algumas ferramentas que pode utilizar para melhorar a segurança e pilhas de comunicação pré-criados. Este artigo discute como melhorar a segurança, quando estiver usando a comunicação remota do serviço num serviço Java. Ele se baseia no existente [exemplo](service-fabric-reliable-services-communication-remoting-java.md) que explica como configurar a comunicação remota de serviços fiáveis escritos em Java. 

Para ajudar a proteger um serviço, quando estiver usando a comunicação remota do serviço com os serviços de Java, siga estes passos:

1. Criar uma interface, `HelloWorldStateless`, que define os métodos que estarão disponíveis para uma chamada de procedimento remoto no seu serviço. O serviço irá utilizar `FabricTransportServiceRemotingListener`, que é declarado no `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` pacote. Este é um `CommunicationListener` implementação que fornece capacidades de comunicação remota.

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
2. Adicione credenciais de segurança e as definições de escuta.

    Certificar-se de que o certificado que pretende utilizar para ajudar a proteger a comunicação de serviço está instalado em todos os nós do cluster. Para os serviços em execução no Linux, o certificado tem de estar disponível como um ficheiro PEM formmatted; de qualquer um `.pem` ficheiro que contém o certificado e chave privada ou uma `.crt` ficheiro que contém o certificado e um `.key` ficheiro que contém a chave privada. Para obter mais informações, consulte [local e o formato de certificados X.509 em nós do Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Há duas maneiras que pode fornecer as definições de escuta e credenciais de segurança:

   1. Disponibilizar-lhes através de um [pacote de configuração](service-fabric-application-and-service-manifests.md):

       Adicionar uma determinada `TransportSettings` seção no arquivo Settings.

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

       Neste caso, o `createServiceInstanceListeners` método terá esta aparência:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Se adicionar um `TransportSettings` secção do arquivo Settings XML sem qualquer prefixo `FabricTransportListenerSettings` carregará todas as definições nesta seção por predefinição.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Neste caso, o `CreateServiceInstanceListeners` método terá esta aparência:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Quando chamar métodos num serviço protegido utilizando a pilha de comunicação remota, em vez de utilizar o `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` classe para criar um proxy de serviço, utilize `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Se estiver a executar o código de cliente como parte de um serviço, pode carregar `FabricTransportSettings` do arquivo Settings. Crie uma seção de TransportSettings é semelhante ao código de serviço, conforme mostrado anteriormente. Efetue as seguintes alterações para o código de cliente:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
