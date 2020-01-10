---
title: Conectar-se e comunicar-se com serviços no Azure Service Fabric
description: Saiba como resolver, conectar e se comunicar com serviços no Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: e57d169decf482f8b8be1e3b31a07690bc222c5d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458237"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Conectar-se e comunicar-se com serviços no Service Fabric
No Service Fabric, um serviço é executado em algum lugar em um Cluster Service Fabric, normalmente distribuído entre várias VMs. Ele pode ser movido de um local para outro, pelo proprietário do serviço ou automaticamente pelo Service Fabric. Os serviços não estão estaticamente vinculados a um determinado computador ou endereço.

Um aplicativo Service Fabric geralmente é composto por vários serviços diferentes, onde cada serviço executa uma tarefa especializada. Esses serviços podem se comunicar entre si para formar uma função completa, como a renderização de diferentes partes de um aplicativo Web. Também há aplicativos cliente que se conectam e se comunicam com serviços. Este documento discute como configurar a comunicação com e entre seus serviços no Service Fabric.

## <a name="bring-your-own-protocol"></a>Traga seu próprio protocolo
Service Fabric ajuda a gerenciar o ciclo de vida de seus serviços, mas não toma decisões sobre o que seus serviços fazem. Isso inclui a comunicação. Quando o serviço é aberto pelo Service Fabric, essa é a oportunidade do serviço de configurar um ponto de extremidade para solicitações de entrada, usando qualquer protocolo ou pilha de comunicação que você desejar. Seu serviço escutará em um endereço **IP: porta** normal usando qualquer esquema de endereçamento, como um URI. Várias instâncias ou réplicas de serviço podem compartilhar um processo de host; nesse caso, eles precisarão usar portas diferentes ou usar um mecanismo de compartilhamento de porta, como o driver de kernel http. sys no Windows. Em ambos os casos, cada instância de serviço ou réplica em um processo de host deve ser endereçável exclusivamente.

![pontos de extremidade de serviço][1]

## <a name="service-discovery-and-resolution"></a>Descoberta e resolução de serviço
Em um sistema distribuído, os serviços podem mudar de um computador para outro ao longo do tempo. Isso pode ocorrer por vários motivos, incluindo balanceamento de recursos, atualizações, failovers ou expansão. Isso significa que os endereços de ponto de extremidade de serviço são alterados conforme o serviço é movido para nós com endereços IP diferentes e podem ser abertos em portas diferentes se o serviço usar uma porta selecionada dinamicamente.

![Distribuição de serviços][7]

Service Fabric fornece um serviço de descoberta e resolução chamado de Serviço de Nomenclatura. O Serviço de Nomenclatura mantém uma tabela que mapeia instâncias de serviço nomeadas para os endereços de ponto de extremidade que escutam. Todas as instâncias de serviço nomeadas no Service Fabric têm nomes exclusivos representados como URIs, por exemplo, `"fabric:/MyApplication/MyService"`. O nome do serviço não é alterado durante o tempo de vida do serviço; são apenas os endereços de ponto de extremidade que podem ser alterados quando os serviços são movidos. Isso é análogo aos sites que têm URLs constantes, mas onde o endereço IP pode mudar. E semelhante ao DNS na Web, que resolve URLs de site para endereços IP, Service Fabric tem um registrador que mapeia nomes de serviço para seu endereço de ponto de extremidade.

![pontos de extremidade de serviço][2]

A resolução e a conexão a serviços envolve as seguintes etapas executadas em um loop:

* **Resolver**: Obtenha o ponto de extremidade que um serviço publicou do serviço de nomenclatura.
* **Conectar**: Conecte-se ao serviço em qualquer protocolo usado por ele no ponto de extremidade.
* **Repetir**: uma tentativa de conexão pode falhar por vários motivos, por exemplo, se o serviço foi movido desde a última vez em que o endereço do ponto de extremidade foi resolvido. Nesse caso, as etapas de resolução e conexão anteriores precisam ser repetidas e esse ciclo é repetido até que a conexão seja realizada com sucesso.

## <a name="connecting-to-other-services"></a>Conectando-se a outros serviços
Serviços que se conectam entre si dentro de um cluster geralmente podem acessar diretamente os pontos de extremidade de outros serviços, pois os nós em um cluster estão na mesma rede local. Para facilitar a conexão entre os serviços do, Service Fabric fornece serviços adicionais que usam o Serviço de Nomenclatura. Um serviço DNS e um serviço de proxy reverso.


### <a name="dns-service"></a>Serviço DNS
Como muitos serviços, especialmente serviços em contêineres, podem ter um nome de URL existente, ser capaz de resolvê-los usando o protocolo DNS padrão (em vez do protocolo Serviço de Nomenclatura) é muito conveniente, especialmente em cenários de "desvio e deslocamento" do aplicativo. Isso é exatamente o que o serviço DNS faz. Ele permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP de ponto de extremidade. 

Conforme mostrado no diagrama a seguir, o serviço DNS, em execução no Cluster Service Fabric, mapeia nomes DNS para nomes de serviço que são então resolvidos pelo Serviço de Nomenclatura para retornar os endereços de ponto de extremidade aos quais se conectar. O nome DNS para o serviço é fornecido no momento da criação. 

![pontos de extremidade de serviço][9]

Para obter mais detalhes sobre como usar o serviço DNS, consulte o artigo [serviço DNS no Azure Service Fabric](service-fabric-dnsservice.md) .

### <a name="reverse-proxy-service"></a>Serviço de proxy reverso
O proxy reverso lida com os serviços no cluster que expõe os pontos de extremidade HTTP, incluindo HTTPS. O proxy reverso simplifica bastante a chamada de outros serviços e seus métodos, tendo um formato de URI específico e manipula as etapas de resolução, conexão e repetição necessárias para que um serviço se comunique com outro usando o Serviço de Nomenclatura. Em outras palavras, ele oculta o Serviço de Nomenclatura de você ao chamar outros serviços, tornando-o tão simples quanto chamar uma URL.

![pontos de extremidade de serviço][10]

Para obter mais detalhes sobre como usar o serviço de proxy reverso, consulte o artigo [proxy reverso no Azure Service Fabric](service-fabric-reverseproxy.md) .

## <a name="connections-from-external-clients"></a>Conexões de clientes externos
Serviços que se conectam entre si dentro de um cluster geralmente podem acessar diretamente os pontos de extremidade de outros serviços, pois os nós em um cluster estão na mesma rede local. Em alguns ambientes, no entanto, um cluster pode estar atrás de um balanceador de carga que roteia o tráfego de entrada externo por meio de um conjunto limitado de portas. Nesses casos, os serviços ainda podem se comunicar entre si e resolver endereços usando o Serviço de Nomenclatura, mas etapas adicionais devem ser tomadas para permitir que clientes externos se conectem aos serviços.

## <a name="service-fabric-in-azure"></a>Service Fabric no Azure
Um Cluster Service Fabric no Azure é colocado atrás de um Azure Load Balancer. Todo o tráfego externo para o cluster deve passar pelo balanceador de carga. O balanceador de carga encaminhará automaticamente o tráfego de entrada em uma determinada porta para um *nó* aleatório que tenha a mesma porta aberta. A Azure Load Balancer só conhece as portas abertas nos *nós*, ela não sabe sobre as portas abertas por *Serviços*individuais.

![Topologia de Azure Load Balancer e Service Fabric][3]

Por exemplo, para aceitar o tráfego externo na porta **80**, os seguintes itens devem ser configurados:

1. Gravar um serviço que escuta na porta 80. Configure a porta 80 no arquivo Service manifest. XML do serviço e abra um ouvinte no serviço, por exemplo, um servidor Web hospedado automaticamente.

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
2. Crie um Cluster Service Fabric no Azure e especifique a porta **80** como uma porta de ponto de extremidade personalizada para o tipo de nó que hospedará o serviço. Se você tiver mais de um tipo de nó, poderá configurar uma *restrição de posicionamento* no serviço para garantir que ele seja executado apenas no tipo de nó que tem a porta de ponto de extremidade personalizada aberta.

    ![Abrir uma porta em um tipo de nó][4]
3. Depois que o cluster tiver sido criado, configure o Azure Load Balancer no grupo de recursos do cluster para encaminhar o tráfego na porta 80. Ao criar um cluster por meio do portal do Azure, isso é configurado automaticamente para cada porta de ponto de extremidade personalizada que foi configurada.

    ![Encaminhe o tráfego no Azure Load Balancer][5]
4. O Azure Load Balancer usa uma investigação para determinar se o tráfego deve ser enviado ou não para um nó específico. A investigação verifica periodicamente um ponto de extremidade em cada nó para determinar se o nó está respondendo ou não. Se a investigação não receber uma resposta após um número configurado de vezes, o balanceador de carga interromperá o envio de tráfego para esse nó. Ao criar um cluster por meio do portal do Azure, uma investigação é automaticamente configurada para cada porta de ponto de extremidade personalizada que foi configurada.

    ![Encaminhe o tráfego no Azure Load Balancer][8]

É importante lembrar que o Azure Load Balancer e a investigação só conhecem os *nós*, não os *Serviços* em execução nos nós. O Azure Load Balancer sempre enviará tráfego para nós que respondem à investigação, portanto, é necessário tomar cuidado para garantir que os serviços estejam disponíveis nos nós que possam responder à investigação.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: opções de API de comunicação internas
O Reliable Services Framework é fornecido com várias opções de comunicação predefinidas. A decisão sobre qual delas funcionará melhor para você depende da escolha do modelo de programação, da estrutura de comunicação e da linguagem de programação na qual os serviços são escritos.

* **Nenhum protocolo específico:**  Se você não tiver uma opção específica de estrutura de comunicação, mas quiser colocar algo em funcionamento rapidamente, a opção ideal para você é a [comunicação remota do serviço](service-fabric-reliable-services-communication-remoting.md), que permite chamadas de procedimento remoto com rigidez de tipos para Reliable Services e Reliable Actors. Essa é a maneira mais fácil e rápida de começar a comunicação do serviço. A comunicação remota do serviço manipula a resolução de endereços de serviço, conexão, repetição e tratamento de erros. Isso está disponível para aplicativos C# Java e.
* **Http**: para comunicação independente de idioma, o http fornece uma opção padrão do setor com ferramentas e servidores http disponíveis em vários idiomas diferentes, tudo com suporte do Service Fabric. Os serviços podem usar qualquer pilha HTTP disponível, [](service-fabric-reliable-services-communication-webapi.md) incluindo ASP.NET Web API C# para aplicativos. Os clientes escritos C# em podem aproveitar as classes `ICommunicationClient` e `ServicePartitionClient`, enquanto para Java, usam as classes `CommunicationClient` e `FabricServicePartitionClient`, [para a resolução de serviço, conexões http e loops de repetição](service-fabric-reliable-services-communication.md).
* **WCF**: se você tiver um código existente que usa o WCF como sua estrutura de comunicação, poderá usar o `WcfCommunicationListener` para o lado do servidor e `WcfCommunicationClient` e `ServicePartitionClient` classes para o cliente. No entanto, isso só C# está disponível para aplicativos em clusters baseados no Windows. Para obter mais detalhes, consulte este artigo sobre [a implementação baseada no WCF da pilha de comunicação](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Usando protocolos personalizados e outras estruturas de comunicação
Os serviços podem usar qualquer protocolo ou estrutura para comunicação, seja um protocolo binário personalizado sobre Soquetes TCP ou eventos de streaming por meio dos [hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) ou [do Hub IOT do Azure](https://azure.microsoft.com/services/iot-hub/). Service Fabric fornece APIs de comunicação para as quais você pode conectar sua pilha de comunicação, enquanto todo o trabalho para descobrir e conectar é dissociado de você. Consulte este artigo sobre o [modelo de comunicação do Reliable Services](service-fabric-reliable-services-communication.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os conceitos e as APIs disponíveis no [modelo de comunicação Reliable Services](service-fabric-reliable-services-communication.md), em seguida, comece rapidamente com a [comunicação remota do serviço](service-fabric-reliable-services-communication-remoting.md) ou Aprofunde-se para saber como escrever um ouvinte de comunicação usando a [API da Web com o OWIN Self-host](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
