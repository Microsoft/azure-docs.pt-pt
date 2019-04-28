---
title: Comunicação remota do serviço através de Java no Azure Service Fabric | Documentos da Microsoft
description: Comunicação remota do Service Fabric permite que os clientes e serviços para comunicar com os serviços de Java através de uma chamada de procedimento remoto.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 51c8c689bd3fe3e8967bab77e776ad02f9cb59f1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123640"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Comunicação remota do serviço em Java com o Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java em Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Para os serviços que não ficam amarrados a um protocolo de comunicação específico ou a pilha, como WebAPI, Windows Communication Foundation (WCF) ou outras pessoas, a estrutura de Reliable Services fornece um mecanismo de comunicação remota de forma rápida e fácil configurar chamadas de procedimento remoto para serviços.  Este artigo aborda como configurar a chamadas de procedimento remoto para os serviços escritos com Java.

## <a name="set-up-remoting-on-a-service"></a>Configurar remotamente num serviço
Configurar a comunicação remota para um serviço é feita em duas etapas simples:

1. Crie uma interface para o seu serviço implementar. Esta interface define os métodos disponíveis para uma chamada de procedimento remoto no seu serviço. Tem de ser os métodos que retornam task métodos assíncronos. Tem de implementar a interface `microsoft.serviceFabric.services.remoting.Service` para sinalizar que o serviço tem uma interface de comunicação remota.
2. Utilize um serviço de escuta de comunicação remota no seu serviço. Este é um `CommunicationListener` implementação que fornece capacidades de comunicação remota. `FabricTransportServiceRemotingListener` pode ser utilizado para criar um serviço de escuta de comunicação remota utilizando o protocolo de transporte de comunicação remota do padrão.

Por exemplo, o serviço sem estado seguinte expõe um único método para obter "Hello World" através de uma chamada de procedimento remoto.

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
> Os argumentos e os tipos de retorno na interface de serviço podem ser qualquer tipo personalizado, simple ou complexo, mas têm de ser serializáveis.
>
>

## <a name="call-remote-service-methods"></a>Chamar os métodos de serviço remoto
Chamar métodos num serviço utilizando a pilha de comunicação remota é feito usando um proxy local para o serviço através do `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` classe. O `ServiceProxyBase` método cria um proxy local com a mesma interface, que implementa o serviço. Com que o proxy, pode simplesmente chamar métodos na interface remotamente.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

A estrutura de comunicação remota propaga exceções geradas no serviço ao cliente. Lógica de manipulação de exceção por isso, o cliente utilizando `ServiceProxyBase` diretamente pode manipular exceções que o serviço gera.

## <a name="service-proxy-lifetime"></a>O tempo de vida do serviço Proxy
Criação de ServiceProxy é uma operação simples, para que possa criar tantos conforme necessário. Instâncias de Proxy de serviço podem ser reutilizadas, desde que eles são necessários. Se uma chamada de procedimento remoto lança uma exceção, ainda pode reutilizar a mesma instância de proxy. Cada ServiceProxy contenha um cliente de comunicação utilizado para enviar mensagens durante a transmissão. Ao invocar chamadas remotas, são executadas verificações de internas para determinar se o cliente de comunicação é válido. Com base nos resultados dessas verificações, o cliente de comunicação é recriado se for necessário. Por conseguinte, se ocorrer uma exceção, não precisa de recriar `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>Tempo de vida de ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) é uma fábrica que cria o proxy para interfaces de comunicação remota diferente. Se usar a API `ServiceProxyBase.create` para a criação de proxy, em seguida, framework cria uma `FabricServiceProxyFactory`.
É útil criar um manualmente quando precisa substituir [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) propriedades.
Que é uma operação dispendiosa. `FabricServiceProxyFactory` mantém a cache de clientes de comunicação.
Melhor prática consiste em cache `FabricServiceProxyFactory` pelo tempo possível.

## <a name="remoting-exception-handling"></a>Manipulação de exceção de comunicação remota
A exceção de remota gerada pela API de serviço, como RuntimeException ou FabricException são enviados ao cliente.

ServiceProxy lidar com todas as exceções de ativação pós-falha para a partição de serviço é criado para. Novamente resolve os pontos de extremidade se houver Exceptions(Non-Transient Exceptions) ativação pós-falha e repetem a chamada com o ponto final correto. Número de tentativas de ativação pós-falha de exceção é indefinido.
Em caso de TransientExceptions, apenas repete a chamada.

Padrão repetição parâmetros são fornecem pela [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Pode configurar estes valores, passando o objeto OperationRetrySettings para ServiceProxyFactory construtor.

## <a name="next-steps"></a>Passos Seguintes
* [Proteger a comunicação de Reliable Services](service-fabric-reliable-services-secure-communication-java.md)
