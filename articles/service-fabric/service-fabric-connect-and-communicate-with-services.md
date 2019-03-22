---
title: Ligar e comunicar com serviços no Azure Service Fabric | Documentos da Microsoft
description: Saiba como resolver, conectar e se comunicar com serviços no Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: msfussell
ms.assetid: 7d1052ec-2c9f-443d-8b99-b75c97266e6c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: c4516e86e25bb31b113b495a239c9eae9df8c9f8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094774"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Ligue-se e se comunicar com serviços no Service Fabric
No Service Fabric, um serviço é executado em algum lugar no cluster do Service Fabric, geralmente distribuído por várias VMs. Ele pode ser movido de um lugar para outro, o proprietário do serviço, tanto pelo automaticamente pelo Service Fabric. Os serviços não estão estaticamente vinculados a uma máquina específica ou o endereço.

Uma aplicação do Service Fabric em geral, é composta por vários serviços diferentes, onde cada serviço executa uma tarefa de especializados. Estes serviços podem se comunicar entre si para formar uma função de conclua, como partes diferentes do processamento de um aplicativo web. Também existem aplicações de cliente que se ligam para e comunicam com os serviços. Este documento explica como configurar a comunicação com e entre os seus serviços no Service Fabric.

## <a name="bring-your-own-protocol"></a>Traga seu próprio protocolo
Service Fabric ajuda a gerir o ciclo de vida dos seus serviços, mas não faz decisões sobre o que fazem seus serviços. Isto inclui a comunicação. Quando seu serviço é aberto pelo Service Fabric, que é a oportunidade de seu serviço de configurar um ponto final para pedidos recebidos, com qualquer pilha de protocolo ou comunicação que pretende. O serviço irá escutar um normal **IP: porta** usando qualquer esquema de endereçamento, como um URI de endereço. Várias instâncias de serviço ou réplicas podem partilhar um processo de host, caso em que eles terão de utilizar portas diferentes ou utilizar um mecanismo de compartilhamento da porta, como o driver do kernel HTTP. sys no Windows. Em ambos os casos, cada instância de serviço ou a réplica num processo de anfitrião tem de ser exclusivamente endereçável.

![pontos finais de serviço][1]

## <a name="service-discovery-and-resolution"></a>Deteção do serviço e resolução
Num sistema distribuído, os serviços podem mover de uma máquina para outra ao longo do tempo. Isto pode acontecer por várias razões, incluindo balanceamento, atualizações, as ativações pós-falha ou Escalamento horizontal de recurso. Isso significa que os endereços de ponto final de serviço alterar à medida que o serviço se move para nós com endereços IP diferentes e pode abrir nas portas diferentes, se o serviço utiliza uma porta dinamicamente selecionada.

![Distribuição de serviços][7]

O Service Fabric fornece um serviço de deteção e resolução chamado o serviço de nomenclatura. O serviço de nomenclatura mantém uma tabela que é mapeado com o nome instâncias de serviço para os endereços de ponto final que escutam. Todas as instâncias de serviço com nome no Service Fabric possuem nomes exclusivos representados como URIs, por exemplo, `"fabric:/MyApplication/MyService"`. O nome do serviço não se altera ao longo do tempo de vida do serviço, é apenas os endereços de ponto de extremidade que podem ser alteradas quando mover de serviços. Isso é análogo aos Web sites que tenham URLs constantes, mas onde pode alterar o endereço IP. E semelhante ao DNS na web, que resolve os URLs de Web site para endereços IP, Service Fabric tem uma entidade de registo que mapeia nomes de serviço para o respetivo endereço de ponto final.

![pontos finais de serviço][2]

Resolução e ligar a serviços envolvem os seguintes passos que executar num loop:

* **Resolver**: Obter o ponto final que publicou um serviço do serviço de nomenclatura.
* **Ligar**: Liga ao serviço através de qualquer protocolo utiliza no ponto de extremidade.
* **Repita**: Uma tentativa de ligação poderá falhar por diversas razões, para o exemplo, se o serviço foi movido, uma vez que a última vez que o endereço de ponto final foi resolvido. Nesse caso, precedente resolver e ligue-se passos têm de ser repetida e esse ciclo é repetido até que a ligação é bem-sucedida.

## <a name="connecting-to-other-services"></a>Ligar a outros serviços
Ligar entre si dentro de um cluster em geral de serviços podem acessar diretamente os pontos finais de outros serviços porque os nós num cluster estão na mesma rede local. Para tornar mais fácil ligar entre os serviços, o Service Fabric fornece serviços adicionais que utilizam o serviço de nomenclatura. Um serviço DNS e um serviço de proxy inverso.


### <a name="dns-service"></a>Serviço DNS
Já que muitos serviços, especialmente em contentores de serviços, pode ter um nome de URL existente, ser capaz de resolver estes usando o DNS padrão protocol (em vez do protocolo de Naming Service) é muito conveniente, especialmente em cenários de "lift- and -shift" de aplicações. Isso é exatamente o que faz o serviço DNS. Permite-lhe mapear nomes DNS para um nome de serviço e, por conseguinte, resolver endereços IP do ponto final. 

Conforme mostrado no diagrama seguinte, o serviço DNS, em execução no cluster do Service Fabric, mapeia nomes DNS para nomes de serviço que são, em seguida, resolvidos pelo serviço de nomenclatura para devolver os endereços de ponto de extremidade para ligar a. O nome DNS para o serviço está disponível no momento da criação. 

![pontos finais de serviço][9]

Para obter mais detalhes sobre como utilizar o DNS do serviço ver [serviço DNS no Azure Service Fabric](service-fabric-dnsservice.md) artigo.

### <a name="reverse-proxy-service"></a>Serviço de proxy inverso
O proxy inverso lida com os serviços do cluster que expõe os pontos de extremidade HTTP incluindo HTTPS. O proxy inverso simplifica a chamar outros serviços e seus métodos, fazendo com que um formato URI específico e manipula o resolver, ligar, repita os passos necessários para um serviço comunicar com outra com o serviço de nomenclatura. Em outras palavras, ele oculta o serviço de nomenclatura do utilizador ao chamar outros serviços, fazendo isso tão simples quanto chamar um URL.

![pontos finais de serviço][10]

Para obter mais detalhes sobre como utilizar o serviço de proxy inverso, consulte [proxy no Azure Service Fabric inverso](service-fabric-reverseproxy.md) artigo.

## <a name="connections-from-external-clients"></a>Ligações de clientes externos
Ligar entre si dentro de um cluster em geral de serviços podem acessar diretamente os pontos finais de outros serviços porque os nós num cluster estão na mesma rede local. Em alguns ambientes, no entanto, um cluster pode ser atrás de um balanceador de carga que encaminha o tráfego de entrada externos por meio de um conjunto limitado de portas. Nestes casos, serviços ainda podem comunicar entre si e resolver os endereços com o serviço de nomenclatura, mas são necessárias etapas adicionais para permitir que os clientes externos se ligam aos serviços.

## <a name="service-fabric-in-azure"></a>O Service Fabric no Azure
Um cluster do Service Fabric no Azure é colocado atrás de um balanceador de carga do Azure. Todo o tráfego externo para o cluster tem de passar pelo balanceador de carga. O Balanceador de carga automaticamente irá reencaminhar o tráfego de entrada numa determinada porta para um aleatório *nó* que tenha a mesma porta abrir. O Azure Load Balancer só sabe sobre portas, abertas na *nós*, ele não sabe sobre portas abertas por indivíduo *serviços*.

![Topologia de Balanceador de carga e o Service Fabric do Azure][3]

Por exemplo, para aceitar o tráfego na porta externo **80**, tem de ser configurados os seguintes procedimentos:

1. Escreva um serviço que fica à escuta na porta 80. Configurar a porta 80 no servicemanifest. XML do serviço e abra um serviço de escuta no serviço, por exemplo, um servidor web de hospedagem interna.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Criar um Cluster do Service Fabric no Azure e especifique a porta **80** como uma porta de ponto final personalizado para o tipo de nó que irá alojar o serviço. Se tiver mais de um tipo de nó, pode configurar uma *restrição de posicionamento* no serviço para se certificar de que só é executada no tipo de nó que tenha a porta de ponto final personalizado aberta.

    ![Abrir uma porta num tipo de nó][4]
3. Depois do cluster ter sido criado, configure o Balanceador de carga do Azure no grupo de recursos do cluster para encaminhar o tráfego na porta 80. Ao criar um cluster através do portal do Azure, este passo é configurado automaticamente para cada porta de ponto final personalizado que foi configurada.

    ![Encaminhamento de tráfego no balanceador de carga do Azure][5]
4. O Balanceador de carga do Azure utiliza uma sonda para determinar se deve ou não enviar tráfego para um determinado nó. A sonda verifica periodicamente a um ponto de extremidade em cada nó para determinar se é ou não o nó está a responder. Se a sonda falhará receber uma resposta após um número de vezes configurado, o Balanceador de carga deixará de enviar o tráfego para esse nó. Ao criar um cluster através do portal do Azure, uma sonda é configurada automaticamente para cada porta de ponto final personalizado que foi configurada.

    ![Encaminhamento de tráfego no balanceador de carga do Azure][8]

É importante lembrar-se de que o Balanceador de carga do Azure e a sonda apenas sabem sobre o *nós*, e não a *serviços* em execução em nós. O Azure Load Balancer sempre irão enviar o tráfego a nós respondem à sonda, pelo que deve ter cuidado para garantir que os serviços estão disponíveis em nós que são capazes de responder para a sonda.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: Opções de API de comunicação incorporada
A estrutura de Reliable Services vem com várias opções de comunicação criados previamente. A decisão sobre qual um funcionará melhor para depende da opção do modelo de programação, a estrutura de comunicação e a linguagem de programação que seus serviços são escritos em.

* **Nenhum protocolo específico:**  Se não tiver uma opção específica do framework de comunicação, mas pretende obter algo cópia de segurança e a funcionar rapidamente, em seguida, é a opção ideal para [comunicação remota do serviço](service-fabric-reliable-services-communication-remoting.md), que permite a rigidez de tipos de procedimento remoto chama para Reliable Services e Reliable Actors. Esta é a maneira mais fácil e rápida para iniciar a comunicação de serviço. Comunicação remota do serviço processa a resolução de endereços do serviço, ligação, tente novamente e tratamento de erros. Isto está disponível para C# e aplicações de Java.
* **HTTP**: Para a comunicação de linguagem desconhecida, o HTTP fornece uma opção de norma da indústria com ferramentas e servidores HTTP disponíveis em várias linguagens diferentes, todos suportadas pelo Service Fabric. Os serviços podem utilizar qualquer pilha HTTP disponível, incluindo [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) para C# aplicações. Os clientes gravados em C# pode tirar partido do `ICommunicationClient` e `ServicePartitionClient` classes, ao passo que para Java, utilize o `CommunicationClient` e `FabricServicePartitionClient` classes, [para resolução de serviço, ligações HTTP e loops de repetição](service-fabric-reliable-services-communication.md).
* **WCF**: Se tiver o código existente que usa WCF como sua arquitetura de comunicação, em seguida, pode utilizar o `WcfCommunicationListener` no lado do servidor e `WcfCommunicationClient` e `ServicePartitionClient` classes para o cliente. Isso no entanto só está disponível para C# clusters baseados em aplicativos no Windows. Para obter mais detalhes, consulte este artigo [baseadas no WCF implementação da pilha de comunicação](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Usando protocolos personalizados e outras estruturas de comunicação
Os serviços podem utilizar qualquer protocolo ou a arquitetura para a comunicação, quer se trate de um protocolo binário personalizado através de soquetes TCP ou eventos de transmissão em fluxo por meio [os Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) ou [IoT Hub do Azure](https://azure.microsoft.com/services/iot-hub/). O Service Fabric fornece comunicação APIs que pode conectar sua pilha de comunicações, enquanto todo o trabalho para descoberta e ligação é abstraído do utilizador. Veja este artigo o [modelo de comunicação de Reliable Service](service-fabric-reliable-services-communication.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre os conceitos e as APIs disponíveis na [modelo de comunicação de Reliable Services](service-fabric-reliable-services-communication.md), em seguida, começar rapidamente com [comunicação remota do serviço](service-fabric-reliable-services-communication-remoting.md) ou aprofundar-se de que aprender a escrever uma comunicação serviço de escuta usando [API da Web com o OWIN hospedar internamente](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
