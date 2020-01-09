---
title: Comunicação remota do serviço usando Java no Azure Service Fabric
description: Service Fabric comunicação remota permite que clientes e serviços se comuniquem com os serviços Java usando uma chamada de procedimento remoto.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: eef63d7a2c8a4b15938dfbffd7db5f9d1b22d426
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426639"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Comunicação remota do serviço em Java com Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java em Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Para serviços que não estão vinculados a um determinado protocolo de comunicação ou pilha, como WebAPI, Windows Communication Foundation (WCF) ou outros, a estrutura de Reliable Services fornece um mecanismo de comunicação remota para configurar de forma rápida e fácil as chamadas de procedimento remoto para serviços.  Este artigo discute como configurar chamadas de procedimento remoto para serviços escritos com Java.

## <a name="set-up-remoting-on-a-service"></a>Configurar a comunicação remota em um serviço
A configuração da comunicação remota para um serviço é feita em duas etapas simples:

1. Crie uma interface para o serviço implementar. Essa interface define os métodos que estão disponíveis para uma chamada de procedimento remoto em seu serviço. Os métodos devem ser métodos assíncronos que retornam tarefas. A interface deve implementar `microsoft.serviceFabric.services.remoting.Service` para sinalizar que o serviço tem uma interface de comunicação remota.
2. Use um ouvinte de comunicação remota em seu serviço. Essa é uma implementação `CommunicationListener` que fornece recursos de comunicação remota. `FabricTransportServiceRemotingListener` pode ser usado para criar um ouvinte de comunicação remota usando o protocolo de transporte remoto padrão.

Por exemplo, o serviço sem estado a seguir expõe um único método para obter "Olá, Mundo" sobre uma chamada de procedimento remoto.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Os argumentos e os tipos de retorno na interface de serviço podem ser de tipos simples, complexos ou personalizados, mas devem ser serializáveis.
>
>

## <a name="call-remote-service-methods"></a>Chamar métodos de serviço remoto
Chamar métodos em um serviço usando a pilha de comunicação remota é feito usando um proxy local para o serviço por meio da classe `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase`. O método `ServiceProxyBase` cria um proxy local usando a mesma interface implementada pelo serviço. Com esse proxy, você pode simplesmente chamar métodos na interface remotamente.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

A estrutura de comunicação remota propaga exceções lançadas no serviço para o cliente. Portanto, a lógica de tratamento de exceção no cliente usando `ServiceProxyBase` pode tratar diretamente as exceções que o serviço lança.

## <a name="service-proxy-lifetime"></a>Tempo de vida do proxy de serviço
A criação do outproxy é uma operação leve, para que você possa criar quantas forem necessárias. As instâncias de proxy de serviço podem ser reutilizadas desde que sejam necessárias. Se uma chamada de procedimento remoto lançar uma exceção, você ainda poderá reutilizar a mesma instância de proxy. Cada outproxy contém um cliente de comunicação usado para enviar mensagens pela conexão. Ao invocar chamadas remotas, verificações internas são executadas para determinar se o cliente de comunicação é válido. Com base nos resultados dessas verificações, o cliente de comunicação é recriado, se necessário. Portanto, se ocorrer uma exceção, você não precisará recriar `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>Tempo de vida de ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) é uma fábrica que cria um proxy para diferentes interfaces de comunicação remota. Se você usar `ServiceProxyBase.create` de API para criar o proxy, a estrutura criará uma `FabricServiceProxyFactory`.
É útil criar um manualmente quando você precisa substituir as propriedades [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) .
A fábrica é uma operação cara. `FabricServiceProxyFactory` mantém o cache de clientes de comunicação.
A prática recomendada é armazenar em cache `FabricServiceProxyFactory` por tanto tempo quanto possível.

## <a name="remoting-exception-handling"></a>Manipulação de exceção de comunicação remota
Todas as exceções remotas geradas pela API de serviço são enviadas de volta ao cliente como RuntimeException ou Fabricexception.

O Service-Proxy lida com toda exceção de failover para a partição de serviço para a qual ela foi criada. Ele resolverá novamente os pontos de extremidade se houver exceções de failover (exceções não transitórias) e tentará novamente a chamada com o ponto de extremidade correto. O número de repetições para a exceção de failover é indefinido.
No caso do TransientExceptions, ele apenas tenta a chamada novamente.

Os parâmetros de repetição padrão são fornecidos por [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Você pode configurar esses valores passando o objeto OperationRetrySettings para o Construtor ServiceProxyFactory.

## <a name="next-steps"></a>Passos seguintes
* [Protegendo a comunicação para Reliable Services](service-fabric-reliable-services-secure-communication-java.md)
