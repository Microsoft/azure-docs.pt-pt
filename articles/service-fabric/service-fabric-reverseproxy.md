---
title: O Azure Service Fabric de proxy inverso | Documentos da Microsoft
description: Utilize o proxy inverso do Service Fabric para a comunicação para os microsserviços de dentro e fora do cluster.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 6ce6f1f6559b43a64fb7edd0773a20f8ee0cf8a3
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661926"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Proxy inverso no Azure Service Fabric
Proxy inverso integrado do Azure Service Fabric ajuda a microsserviços em execução no cluster do Service Fabric detetar e comunicar com outros serviços que têm pontos finais de http.

## <a name="microservices-communication-model"></a>Modelo de comunicação de Microsserviços
Microsserviços no Service Fabric execute num subconjunto de nós do cluster e podem migrar entre os nós por vários motivos. Como resultado, podem alterar dinamicamente os pontos finais para microsserviços. Para detetar e comunicar com outros serviços do cluster, os microsserviços precisa passar os seguintes passos:

1. Resolva a localização de serviço através do serviço de nomenclatura.
2. Liga ao serviço.
3. Encapsular os passos anteriores num loop que implementa a resolução de serviço e tente novamente as políticas para aplicar em falhas de ligação

Para obter mais informações, consulte [Connect e comunicar com serviços](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Comunicar utilizando o proxy inverso
Proxy inverso é um serviço que é executado em cada nó e lida com a resolução do ponto de extremidade, tentativas automáticas e outras falhas de ligação em nome dos serviços de cliente. Proxy inverso pode ser configurado para aplicar várias políticas, como ele lida com pedidos de serviços do cliente. Utilizar um proxy inverso, permite que o serviço de cliente utilizar quaisquer bibliotecas de comunicação do lado do cliente HTTP e não requerem resolução especial e repetir a lógica no serviço. 

Proxy inverso expõe um ou mais pontos finais no nó local para serviços de cliente a utilizar para enviar pedidos para outros serviços.

![Comunicação interna][1]

> [!NOTE]
> **Plataformas suportadas**
>
> Atualmente, o proxy inverso no Service Fabric suporta as seguintes plataformas
> * *Cluster do Windows*: Windows 8 e posterior ou Windows Server 2012 e versões posterior
> * *Cluster do Linux*: Proxy inverso não está atualmente disponível para clusters do Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Atingir microsserviços de fora do cluster
O modelo de comunicação externa padrão para microsserviços é um modelo de participação ativa em que cada serviço não é possível aceder diretamente a partir de clientes externos. [O Azure Load Balancer](../load-balancer/load-balancer-overview.md), que é um limite de rede entre os microsserviços e clientes externos, executa a tradução de endereços de rede e encaminha solicitações externas para pontos de extremidade IP: porta interna. Para tornar o ponto final de um microsserviço diretamente acessível aos clientes externos, primeiro tem de configurar o Balanceador de carga para encaminhar o tráfego para cada porta que o serviço utiliza no cluster. Além disso, a maioria dos microsserviços, especialmente os microsserviços com estado, não vivem em todos os nós do cluster. Os microsserviços podem se mover entre nós numa ativação pós-falha. Nesses casos, o Balanceador de carga não consegue determinar com eficiência a localização do nó de destino das réplicas para que encaminhe tráfego.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Atingir microsserviços através do proxy inverso de fora do cluster
Em vez de configurar a porta de um serviço individual no balanceador de carga, pode configurar apenas a porta do proxy reverso no balanceador de carga. Esta configuração permite que os clientes fora do cluster a alcançar serviços dentro do cluster utilizando o proxy inverso sem configuração adicional.

![Comunicação externa][0]

> [!WARNING]
> Quando configura a porta do proxy inverso no balanceador de carga, todos os microsserviços no cluster que expõem um ponto de extremidade HTTP são endereçáveis a partir de fora do cluster. Isso significa que os microsserviços devem ser interno podem ser Detetáveis por um determinado utilizador mal intencionado. Isso apresenta potencialmente sérias vulnerabilidades que podem ser exploradas; Por exemplo:
>
> * Um utilizador mal intencionado pode iniciar um ataque de negação de serviço chamando um serviço interno que não tenha uma superfície de ataque suficientemente fortalecidos repetidamente.
> * Um utilizador mal intencionado pode enviar pacotes com formato incorreto para um serviço interno, resultando em comportamentos indesejados.
> * Um serviço devem ser interno pode retornar informações confidenciais ou privadas, que não se destina a ser exposto aos serviços fora do cluster, expondo, portanto, essas informações confidenciais para um utilizador mal intencionado. 
>
> Certifique-se totalmente entender e atenuar as possíveis ramificações de segurança do cluster e as aplicações em execução, antes de fazer a porta de proxy inverso pública. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formato URI para abordar os serviços ao utilizar o proxy inverso
O proxy inverso usa um formato de identifier (URI) de recurso uniforme específico para identificar a partição de serviço ao qual a solicitação de entrada deve ser reencaminhada:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s):** O proxy inverso pode ser configurado para aceitar o tráfego HTTP ou HTTPS. Para o encaminhamento de HTTPS, consulte [ligar a um serviço seguro com o proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md) assim que tiver a configuração de proxy inverso para escutar o HTTPS.
* **Nome de domínio completamente qualificado (FQDN) do cluster | interní IP adresa:** Para clientes externos, pode configurar o proxy inverso para que seja acessível por meio do domínio do cluster, como mycluster.eastus.cloudapp.azure.com. Por predefinição, o proxy inverso é executado em cada nó. Para o tráfego interno, o proxy inverso pode ser contatado em localhost ou em qualquer IP interno do nó, como 10.0.0.1.
* **Porta:** Esta é a porta, de como 19081, que foi especificada para o proxy inverso.
* **ServiceInstanceName:** Este é o nome completamente qualificado da instância do serviço implementado que está a tentar acessar sem o "fabric: /" esquema. Por exemplo, para aceder a *fabric: / myapp/myservice/* serviço, usaria *myapp/meuserviço*.

    O nome da instância de serviço diferencia maiúsculas de minúsculas. Utilizar uma maiúsculas/minúsculas diferentes para o nome de instância de serviço no URL faz com que os pedidos a falhar com 404 (não encontrado).
* **Caminho de sufixo:** Este é o caminho de URL real, como *myapi/valores/Adicionar/3*, para o serviço que deseja se conectar.
* **PartitionKey:** Para um serviço particionado, esta é a chave de partição calculada da partição que pretende aceder. Tenha em atenção que se trata *não* a GUID de ID de partição. Este parâmetro não é necessário para serviços que utilizam o esquema de partição singleton.
* **PartitionKind:** Este é o esquema de partição de serviço. Isso pode ser 'Int64Range' ou "Com o nome". Este parâmetro não é necessário para serviços que utilizam o esquema de partição singleton.
* **ListenerName** os pontos de extremidade do serviço têm o formato {"Pontos finais": {"Listener1": "Endpoint1", "Listener2": "Endpoint2"...}}. Quando o serviço expõe vários pontos de extremidade, identifica o ponto final que o pedido de cliente deve ser reencaminhado. Isso pode ser omitido se o serviço tiver apenas um serviço de escuta.
* **TargetReplicaSelector** esta ação Especifica como a réplica de destino ou a instância deve ser selecionada.
  * Quando o serviço de destino está com monitoração de estado, o TargetReplicaSelector pode ser um dos seguintes:  'PrimaryReplica', 'RandomSecondaryReplica' ou 'RandomReplica'. Se este parâmetro não for especificado, a predefinição é 'PrimaryReplica'.
  * Quando o serviço de destino está sem monitoração de estado, o proxy inverso escolhe uma instância aleatória da partição para encaminhar a solicitação ao serviço.
* **Tempo limite:**  Esta ação Especifica o tempo limite do pedido HTTP criado pelo proxy inverso para o serviço em nome do pedido do cliente. O valor predefinido é de 60 segundos. Este é um parâmetro opcional.

### <a name="example-usage"></a>Exemplo de utilização
Por exemplo, vamos dar a *fabric: / MyApp/Meuserviço* serviço que abre um serviço de escuta HTTP no seguinte URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Seguem-se os recursos para o serviço:

* `/index.html`
* `/api/users/<userId>`

Se o serviço utiliza o singleton a criação de partições de esquema, o *PartitionKey* e *PartitionKind* parâmetros de cadeia de caracteres de consulta não são necessários, e o serviço pode ser contatado através do gateway como:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Internamente: `http://localhost:19081/MyApp/MyService`

Se o serviço usa o esquema de particionamento de Int64 uniforme, o *PartitionKey* e *PartitionKind* parâmetros de cadeia de caracteres de consulta tem de ser utilizados para chegar a uma partição do serviço:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para aceder os recursos que o serviço expõe, basta coloca o caminho de recurso após o nome do serviço no URL:

* Externamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

O gateway, em seguida, irá reencaminhar estes pedidos para o URL do serviço:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Um tratamento especial para o compartilhamento de porta de serviços
O proxy inverso do Service Fabric tenta resolver um endereço de serviço novamente e repita o pedido quando não é possível aceder um serviço. Em geral, quando não é possível aceder um serviço, a instância de serviço ou a réplica foi movido para um nó diferente como parte do seu ciclo de vida normal. Quando isto acontecer, o proxy inverso poderá receber um erro de ligação de rede que indica que um ponto final já não está aberto no endereço resolvido originalmente.

No entanto, as réplicas ou instâncias de serviço pode partilhar um processo de host e também pode partilhar uma porta quando alojadas por um servidor web baseado em http. sys, incluindo:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [WebListener do ASP.NET Core](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Nesta situação, é provável que o servidor web está disponível no processo do host e a responder a pedidos, mas a instância de serviço resolvidos ou a réplica já não está disponível no anfitrião. Neste caso, o gateway irá receber uma resposta HTTP 404 do servidor web. Portanto, uma resposta HTTP 404 pode ter dois significados diferentes:

- Caso #1: O endereço do serviço está correto, mas o recurso que o usuário solicitou não existe.
- Caso #2: O endereço do serviço está incorreto, e o recurso que o usuário solicitou pode existir num nó diferente.

O primeiro caso é um HTTP 404 normal, que é considerado um erro de utilizador. No entanto, no segundo caso, o utilizador solicitou um recurso que existem. O proxy inverso não foi possível localizá-la porque o próprio serviço foi movido. O proxy inverso tem de resolver o endereço novamente e repita o pedido.

O proxy inverso, portanto, precisa de uma maneira para distinguir entre esses dois casos. Para fazer essa distinção, uma sugestão do servidor é necessária.

* Por predefinição, o proxy inverso pressupõe caso #2 e tenta resolver e emitir o pedido novamente.
* Para indicar caso #1 para o proxy inverso, o serviço deve retornar o seguinte cabeçalho de resposta HTTP:

  `X-ServiceFabric : ResourceNotFound`

Este cabeçalho de resposta HTTP indica uma situação de HTTP 404 normal em que o recurso solicitado não existe e o proxy inverso não irá tentar resolver o endereço do serviço novamente.

## <a name="special-handling-for-services-running-in-containers"></a>Tratamento especial para serviços em execução em contentores

Para serviços em execução no interior de contentores, pode utilizar a variável de ambiente `Fabric_NodeIPOrFQDN` construir o [inverter o URL de proxy](#uri-format-for-addressing-services-by-using-the-reverse-proxy) como no seguinte código:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Para o cluster local, `Fabric_NodeIPOrFQDN` está definido como "localhost" por predefinição. Inicie o cluster local com o `-UseMachineName` parâmetro para se certificar de que os contentores podem entrar em execução no nó de proxy inverso. Para obter mais informações, consulte [configurar o seu ambiente de desenvolvimento para depurar contentores](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Serviços do Service Fabric que são executados dentro de contentores do Docker Compose requerem um especial docker-Compose *portas secção* http: ou https: configuração. Para obter mais informações, consulte [suporte de implementação do Docker Compose no Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Passos Seguintes
* [Definir e configurar o proxy inverso num cluster](service-fabric-reverseproxy-setup.md).
* [Configurar o encaminhamento para o serviço HTTP seguro com o proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnosticar a eventos de proxy inverso](service-fabric-reverse-proxy-diagnostics.md)
* Ver um exemplo de comunicação HTTP entre os serviços numa [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chamadas de procedimento remoto com a comunicação remota do Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que utiliza o OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação do WCF com o Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
