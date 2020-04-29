---
title: Procuração inversa do Tecido de Serviço Azure
description: Utilize o proxy inverso da Service Fabric para a comunicação a microserviços de dentro e de fora do cluster.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 4fa4c6e46dd786b833087f892d995e85b5d2ea47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282227"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Procuração inversa no Tecido de Serviço Azure
A procuração inversa incorporada no Tecido de Serviço Azure ajuda os microserviços a funcionar num cluster de Tecidode Serviço a descobrir e a comunicar com outros serviços que têm pontos finais em http.

## <a name="microservices-communication-model"></a>Modelo de comunicação de microserviços
Os microserviços em Tecido de Serviço funcionam num subconjunto de nós no cluster e podem migrar entre os nós por várias razões. Como resultado, os pontos finais dos microserviços podem mudar dinamicamente. Para descobrir e comunicar com outros serviços do cluster, o microserviço deve passar pelos seguintes passos:

1. Resolva a localização do serviço através do serviço de nomeação.
2. Ligue-se ao serviço.
3. Envolva os passos anteriores num ciclo que implemente políticas de resolução de serviços e de novatentativa a aplicar em falhas de ligação

Para mais informações, consulte [Connect e comunique com os serviços.](service-fabric-connect-and-communicate-with-services.md)

### <a name="communicating-by-using-the-reverse-proxy"></a>Comunicação utilizando o proxy inverso
O proxy inverso é um serviço que funciona em cada nó e lida com resolução de ponto final, retry automático e outras falhas de conexão em nome dos serviços do cliente. O proxy inverso pode ser configurado para aplicar várias políticas, uma vez que lida com pedidos de serviços de clientes. A utilização de um proxy inverso permite ao serviço do cliente utilizar quaisquer bibliotecas de comunicação http do lado do cliente e não requer uma lógica especial de resolução e retry no serviço. 

O proxy inverso expõe um ou mais pontos finais no nó local para os serviços do cliente usarem para o envio de pedidos para outros serviços.

![Comunicação interna][1]

> [!NOTE]
> **Plataformas Suportadas**
>
> Procuração inversa no Tecido de Serviço suporta atualmente as seguintes plataformas
> * *Windows Cluster*: Windows 8 e mais tarde ou Windows Server 2012 e mais tarde
> * *Linux Cluster*: O Proxy inverso não está disponível para clusters Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Alcançar microserviços de fora do cluster
O modelo de comunicação externa padrão para microserviços é um modelo de opt-in onde cada serviço não pode ser acedido diretamente a partir de clientes externos. O [Azure Load Balancer](../load-balancer/load-balancer-overview.md), que é um limite de rede entre microserviços e clientes externos, realiza a tradução de endereços de rede e reencaminha pedidos externos para pontos finais internos ip:porta. Para tornar o ponto final de um microserviço diretamente acessível a clientes externos, tem primeiro de configurar o Balancer de Carga para encaminhar o tráfego para cada porta que o serviço utiliza no cluster. Além disso, a maioria dos microserviços, especialmente microserviços audais, não vivem em todos os nós do cluster. Os microserviços podem mover-se entre nós em falha. Nesses casos, o Balancer load não pode determinar eficazmente a localização do nó-alvo das réplicas às quais deve encaminhar o tráfego.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Chegar a microserviços através do proxy inverso de fora do cluster
Em vez de configurar a porta de um serviço individual no Balancer load, pode configurar apenas a porta do proxy inverso no Balancer de Carga. Esta configuração permite que os clientes fora do cluster alcancem serviços dentro do cluster utilizando o proxy inverso sem configuração adicional.

![Comunicação externa][0]

> [!WARNING]
> Quando configurar a porta do proxy inverso no Balancer de Carga, todos os microserviços do cluster que expõem um ponto final HTTP são endereçados de fora do cluster. Isto significa que os microserviços destinados a serem internos podem ser detetáveis por um determinado utilizador malicioso. Isto potencialmente apresenta vulnerabilidades graves que podem ser exploradas; Por exemplo:
>
> * Um utilizador malicioso pode lançar um ataque de negação de serviço, chamando repetidamente um serviço interno que não tenha uma superfície de ataque suficientemente endurecida.
> * Um utilizador malicioso pode entregar pacotes mal formados a um serviço interno, resultando em comportamentos não intencionais.
> * Um serviço destinado a ser interno pode devolver informações privadas ou sensíveis não destinadas a serem expostas a serviços fora do cluster, expondo assim esta informação sensível a um utilizador mal-intencionado. 
>
> Certifique-se de que compreende e atenua as potenciais ramificações de segurança para o seu cluster e as aplicações que estão a decorrer nele, antes de tornar público o porto de procuração inversa. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formato URI para endereçar serviços utilizando o proxy inverso
O representante inverso utiliza um formato específico de identificador uniforme de recursos (URI) para identificar a partição do serviço para a qual o pedido de entrada deve ser reencaminhado:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s):** O proxy inverso pode ser configurado para aceitar o tráfego HTTP ou HTTPS. Para reencaminhamento HTTPS, consulte [o Connect para um serviço seguro com o proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md) uma vez que tenha configuração de procuração inversa para ouvir em HTTPS.
* Nome de **domínio totalmente qualificado (FQDN) [ IP interno):** Para clientes externos, pode configurar o proxy inverso para que seja acessível através do domínio do cluster, como mycluster.eastus.cloudapp.azure.com. Por padrão, o proxy invertido corre em cada nó. Para o tráfego interno, o proxy inverso pode ser alcançado em local de acolhimento ou em qualquer nó interno IP, como 10.0.0.1.
* **Porto:** Este é o porto, como 19081, que foi especificado para o proxy inverso.
* **Nome de caso de serviço:** Este é o nome totalmente qualificado da instância de serviço implantada que está a tentar alcançar sem o "tecido:/" esquema. Por exemplo, para chegar ao *tecido:/myapp/myservice/* serviço, você usaria *myapp/myservice*.

    O nome da instância de serviço é sensível a casos. A utilização de um invólucro diferente para o nome da instância de serviço no URL faz com que os pedidos falhem com 404 (Não Encontrados).
* Caminho do **sufixo:** Este é o verdadeiro caminho de URL, como *myapi/values/add/3,* para o serviço a que pretende ligar.
* **PartitionKey:** Para um serviço dividido, esta é a chave de partição computorizada da partição que você quer alcançar. Note que este *não* é o ID GUID da partição. Este parâmetro não é necessário para serviços que utilizem o regime de partição singleton.
* **Partilhakind:** Este é o esquema de partição de serviços. Isto pode ser 'Int64Range' ou 'Nomeado'. Este parâmetro não é necessário para serviços que utilizem o regime de partição singleton.
* **Nome do ouvinte** Os pontos finais do serviço são do formulário {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2"...}}} Quando o serviço expõe vários pontos finais, isto identifica o ponto final a que o pedido do cliente deve ser encaminhado. Isto pode ser omitido se o serviço tiver apenas um ouvinte.
* **TargetReplicaSelector** Isto especifica como a réplica ou instância do alvo deve ser selecionada.
  * Quando o serviço alvo é imponente, o TargetReplicaSelector pode ser um dos seguintes: 'PrimaryReplica', 'RandomSecondaryReplica', ou 'RandomReplica'. Quando este parâmetro não é especificado, o padrão é 'PrimaryReplica'.
  * Quando o serviço alvo é apátrida, o proxy inverso escolhe uma instância aleatória da partição do serviço para reencaminhar o pedido para.
* **Intervalo:**  Isto especifica o prazo limite para o pedido HTTP criado pelo representante inverso ao serviço em nome do pedido do cliente. O valor padrão é de 60 segundos. Este é um parâmetro opcional.

### <a name="example-usage"></a>Utilização de exemplo
Como exemplo, vamos pegar no *serviço de tecido:/MyApp/MyService* que abre um ouvinte HTTP no seguinte URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Seguem-se os recursos para o serviço:

* `/index.html`
* `/api/users/<userId>`

Se o serviço utilizar o regime de parto singleton, os parâmetros de cordas de consulta *PartitionKey* e *PartitionKind* não são necessários, e o serviço pode ser alcançado utilizando o gateway como:

* Externamente:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Internamente:`http://localhost:19081/MyApp/MyService`

Se o serviço utilizar o regime de partição Uniforme Int64, os parâmetros de corda de consulta *PartitionKey* e *PartitionKind* devem ser utilizados para alcançar uma partição do serviço:

* Externamente:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internamente:`http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para alcançar os recursos que o serviço expõe, basta colocar o caminho de recursos após o nome de serviço no URL:

* Externamente:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internamente:`http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

O portal irá então encaminhar estes pedidos para o URL do serviço:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Manuseamento especial para serviços de partilha de portos
O Serviço Fabric inverte as tentativas de procuração para resolver novamente um endereço de serviço e voltar a tentar o pedido quando um serviço não pode ser alcançado. Geralmente, quando um serviço não pode ser alcançado, a instância de serviço ou réplica passou para um nó diferente como parte do seu ciclo de vida normal. Quando isto acontece, o proxy inverso pode receber um erro de ligação de rede indicando que um ponto final já não está aberto no endereço originalmente resolvido.

No entanto, réplicas ou instâncias de serviço podem partilhar um processo de anfitrião e também podem partilhar uma porta quando hospedadas por um servidor web baseado em http.sys, incluindo:

* [System.Net.httpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Nesta situação, é provável que o servidor web esteja disponível no processo de hospedada e respondendo a pedidos, mas a instância de serviço ou réplica resolvida já não está disponível no anfitrião. Neste caso, o portal receberá uma resposta HTTP 404 do servidor web. Assim, uma resposta HTTP 404 pode ter dois significados distintos:

- Caso #1: O endereço de serviço está correto, mas o recurso que o utilizador solicitou não existe.
- Caso #2: O endereço de serviço está incorreto e o recurso solicitado pelo utilizador pode existir num nó diferente.

O primeiro caso é um HTTP 404 normal, que é considerado um erro do utilizador. No entanto, no segundo caso, o utilizador solicitou um recurso que existe. O representante inverso não conseguiu localizá-lo porque o próprio serviço se moveu. O representante inverso precisa de resolver o endereço novamente e de voltar a tentar o pedido.

O representante inverso precisa, assim, de uma forma de distinguir estes dois casos. Para fazer esta distinção, é necessária uma dica do servidor.

* Por padrão, o representante inverso assume caso #2 e tenta resolver e emitir o pedido novamente.
* Para indicar o caso #1 ao proxy inverso, o serviço deve devolver o seguinte cabeçalho de resposta HTTP:

  `X-ServiceFabric : ResourceNotFound`

Este cabeçalho de resposta HTTP indica uma situação normal http 404 em que o recurso solicitado não existe, e o representante inverso não tentará resolver novamente o endereço de serviço.

## <a name="special-handling-for-services-running-in-containers"></a>Manuseamento especial para serviços em funcionamento em contentores

Para serviços que estão dentro de `Fabric_NodeIPOrFQDN` contentores, pode utilizar a variável ambiental, para construir o URL de [procuração inversa](#uri-format-for-addressing-services-by-using-the-reverse-proxy) como no seguinte código:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Para o cluster `Fabric_NodeIPOrFQDN` local, está definido para "localhost" por padrão. Inicie o aglomerado `-UseMachineName` local com o parâmetro para se certificar de que os recipientes podem chegar ao proxy inverso que corre no nó. Para mais informações, consulte Configure o seu ambiente de [desenvolvimento para depurar recipientes](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Serviço Os serviços de tecido que funcionam dentro dos contentores Docker Compose requerem uma secção especial de docker-compose.yml *Ports* http: ou https: configuração. Para mais informações, consulte o suporte de [implantação do Docker Compose no Tecido de Serviço Azure](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Passos seguintes
* [Configurar e configurar procuração inversa num cluster](service-fabric-reverseproxy-setup.md).
* [Configurar o encaminhamento para assegurar o serviço HTTP com o proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnosticar eventos de proxy inverso](service-fabric-reverse-proxy-diagnostics.md)
* Veja um exemplo de comunicação http entre serviços num [projeto de amostra no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chamadas de procedimento remoto com serviços fiáveis remoting](service-fabric-reliable-services-communication-remoting.md)
* [Web API que usa OWIN em Serviços Fiáveis](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação WCF utilizando Serviços Fiáveis](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
