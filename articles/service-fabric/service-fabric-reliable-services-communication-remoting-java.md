---
title: Remoagem de serviço usando Java em Tecido de Serviço Azure
description: A remoing de tecido de serviço permite que clientes e serviços comuniquem com os serviços java através de uma chamada de procedimento remoto.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: d53d20510db70d81aab796efab48de40c880bb3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87316129"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Remoing de serviço em Java com Serviços Fiáveis
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java em Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Para serviços que não estejam ligados a um determinado protocolo de comunicação ou stack, como WebAPI, Windows Communication Foundation (WCF), ou outros, o quadro de Serviços Fiáveis fornece um mecanismo de remoagem para configurar rapidamente e facilmente o procedimento remoto pedidos de serviços.  Este artigo discute como configurar procedimentos remotos pedidos de serviços escritos com Java.

## <a name="set-up-remoting-on-a-service"></a>Configurar a remoing em um serviço
A criação de um remoing para um serviço é feita em dois passos simples:

1. Crie uma interface para o seu serviço implementar. Esta interface define os métodos disponíveis para uma chamada de procedimento remoto no seu serviço. Os métodos devem ser métodos assíncronos de retorno de tarefas. A interface deve implementar `microsoft.serviceFabric.services.remoting.Service` para sinalizar que o serviço tem uma interface de remoting.
2. Utilize um ouvinte de remoing ao seu serviço. Esta é uma `CommunicationListener` implementação que fornece capacidades de remoing. `FabricTransportServiceRemotingListener` pode ser usado para criar um ouvinte de remoing usando o protocolo de transporte de remoing predefinido.

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
> Os argumentos e os tipos de devolução na interface de serviço podem ser quaisquer tipos simples, complexos ou personalizados, mas devem ser serializáveis.
>
>

## <a name="call-remote-service-methods"></a>Ligue para métodos de serviço remoto
Os métodos de chamada de um serviço utilizando a pilha de remoting são feitos usando um representante local para o serviço através da `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` classe. O `ServiceProxyBase` método cria um proxy local utilizando a mesma interface que o serviço implementa. Com esse proxy, pode simplesmente ligar para os métodos da interface remotamente.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

A estrutura de remoing propaga exceções lançadas ao serviço ao cliente. Assim, a lógica de tratamento de exceções no cliente, `ServiceProxyBase` utilizando, pode lidar diretamente com exceções que o serviço lança.

## <a name="service-proxy-lifetime"></a>Vida útil do proxy de serviço
A criação de ServiceProxy é uma operação leve, para que possa criar quantas precisar. As instâncias de procuração de serviço podem ser reutilizadas enquanto forem necessárias. Se uma chamada de procedimento remoto lançar uma exceção, ainda pode reutilizar a mesma instância de procuração. Cada Procuração de Serviço contém um cliente de comunicação usado para enviar mensagens pelo fio. Ao invocar chamadas remotas, são realizadas verificações internas para determinar se o cliente de comunicação é válido. Com base nos resultados dessas verificações, o cliente de comunicação é recriado se necessário. Portanto, se ocorrer uma exceção, não é necessário recriar `ServiceProxy` .

### <a name="serviceproxyfactory-lifetime"></a>Vida útil da procuração de serviço
[FabricServiceProxyFactory](/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) é uma fábrica que cria procuração para diferentes interfaces de remoting. Se utilizar a API `ServiceProxyBase.create` para criar procuração, então a estrutura cria um `FabricServiceProxyFactory` .
É útil criar um manualmente quando é necessário anular as propriedades [do ServiceRemotingClientFactory.](/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory)
A fábrica é uma operação cara. `FabricServiceProxyFactory` mantém cache de clientes de comunicação.
A melhor prática é cache `FabricServiceProxyFactory` o máximo possível.

## <a name="remoting-exception-handling"></a>Tratamento de Exceções de Remoting
Toda a exceção remota lançada pela API de serviço, é devolvida ao cliente quer como RuntimeException ou FabricException.

O ServiceProxy lida com toda a Falha de Exceção para a partição de serviço para a qual é criada. Reensi para o efeito dos pontos finais se houver Exceções de Failover (Exceções Não Transitórias) e recauchutar a chamada com o ponto final correto. Número de retró assim que não há exceção é por tempo indeterminado.
No caso de TransientExceptions, só retrifica a chamada.

Os parâmetros de relagem predefinidos são provied por [OperaçãoRetrySettings](/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Pode configurar estes valores passando operaçãoRerySettings oponha-se ao construtor serviceProxyFactory.

## <a name="next-steps"></a>Passos seguintes
* [Assegurar a comunicação para serviços fiáveis](service-fabric-reliable-services-secure-communication-java.md)
