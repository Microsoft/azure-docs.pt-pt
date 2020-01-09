---
title: Pilha de comunicações WCF de Reliable Services
description: A pilha de comunicação WCF interna no Service Fabric fornece comunicação WCF de serviço de cliente para Reliable Services.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: 7f3b3974893316a488270f755b8f8822080658d9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433869"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Pilha de comunicação baseada em WCF para Reliable Services
O Reliable Services Framework permite que os autores de serviço escolham a pilha de comunicação que desejam usar para seu serviço. Eles podem conectar a pilha de comunicação de sua escolha por meio do **ICommunicationListener** retornado dos métodos [CreateServiceReplicaListeners ou CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) . A estrutura fornece uma implementação da pilha de comunicação com base no Windows Communication Foundation (WCF) para autores de serviço que desejam usar a comunicação baseada no WCF.

## <a name="wcf-communication-listener"></a>Ouvinte de comunicação do WCF
A implementação específica do WCF do **ICommunicationListener** é fornecida pela classe **Microsoft. Services. Communication. WCF. Runtime. WcfCommunicationListener** .

Última digamos que temos um contrato de serviço do tipo `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Podemos criar um ouvinte de comunicação do WCF no serviço da seguinte maneira.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Gravando clientes para a pilha de comunicação do WCF
Para que os clientes de gravação se comuniquem com serviços usando o WCF, a estrutura fornece **WcfClientCommunicationFactory**, que é a implementação específica do WCF do [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

O canal de comunicação do WCF pode ser acessado do **WcfCommunicationClient** criado pelo **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

O código do cliente pode usar o **WcfCommunicationClientFactory** junto com o **WcfCommunicationClient** , que implementa **ServicePartitionClient** para determinar o ponto de extremidade do serviço e se comunicar com o serviço.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> O ServicePartitionResolver padrão assume que o cliente está sendo executado no mesmo cluster que o serviço. Se esse não for o caso, crie um objeto ServicePartitionResolver e passe os pontos de extremidade de conexão do cluster.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Chamada de procedimento remoto com Reliable Services comunicação remota](service-fabric-reliable-services-communication-remoting.md)
* [API Web com OWIN em Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Protegendo a comunicação para Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)

