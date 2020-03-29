---
title: Remo de serviço usando Java em Tecido de Serviço Azure
description: Service Fabric remoting permite que clientes e serviços comuniquem com os serviços Java usando uma chamada de procedimento remoto.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: eef63d7a2c8a4b15938dfbffd7db5f9d1b22d426
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426639"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Serviço de remo em Java com Serviços Fiáveis
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java em Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Para serviços que não estão ligados a um determinado protocolo de comunicação ou stack, como WebAPI, Windows Communication Foundation (WCF), ou outros, o quadro de Serviços Fiáveis fornece um mecanismo de remoing para configurar procedimentoremoto de forma rápida e fácil serviços.  Este artigo discute como configurar procedimentoremoto remoto exige serviços escritos com Java.

## <a name="set-up-remoting-on-a-service"></a>Configurar remoting em um serviço
A instalação de remopara um serviço é feita em dois passos simples:

1. Crie uma interface para o seu serviço implementar. Esta interface define os métodos disponíveis para uma chamada de procedimento remoto no seu serviço. Os métodos devem ser métodos assíncronos de retorno de tarefas. A interface `microsoft.serviceFabric.services.remoting.Service` deve ser implementada para sinalizar que o serviço tem uma interface remoting.
2. Use um ouvinte remoting ao seu serviço. Esta é `CommunicationListener` uma implementação que fornece capacidades de remo. `FabricTransportServiceRemotingListener`pode ser utilizado para criar um ouvinte remoting utilizando o protocolo de transporte de remo padrão.

Por exemplo, o seguinte serviço apátrida expõe um único método para obter "Hello World" sobre uma chamada de procedimento remoto.

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
> Os argumentos e os tipos de retorno na interface de serviço podem ser de tipos simples, complexos ou personalizados, mas devem ser ser ilizáveis.
>
>

## <a name="call-remote-service-methods"></a>Ligue para métodos de serviço remoto
A chamada de métodos num serviço utilizando a pilha de remo `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` é feita utilizando um representante local para o serviço através da classe. O `ServiceProxyBase` método cria um proxy local utilizando a mesma interface que o serviço implementa. Com esse representante, pode simplesmente ligar para métodos na interface remotamente.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

O quadro de remoing propaga exceções lançadas ao serviço ao cliente. Assim, a lógica de manipulação `ServiceProxyBase` de exceções no cliente, utilizando, pode lidar diretamente com exceções que o serviço lança.

## <a name="service-proxy-lifetime"></a>Serviço Proxy Lifetime
A criação serviceProxy é uma operação leve, para que possa criar o máximo que precisar. As instâncias de Procuração de Serviço podem ser reutilizadas enquanto forem necessárias. Se uma chamada de procedimento remoto lançar uma Exceção, ainda pode reutilizar a mesma instância de procuração. Cada ServiceProxy contém um cliente de comunicação usado para enviar mensagens por cima do fio. Ao invocar chamadas remotas, são realizadas verificações internas para determinar se o cliente de comunicação é válido. Com base nos resultados desses controlos, o cliente de comunicação é recriado se necessário. Portanto, se ocorrer uma exceção, não precisa `ServiceProxy`de recriar .

### <a name="serviceproxyfactory-lifetime"></a>ServiçoProxyFactory Lifetime
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) é uma fábrica que cria procuração para diferentes interfaces de remo. Se utilizar a `ServiceProxyBase.create` API para criar `FabricServiceProxyFactory`procuração, então a estrutura cria um .
É útil criar um manualmente quando precisa de substituir as propriedades [do ServiceRemotingClientFactory.](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory)
A fábrica é uma operação cara. `FabricServiceProxyFactory`mantém cache de clientes de comunicação.
A melhor prática `FabricServiceProxyFactory` é cache o máximo possível.

## <a name="remoting-exception-handling"></a>Manipulação de exceções remoing
Toda a exceção remota lançada pelo serviço API, é enviada de volta para o cliente como RuntimeException ou FabricException.

ServiceProxy lida com todas as Exceções failover para a partição de serviço para a sua criação. Reresolve os pontos finais se houver exceções failover (exceções não transitórias) e retenta a chamada com o ponto final correto. Número de tentativas de falha exceção é indefinido.
No caso de TransientExceptions, só volta a tentar a chamada.

Os parâmetros de retry predefinidos são obtidos pela [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Pode configurar estes valores passando o objeto OperationRetrySettings para o construtor ServiceProxyFactory.

## <a name="next-steps"></a>Passos seguintes
* [Garantir a comunicação de serviços fiáveis](service-fabric-reliable-services-secure-communication-java.md)
