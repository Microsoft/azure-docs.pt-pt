---
title: Proxy reverso do Azure Service Fabric
description: Use o proxy reverso do Service Fabric para comunicação com os microserviços de dentro e fora do cluster.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 4fa4c6e46dd786b833087f892d995e85b5d2ea47
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464289"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Proxy reverso no Azure Service Fabric
O proxy reverso interno do Azure Service Fabric ajuda os microserviços em execução em uma descoberta de Cluster Service Fabric e se comunicam com outros serviços que têm pontos de extremidade http.

## <a name="microservices-communication-model"></a>Modelo de comunicação de microserviços
Os microserviços no Service Fabric são executados em um subconjunto de nós no cluster e podem migrar entre os nós por vários motivos. Como resultado, os pontos de extremidade para os microserviços podem ser alterados dinamicamente. Para descobrir e se comunicar com outros serviços no cluster, o Microservice deve passar pelas seguintes etapas:

1. Resolva o local do serviço por meio do serviço de cadastramento.
2. Conecte-se ao serviço.
3. Encapsule as etapas anteriores em um loop que implementa a resolução de serviço e as políticas de repetição para aplicar em falhas de conexão

Para obter mais informações, consulte [conectar-se e comunicar](service-fabric-connect-and-communicate-with-services.md)-se com os serviços.

### <a name="communicating-by-using-the-reverse-proxy"></a>Comunicando-se usando o proxy reverso
O proxy reverso é um serviço executado em cada nó e manipula a resolução do ponto de extremidade, repetição automática e outras falhas de conexão em nome dos serviços do cliente. O proxy reverso pode ser configurado para aplicar várias políticas, pois ele lida com solicitações dos serviços do cliente. O uso de um proxy reverso permite que o serviço do cliente use qualquer biblioteca de comunicação HTTP do lado do cliente e não exija resolução especial e lógica de repetição no serviço. 

O proxy reverso expõe um ou mais pontos de extremidade no nó local para que os serviços cliente usem para enviar solicitações para outros serviços.

![Comunicação interna][1]

> [!NOTE]
> **Plataformas com suporte**
>
> Proxy reverso no Service Fabric atualmente dá suporte às seguintes plataformas
> * *Cluster do Windows*: Windows 8 e posterior ou windows Server 2012 e posterior
> * *Cluster do Linux*: o proxy reverso não está disponível no momento para clusters do Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Atingir os microserviços de fora do cluster
O modelo de comunicação externa padrão para os microserviços é um modelo de aceitação em que cada serviço não pode ser acessado diretamente de clientes externos. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), que é um limite de rede entre os microserviço e clientes externos, o executa a conversão de endereços de rede e encaminha as solicitações externas para os pontos de extremidade de IP: porta internos. Para tornar o ponto de extremidade de um microserviço acessível diretamente para clientes externos, primeiro você deve configurar Load Balancer para encaminhar o tráfego para cada porta que o serviço usa no cluster. Além disso, a maioria dos microserviços, especialmente os microserviços com estado, não reside em todos os nós do cluster. Os microserviços podem se mover entre os nós no failover. Nesses casos, Load Balancer não pode efetivamente determinar o local do nó de destino das réplicas para o qual ele deve encaminhar o tráfego.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Atingir os microserviços por meio do proxy reverso de fora do cluster
Em vez de configurar a porta de um serviço individual no Load Balancer, você pode configurar apenas a porta do proxy reverso em Load Balancer. Essa configuração permite que clientes fora do cluster alcancem serviços dentro do cluster usando o proxy reverso sem configuração adicional.

![Comunicação externa][0]

> [!WARNING]
> Quando você configura a porta do proxy reverso no Load Balancer, todos os microserviços no cluster que expõem um ponto de extremidade HTTP são endereçáveis de fora do cluster. Isso significa que os microserviços destinados a serem internos podem ser detectáveis por um determinado usuário mal-intencionado. Isso potencialmente apresenta vulnerabilidades sérias que podem ser exploradas; por exemplo:
>
> * Um usuário mal-intencionado pode iniciar um ataque de negação de serviço chamando repetidamente um serviço interno que não tem uma superfície de ataque suficientemente protegida.
> * Um usuário mal-intencionado pode entregar pacotes malformados a um serviço interno, resultando em um comportamento indesejado.
> * Um serviço destinado a ser interno pode retornar informações privadas ou confidenciais que não devem ser expostas a serviços fora do cluster, expondo assim essas informações confidenciais a um usuário mal-intencionado. 
>
> Certifique-se de compreender e atenuar totalmente as possíveis ramificações de segurança para o cluster e os aplicativos em execução, antes de tornar a porta de proxy inversa pública. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formato de URI para endereçamento de serviços usando o proxy reverso
O proxy reverso usa um formato de URI (Uniform Resource Identifier) específico para identificar a partição de serviço à qual a solicitação de entrada deve ser encaminhada:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** O proxy reverso pode ser configurado para aceitar o tráfego HTTP ou HTTPS. Para o encaminhamento de HTTPS, consulte [conectar-se a um serviço seguro com o proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md) depois que você tiver a configuração de proxy reverso para escutar em https.
* **FQDN (nome de domínio totalmente qualificado) do cluster | IP interno:** Para clientes externos, você pode configurar o proxy reverso para que ele possa ser acessado por meio do domínio de cluster, como mycluster.eastus.cloudapp.azure.com. Por padrão, o proxy reverso é executado em cada nó. Para o tráfego interno, o proxy reverso pode ser alcançado no localhost ou em qualquer IP de nó interno, como 10.0.0.1.
* **Porta:** Essa é a porta, como 19081, que foi especificada para o proxy reverso.
* **ServiceInstance:** Este é o nome totalmente qualificado da instância de serviço implantada que você está tentando acessar sem a "Fabric:/" esquema. Por exemplo, para acessar a *malha:/MyApp/MyService/* Service, você usaria *MyApp/MyService*.

    O nome da instância de serviço diferencia maiúsculas de minúsculas. Usar uma capitalização diferente para o nome da instância de serviço na URL faz com que as solicitações falhem com 404 (não encontrado).
* **Caminho do sufixo:** Esse é o caminho de URL real, como *myapi/Values/Add/3*, para o serviço ao qual você deseja se conectar.
* **PartitionKey:** Para um serviço particionado, essa é a chave de partição computada da partição que você deseja alcançar. Observe que esse *não* é o GUID da ID de partição. Esse parâmetro não é necessário para os serviços que usam o esquema de partição singleton.
* **PartitionKind:** Este é o esquema de partição de serviço. Pode ser ' Int64Range ' ou ' named '. Esse parâmetro não é necessário para os serviços que usam o esquema de partição singleton.
* **Listenername** Os pontos de extremidade do serviço estão no formato {"pontos de extremidade": {"Listener1": "Endpoint1", "Listener2": "endpoint2"...}}. Quando o serviço expõe vários pontos de extremidade, isso identifica o ponto de extremidades para o qual a solicitação do cliente deve ser encaminhada. Isso pode ser omitido se o serviço tiver apenas um ouvinte.
* **TargetReplicaSelector** Isso especifica como a réplica ou a instância de destino deve ser selecionada.
  * Quando o serviço de destino tem estado, o TargetReplicaSelector pode ser um dos seguintes: ' PrimaryReplica ', ' RandomSecondaryReplica ' ou ' RandomReplica '. Quando esse parâmetro não é especificado, o padrão é ' PrimaryReplica '.
  * Quando o serviço de destino estiver sem estado, o proxy reverso escolherá uma instância aleatória da partição de serviço para a qual encaminhar a solicitação.
* **Tempo limite:**  Isso especifica o tempo limite para a solicitação HTTP criada pelo proxy reverso para o serviço em nome da solicitação do cliente. O valor padrão é 60 segundos. Esse é um parâmetro opcional.

### <a name="example-usage"></a>Utilização de exemplo
Como exemplo, vamos pegar o serviço *Fabric:/MyApp/MyService* que abre um ouvinte http na seguinte URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

A seguir estão os recursos para o serviço:

* `/index.html`
* `/api/users/<userId>`

Se o serviço usar o esquema de particionamento singleton, os parâmetros de cadeia de caracteres de consulta *PartitionKey* e *PartitionKind* não serão necessários e o serviço poderá ser acessado usando o gateway como:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Internamente: `http://localhost:19081/MyApp/MyService`

Se o serviço usar o esquema de particionamento Int64 uniforme, os parâmetros de cadeia de caracteres de consulta *PartitionKey* e *PartitionKind* deverão ser usados para acessar uma partição do serviço:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para alcançar os recursos que o serviço expõe, basta posicionar o caminho do recurso após o nome do serviço na URL:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Em seguida, o gateway encaminhará essas solicitações para a URL do serviço:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Tratamento especial para serviços de compartilhamento de porta
O Service Fabric proxy reverso tenta resolver um endereço de serviço novamente e repetir a solicitação quando um serviço não puder ser acessado. Geralmente, quando um serviço não pode ser alcançado, a instância ou a réplica de serviço foi movida para um nó diferente como parte de seu ciclo de vida normal. Quando isso acontece, o proxy reverso pode receber um erro de conexão de rede indicando que um ponto de extremidade não está mais aberto no endereço resolvido originalmente.

No entanto, réplicas ou instâncias de serviço podem compartilhar um processo de host e também podem compartilhar uma porta quando hospedada por um servidor Web baseado em http. sys, incluindo:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Nessa situação, é provável que o servidor Web esteja disponível no processo de host e respondendo a solicitações, mas a instância de serviço ou réplica resolvida não está mais disponível no host. Nesse caso, o gateway receberá uma resposta HTTP 404 do servidor Web. Portanto, uma resposta HTTP 404 pode ter dois significados distintos:

- #1 de caso: o endereço do serviço está correto, mas o recurso solicitado pelo usuário não existe.
- #2 de caso: o endereço do serviço está incorreto e o recurso que o usuário solicitou pode existir em um nó diferente.

O primeiro caso é um HTTP 404 normal, que é considerado um erro de usuário. No entanto, no segundo caso, o usuário solicitou um recurso que existe. O proxy reverso não pôde localizá-lo porque o próprio serviço foi movido. O proxy reverso precisa resolver o endereço novamente e repetir a solicitação.

O proxy reverso, portanto, precisa de uma maneira de distinguir entre esses dois casos. Para fazer essa distinção, uma dica do servidor é necessária.

* Por padrão, o proxy reverso assume o caso #2 e tenta resolver e emitir a solicitação novamente.
* Para indicar o caso #1 para o proxy reverso, o serviço deve retornar o seguinte cabeçalho de resposta HTTP:

  `X-ServiceFabric : ResourceNotFound`

Esse cabeçalho de resposta HTTP indica uma situação de HTTP 404 normal na qual o recurso solicitado não existe e o proxy reverso não tentará resolver o endereço do serviço novamente.

## <a name="special-handling-for-services-running-in-containers"></a>Tratamento especial para serviços em execução em contêineres

Para serviços em execução dentro de contêineres, você pode usar a variável de ambiente, `Fabric_NodeIPOrFQDN` para construir a [URL de proxy reverso](#uri-format-for-addressing-services-by-using-the-reverse-proxy) como no código a seguir:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Para o cluster local, `Fabric_NodeIPOrFQDN` é definido como "localhost" por padrão. Inicie o cluster local com o parâmetro `-UseMachineName` para certificar-se de que os contêineres podem acessar o proxy reverso em execução no nó. Para obter mais informações, consulte [configurar seu ambiente de desenvolvedor para depurar contêineres](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Service Fabric serviços que são executados em contêineres de Docker Compose exigem uma *seção de portas* Docker-Compose. yml especial http: ou https: Configuration. Para obter mais informações, consulte [suporte à implantação Docker Compose no Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Passos seguintes
* [Configure e configure o proxy reverso em um cluster](service-fabric-reverseproxy-setup.md).
* [Configurar o encaminhamento para o serviço HTTP seguro com o proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnosticar eventos de proxy reverso](service-fabric-reverse-proxy-diagnostics.md)
* Consulte um exemplo de comunicação HTTP entre serviços em um [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chamadas de procedimento remoto com Reliable Services comunicação remota](service-fabric-reliable-services-communication-remoting.md)
* [API Web que usa OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação do WCF usando o Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
