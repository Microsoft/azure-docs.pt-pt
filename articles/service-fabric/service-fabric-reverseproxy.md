---
title: Proxy de tecido de serviço Azure
description: Utilize o proxy inverso do Service Fabric para comunicação a microserviços de dentro e de fora do cluster.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: fd8e6dd712801de49971c1ef27cea664d73a4cb0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005916"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Procuração inversa no tecido de serviço Azure
O proxy invertido incorporado no Azure Service Fabric ajuda os microserviços a funcionar num cluster de Tecidos de Serviço a descobrir e comunicar com outros serviços que tenham pontos finais http.

## <a name="microservices-communication-model"></a>Modelo de comunicação de microserviços
Os microserviços em Tecido de Serviço funcionam num subconjunto de nós no cluster e podem migrar entre os nós por várias razões. Como resultado, os pontos finais para microserviços podem mudar dinamicamente. Para descobrir e comunicar com outros serviços no cluster, o microserviço deve passar pelos seguintes passos:

1. Resolva a localização do serviço através do serviço de nomeação.
2. Ligue-se ao serviço.
3. Embrulhe os passos anteriores num ciclo que implemente a resolução de serviços e relemisse as políticas para aplicar em falhas de ligação

Para mais informações, consulte [Connect e comunique com os serviços.](service-fabric-connect-and-communicate-with-services.md)

### <a name="communicating-by-using-the-reverse-proxy"></a>Comunicação usando o proxy invertido
Reverse proxy é um serviço que funciona em cada nó e lida com a resolução de ponto final, reagem automática e outras falhas de conexão em nome dos serviços do cliente. O proxy inverso pode ser configurado para aplicar várias políticas, uma vez que lida com pedidos de serviços ao cliente. A utilização de um proxy reverso permite ao serviço do cliente utilizar quaisquer bibliotecas de comunicação HTTP do lado do cliente e não requer uma lógica especial de resolução e de relemissão no serviço. 

O proxy reverso expõe um ou mais pontos finais no nó local para os serviços do cliente utilizarem para o envio de pedidos para outros serviços.

![Comunicação interna][1]

> [!NOTE]
> **Plataformas apoiadas**
>
> A procuração inversa no Service Fabric suporta atualmente as seguintes plataformas
> * *Windows Cluster*: Windows 8 e posteriormente ou Windows Server 2012 e mais tarde
> * *Linux Cluster*: Reverse Proxy não está disponível para clusters Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Alcançar microserviços de fora do cluster
O modelo de comunicação externa predefinido para microserviços é um modelo de opt-in onde cada serviço não pode ser acedido diretamente a partir de clientes externos. [O Azure Load Balancer](../load-balancer/load-balancer-overview.md), que é uma fronteira de rede entre microserviços e clientes externos, realiza tradução de endereço de rede e encaminha pedidos externos para pontos finais ip:port internos. Para tornar o ponto final de um microserviço diretamente acessível a clientes externos, é necessário primeiro configurar o Balancer de Carga para encaminhar o tráfego para cada porta que o serviço utiliza no cluster. Além disso, a maioria dos microserviços, especialmente microserviços estatais, não vivem em todos os nós do cluster. Os microserviços podem mover-se entre nós em falha. Nesses casos, o Balanceador de Carga não pode determinar eficazmente a localização do nó-alvo das réplicas para as quais deve encaminhar o tráfego.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Chegar a microserviços através do proxy invertido de fora do cluster
Em vez de configurar a porta de um serviço individual no Balanceador de Carga, pode configurar apenas a porta do representante inverso no Balanceador de Carga. Esta configuração permite que os clientes fora do cluster alcancem os serviços dentro do cluster utilizando o proxy inverso sem configuração adicional.

![Comunicação externa][0]

> [!WARNING]
> Quando configurar a porta do proxy invertido no Balanceador de Carga, todos os microserviços do cluster que expõem um ponto final HTTP são endereçais de fora do cluster. Isto significa que os microserviços destinados a serem internos podem ser detetáveis por um determinado utilizador malicioso. Isto apresenta potencialmente vulnerabilidades graves que podem ser exploradas; Por exemplo:
>
> * Um utilizador mal-intencionado pode lançar um ataque de negação de serviço chamando repetidamente um serviço interno que não tenha uma superfície de ataque suficientemente endurecida.
> * Um utilizador malicioso pode entregar pacotes mal formados a um serviço interno, resultando em comportamento não intencional.
> * Um serviço destinado a ser interno pode devolver informações privadas ou sensíveis que não se destinem a ser expostas a serviços fora do cluster, expondo assim esta informação sensível a um utilizador mal-intencionado. 
>
> Certifique-se de compreender e mitigar completamente as potenciais ramificações de segurança para o seu cluster e as aplicações em execução nele, antes de tornar público o porto de procuração inversa. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formato URI para endereçar serviços utilizando o proxy reverso
O representante inverso utiliza um formato específico de identificador de recursos uniformes (URI) para identificar a divisória de serviço à qual o pedido de entrada deve ser reencaminhado:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s):** O representante inverso pode ser configurado para aceitar o tráfego HTTP ou HTTPS. Para encaminhamento HTTPS, consulte [o Connect para um serviço seguro com o representante inverso](service-fabric-reverseproxy-configure-secure-communication.md) uma vez que tenha configuração de procuração inversa para ouvir em HTTPS.
* **Nome de domínio totalmente qualificado (FQDN) / IP interno:** Para clientes externos, pode configurar o representante inverso para que seja acessível através do domínio do cluster, como mycluster.eastus.cloudapp.azure.com. Por predefinição, o representante inverso funciona em cada nó. Para o tráfego interno, o representante inverso pode ser alcançado em local ou em qualquer nó IP interno, como 10.0.0.1.
* **Porto:** Este é o porto, como 19081, que foi especificado para o representante inverso.
* **Nome do ServiçoInstance:** Este é o nome totalmente qualificado da instância de serviço implantada que está a tentar alcançar sem o "tecido:/" esquema. Por exemplo, para chegar ao *tecido:/myapp/myservice/service,* utilizaria *o myapp/myservice*.

    O nome da instância de serviço é sensível a casos. A utilização de um invólucro diferente para o nome da instância de serviço no URL faz com que os pedidos falhem com 404 (Não Encontrados).
* **Caminho do sufixo:** Este é o caminho de URL real, como *myapi/valores/add/3,* para o serviço a que pretende ligar.
* **PartitionKey:** Para um serviço dividido, esta é a chave de partição computada da partição que você quer alcançar. Note que este *não* é o ID GUID de partição. Este parâmetro não é necessário para os serviços que utilizam o esquema de partição singleton.
* **PartitionKind:** Este é o esquema de divisão de serviço. Isto pode ser 'Int64Range' ou 'Nomeado'. Este parâmetro não é necessário para os serviços que utilizam o esquema de partição singleton.
* **Nome do ouvinte** Os pontos finais do serviço são do formulário {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}} . Quando o serviço expõe vários pontos finais, isto identifica o ponto final a que o pedido do cliente deve ser reencaminhado. Isto pode ser omitido se o serviço tiver apenas um ouvinte.
* **TargetReplicaSelector** Isto especifica como a réplica ou instância do alvo deve ser selecionada.
  * Quando o serviço alvo é imponente, o TargetReplicaSelector pode ser um dos seguintes: 'PrimaryReplica', 'RandomSecondaryReplica', ou 'RandomReplica'. Quando este parâmetro não é especificado, o padrão é 'PrimaryReplica'.
  * Quando o serviço de destino é apátrida, o representante invertido escolhe uma instância aleatória da partição de serviço para encaminhar o pedido para.
* **Tempo limite:**  Isto especifica o prazo para o pedido HTTP criado pelo representante inverso ao serviço em nome do pedido do cliente. O valor predefinido é de 120 segundos. Este é um parâmetro opcional.

### <a name="example-usage"></a>Utilização de exemplo
Como exemplo, vamos pegar no *tecido:/MyApp/MyService* que abre um ouvinte HTTP no seguinte URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Seguem-se os recursos para o serviço:

* `/index.html`
* `/api/users/<userId>`

Se o serviço utilizar o esquema de partição singleton, os parâmetros de cadeia de consulta *PartitionKey* e *PartitionKind* não são necessários, e o serviço pode ser alcançado utilizando o gateway como:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Internamente: `http://localhost:19081/MyApp/MyService`

Se o serviço utilizar o sistema uniforme de partição Int64, os parâmetros de cadeia de consulta *PartitionKey* e *PartitionKind* devem ser utilizados para alcançar uma partição do serviço:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para alcançar os recursos que o serviço expõe, basta colocar o caminho do recurso após o nome de serviço no URL:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

O gateway irá então encaminhar estes pedidos para a URL do serviço:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Tratamento especial para serviços de partilha portuária
O Service Fabric reverte as tentativas de proxy para resolver novamente um endereço de serviço e tentar novamente o pedido quando um serviço não pode ser alcançado. Geralmente, quando um serviço não pode ser alcançado, a instância de serviço ou réplica mudou-se para um nó diferente como parte do seu ciclo de vida normal. Quando isto acontece, o representante inverso pode receber um erro de ligação de rede indicando que um ponto final já não está aberto no endereço originalmente resolvido.

No entanto, réplicas ou instâncias de serviço podem partilhar um processo de anfitrião e também podem partilhar uma porta quando hospedado por um servidor web baseado em http.sys, incluindo:

* [System.net.httpListener](/dotnet/api/system.net.httplistener?view=netcore-3.1)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Rio Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Nesta situação, é provável que o servidor web esteja disponível no processo de anfitrião e responda a pedidos, mas a instância ou réplica de serviço resolvida já não está disponível no anfitrião. Neste caso, o gateway receberá uma resposta HTTP 404 a partir do servidor web. Assim, uma resposta HTTP 404 pode ter dois significados distintos:

- Caso #1: O endereço de serviço está correto, mas o recurso que o utilizador solicitou não existe.
- Caso #2: O endereço de serviço está incorreto e o recurso que o utilizador solicitou pode existir num nó diferente.

O primeiro caso é um HTTP 404 normal, que é considerado um erro do utilizador. No entanto, no segundo caso, o utilizador solicitou um recurso que existe. O representante inverso não conseguiu localizá-lo porque o próprio serviço se moveu. O representante inverso precisa de resolver o endereço novamente e voltar a tentar o pedido.

O representante inverso precisa, assim, de uma forma de distinguir entre estes dois casos. Para fazer esta distinção, é necessária uma dica do servidor.

* Por predefinição, o representante inverso assume que o caso #2 e tenta resolver e emitir novamente o pedido.
* Para indicar caso #1 ao representante inverso, o serviço deve devolver o seguinte cabeçalho de resposta HTTP:

  `X-ServiceFabric : ResourceNotFound`

Este cabeçalho de resposta HTTP indica uma situação normal http 404 em que o recurso solicitado não existe, e o representante inverso não tentará resolver novamente o endereço de serviço.

## <a name="special-handling-for-services-running-in-containers"></a>Tratamento especial dos serviços em funcionamento em contentores

Para serviços que executam dentro de contentores, pode utilizar a variável ambiente, `Fabric_NodeIPOrFQDN` para construir o URL de [procuração inversa](#uri-format-for-addressing-services-by-using-the-reverse-proxy) como no seguinte código:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Para o aglomerado local, `Fabric_NodeIPOrFQDN` está definido para "localhost" por padrão. Inicie o cluster local com o `-UseMachineName` parâmetro para garantir que os recipientes podem alcançar um representante inverso que funciona no nó. Para obter mais informações, consulte [configurar o seu ambiente de desenvolvimento para depurar recipientes.](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers)

Os serviços de tecido de serviço que funcionam dentro de recipientes Docker Compose requerem uma secção especial de estiva-compose.yml *Ports* http: ou https: configuração. Para mais informações, consulte [o suporte de implementação do Docker Compose no Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Passos seguintes
* [Configurar e configurar](service-fabric-reverseproxy-setup.md)o proxy inverso num cluster .
* [Configurar o encaminhamento para garantir o serviço HTTP com o proxy invertido](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnosticar eventos de proxy inverso](service-fabric-reverse-proxy-diagnostics.md)
* Veja um exemplo de comunicação HTTP entre serviços num [projeto de amostra no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chamadas de procedimento remoto com serviços fiáveis de remoing](service-fabric-reliable-services-communication-remoting.md)
* [API web que usa OWIN em Serviços Fiáveis](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Comunicação WCF utilizando serviços fiáveis](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
