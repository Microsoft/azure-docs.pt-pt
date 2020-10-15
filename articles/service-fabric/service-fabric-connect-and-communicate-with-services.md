---
title: Conecte-se e comunique com os serviços em Azure Service Fabric
description: Saiba como resolver, conectar e comunicar com serviços em Tecido de Serviço.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 715089d40f584fbbaf23f674e4243c92c718e9d1
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093332"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Conecte-se e comunique-se com serviços em Tecido de Serviço
Em Service Fabric, um serviço funciona em algum lugar em um cluster de Tecido de Serviço, normalmente distribuído por vários VMs. Pode ser movido de um lugar para outro, seja pelo proprietário do serviço, ou automaticamente pela Service Fabric. Os serviços não estão estáticos ligados a uma determinada máquina ou endereço.

Uma aplicação de Tecido de Serviço é geralmente composta por muitos serviços diferentes, onde cada serviço executa uma tarefa especializada. Estes serviços podem comunicar entre si para formar uma função completa, como a prestação de diferentes partes de uma aplicação web. Há também aplicações de clientes que se ligam e comunicam com os serviços. Este documento discute como configurar a comunicação com e entre os seus serviços em Service Fabric.

## <a name="bring-your-own-protocol"></a>Traga o seu próprio protocolo
A Service Fabric ajuda a gerir o ciclo de vida dos seus serviços, mas não tomar decisões sobre o que os seus serviços fazem. Isto inclui comunicação. Quando o seu serviço é aberto pela Service Fabric, esta é a oportunidade do seu serviço de configurar um ponto final para pedidos de entrada, usando qualquer protocolo ou stack de comunicação que você deseja. O seu serviço irá ouvir um endereço **IP:porta** normal utilizando qualquer esquema de endereço, como um URI. Várias instâncias de serviço ou réplicas podem partilhar um processo de anfitrião, caso em que precisarão de utilizar portas diferentes ou usar um mecanismo de partilha de porta, como o http.sys o controlador de núcleo no Windows. Em qualquer dos casos, cada instância de serviço ou réplica num processo de hospedeiro deve ser únicamente endereçada.

![pontos finais de serviço][1]

## <a name="service-discovery-and-resolution"></a>Descoberta e resolução de serviços
Num sistema distribuído, os serviços podem mover-se de uma máquina para outra ao longo do tempo. Isto pode acontecer por várias razões, incluindo o equilíbrio de recursos, upgrades, failovers ou escala-out. Isto significa que os endereços de ponto final de serviço mudam à medida que o serviço se move para nós com diferentes endereços IP, e pode abrir em diferentes portas se o serviço utilizar uma porta selecionada dinamicamente.

![Distribuição de serviços][7]

A Service Fabric fornece um serviço de descoberta e resolução chamado Serviço de Nomeação. O Serviço de Nomeação mantém uma tabela que mapeia casos de serviço nomeados para os endereços de ponto final que ouvem. Todas as instâncias de serviço nomeadas em Service Fabric têm nomes únicos representados como URIs, por exemplo, `"fabric:/MyApplication/MyService"` . O nome do serviço não muda ao longo da vida útil do serviço, são apenas os endereços de ponto final que podem mudar quando os serviços se movem. Isto é análogo aos websites que têm URLs constantes, mas onde o endereço IP pode mudar. E semelhante ao DNS na web, que resolve urls do site para endereços IP, o Service Fabric tem um registrador que mapeia nomes de serviço para o seu endereço de ponto final.

![Diagrama que mostra que o Service Fabric tem um registrador que mapeia nomes de serviço para o seu endereço de ponto final.][2]

A resolução e a ligação aos serviços envolvem os seguintes passos executados em loop:

* **Resolver**: Obtenha o ponto final que um serviço publicou a partir do Serviço de Nomeação.
* **Conecte-se:** Ligue-se ao serviço sobre qualquer protocolo que utilize nesse ponto final.
* **Redacionar**: Uma tentativa de ligação pode falhar por várias razões, por exemplo, se o serviço tiver sido movido desde a última vez que o endereço do ponto final foi resolvido. Nesse caso, os passos de resolução e ligação anteriores precisam de ser novamente julgados, e este ciclo é repetido até que a ligação tenha sucesso.

## <a name="connecting-to-other-services"></a>Ligação a outros serviços
Os serviços que se conectam entre si dentro de um cluster geralmente podem aceder diretamente aos pontos finais de outros serviços porque os nós de um cluster estão na mesma rede local. Para facilitar a ligação entre serviços, a Service Fabric fornece serviços adicionais que utilizam o Serviço de Nomeação. Um serviço DNS e um serviço de procuração inversa.


### <a name="dns-service"></a>Serviço DNS
Uma vez que muitos serviços, especialmente serviços contentorizados, podem ter um nome URL existente, ser capaz de resolvê-los usando o protocolo padrão DNS (em vez do protocolo de Serviço de Nomeação) é muito conveniente, especialmente em cenários de "levantar e mudar" de aplicação. Isto é exatamente o que o serviço DNS faz. Permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP de ponto final. 

Como mostrado no diagrama seguinte, o serviço DNS, em execução no cluster De Tecido de Serviço, mapeia nomes DNS para nomes de serviço que são então resolvidos pelo Serviço de Nomeação para devolver os endereços de ponto final para se ligar. O nome DNS para o serviço é fornecido no momento da criação. 

![Diagrama que mostra como o serviço DNS, ao executar no cluster de Tecido de Serviço, mapeia nomes DNS para nomes de serviço que são então resolvidos pelo Serviço de Nomeação para devolver os endereços de ponto final para se ligar.][9]

Para mais detalhes sobre como usar o serviço DNS consulte o serviço DNS no artigo [da Azure Service Fabric.](service-fabric-dnsservice.md)

### <a name="reverse-proxy-service"></a>Serviço de procuração inversa
O proxy reverso endereça serviços no cluster que expõe pontos finais HTTP, incluindo HTTPS. O proxy reverso simplifica muito a chamada de outros serviços e seus métodos, tendo um formato URI específico e lida com os passos de resolução, ligação e reação necessários para que um serviço comunique com outro usando o Serviço de Nomeação. Por outras palavras, esconde o Serviço de Nomeação de si ao ligar para outros serviços, tornando isto tão simples como chamar um URL.

![Diagrama que mostra como o proxy invertido aborda os serviços no cluster que expõe pontos finais HTTP, incluindo HTTPS.][10]

Para obter mais detalhes sobre como utilizar o serviço de procuração inversa consulte Reverse proxy no artigo [do Azure Service Fabric.](service-fabric-reverseproxy.md)

## <a name="connections-from-external-clients"></a>Ligações de clientes externos
Os serviços que se conectam entre si dentro de um cluster geralmente podem aceder diretamente aos pontos finais de outros serviços porque os nós de um cluster estão na mesma rede local. Em alguns ambientes, no entanto, um cluster pode estar por trás de um equilibrador de carga que encaminha o tráfego através de um conjunto limitado de portos. Nestes casos, os serviços ainda podem comunicar entre si e resolver endereços usando o Serviço de Nomeação, mas devem ser tomadas medidas adicionais para permitir que os clientes externos se conectem aos serviços.

## <a name="service-fabric-in-azure"></a>Tecido de serviço em Azure
Um cluster de tecido de serviço em Azure é colocado atrás de um Balançador de Carga Azure. Todo o tráfego externo para o cluster deve passar pelo equilibrador de carga. O balançador de carga encaminha automaticamente o tráfego para uma determinada porta para um *nó* aleatório que tenha a mesma porta aberta. O Azure Load Balancer só sabe sobre portas abertas nos *nós,* desconhece portas abertas por *serviços*individuais.

![Topologia do equilibrador de carga e do tecido de serviço Azure][3]

Por exemplo, para aceitar o tráfego externo no porto **80,** as seguintes coisas devem ser configuradas:

1. Escreva um serviço que ouça na porta 80. Configure a porta 80 no ServiceManifest.xml do serviço e abra um ouvinte no serviço, por exemplo, um servidor web auto-hospedado.

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
2. Crie um Cluster de Tecido de Serviço em Azure e especifique a porta **80** como uma porta de ponto final personalizada para o tipo de nó que irá hospedar o serviço. Se tiver mais do que um tipo de nó, pode configurar uma *restrição de colocação* no serviço para garantir que funciona apenas no tipo de nó que tem a porta de ponto final personalizada aberta.

    ![Abra uma porta em um tipo de nó][4]
3. Uma vez criado o cluster, configuure o Balançador de Carga Azure no Grupo de Recursos do cluster para encaminhar o tráfego na porta 80. Ao criar um cluster através do portal Azure, este é configurado automaticamente para cada porta de ponto final personalizado que foi configurado.

    ![Screenshot que realça o campo porta Backend de acordo com as regras de equilíbrio de carga.][5]
4. O Balançador de Carga Azure utiliza uma sonda para determinar se envia ou não tráfego para um determinado nó. A sonda verifica periodicamente um ponto final em cada nó para determinar se o nó está ou não a responder. Se a sonda não receber uma resposta após um número configurado de vezes, o balançador de carga para de enviar o tráfego para esse nó. Ao criar um cluster através do portal Azure, uma sonda é configurada automaticamente para cada porta de ponto final personalizada que foi configurada.

    ![Tráfego para a frente no Balançador de Carga Azure][8]

É importante lembrar que o Azure Load Balancer e a sonda só sabem sobre os *nós*, e não os *serviços* que estão a funcionar nos nós. O Azure Load Balancer enviará sempre o tráfego para os nós que respondem à sonda, pelo que é necessário ter cuidado para garantir que os serviços estão disponíveis nos nós que são capazes de responder à sonda.

## <a name="reliable-services-built-in-communication-api-options"></a>Serviços fiáveis: Opções de API de comunicação incorporada
Os navios-quadro dos Serviços Fiáveis com várias opções de comunicação pré-construídas. A decisão sobre qual deles funcionará melhor para si depende da escolha do modelo de programação, do quadro de comunicação e da linguagem de programação em que os seus serviços estão escritos.

* **Sem protocolo específico:**  Se você não tem uma escolha particular de enquadramento de comunicação, mas você quer obter algo em funcionamento e funcionando rapidamente, então a opção ideal para você é [remoting de serviço](service-fabric-reliable-services-communication-remoting.md), que permite chamadas de procedimento remoto fortemente dactilografado para Serviços Fiáveis e Atores Confiáveis. Esta é a maneira mais fácil e rápida de começar com a comunicação de serviço. O remoing de serviço trata da resolução de endereços de serviço, ligação, revarramento e manuseamento de erros. Isto está disponível para aplicações C# e Java.
* **HTTP**: Para comunicação idioma-agnóstica, HTTP fornece uma escolha padrão da indústria com ferramentas e servidores HTTP disponíveis em muitos idiomas diferentes, todos suportados pelo Service Fabric. Os serviços podem utilizar qualquer stack HTTP disponível, incluindo [ASP.NET Web API](./service-fabric-reliable-services-communication-aspnetcore.md) para aplicações C#. Os clientes escritos em C# podem alavancar as `ICommunicationClient` classes e `ServicePartitionClient` as classes, enquanto para Java, utilizar as `CommunicationClient` e as `FabricServicePartitionClient` classes, para [resolução de serviços, ligações HTTP e loops de rejulinha](service-fabric-reliable-services-communication.md).
* **WCF**: Se tiver código existente que utilize o WCF como estrutura de comunicação, então pode utilizar o `WcfCommunicationListener` para o lado do servidor e e classes para o `WcfCommunicationClient` `ServicePartitionClient` cliente. No entanto, isto só está disponível para aplicações C# em clusters baseados no Windows. Para mais detalhes, consulte este artigo sobre [a implementação da pilha de comunicações baseada no WCF.](service-fabric-reliable-services-communication-wcf.md)

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Utilização de protocolos personalizados e outros quadros de comunicação
Os serviços podem usar qualquer protocolo ou enquadramento para comunicação, seja um protocolo binário habitual sobre tomadas TCP, ou eventos de streaming através de [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) ou [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). O Service Fabric fornece APIs de comunicação onde pode ligar a sua pilha de comunicação, enquanto todo o trabalho para descobrir e conectar é resumido de si. Consulte este artigo sobre o [modelo de comunicação Do Serviço Fiável](service-fabric-reliable-services-communication.md) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os conceitos e APIs disponíveis no [modelo de comunicação reliable Services,](service-fabric-reliable-services-communication.md)em seguida, começar rapidamente com [o remoing](service-fabric-reliable-services-communication-remoting.md) de serviço ou ir em profundidade para aprender a escrever um ouvinte de comunicação usando [API Web com auto-anfitrião OWIN](./service-fabric-reliable-services-communication-aspnetcore.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
