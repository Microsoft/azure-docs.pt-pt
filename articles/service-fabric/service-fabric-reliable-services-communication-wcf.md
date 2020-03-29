---
title: Pilha de comunicação WCF de serviços fiáveis
description: A pilha de comunicações WCF incorporada no Service Fabric fornece comunicação WCF de serviço ao cliente para serviços fiáveis.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: 7f3b3974893316a488270f755b8f8822080658d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433869"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Pilha de comunicação baseada no WCF para Serviços Fiáveis
O quadro de Serviços Fiáveis permite que os autores de serviços escolham a pilha de comunicação que pretendem utilizar para o seu serviço. Podem ligar a pilha de comunicação à sua escolha através do **ICommunicationListener** devolvido dos [métodos CreateServiceReplicaListeners ou CreateServiceInstanceListeners.](service-fabric-reliable-services-communication.md) O quadro fornece uma implementação da pilha de comunicação com base na Fundação de Comunicação do Windows (WCF) para autores de serviços que queiram utilizar a comunicação baseada no WCF.

## <a name="wcf-communication-listener"></a>Ouvinte de Comunicação WCF
A implementação específica do **ICommunicationListener** é fornecida pela classe **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener.**

Para não dizer que temos um contrato de serviço de tipo`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Podemos criar um ouvinte de comunicação WCF no serviço da seguinte forma.

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Escrever clientes para a pilha de comunicações wCF
Para que os clientes de escrita comuniquem com os serviços utilizando o WCF, o quadro fornece **a WcfClientCommunicationFactory**, que é a implementação específica da WCF da [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

O canal de comunicação WCF pode ser acedido a partir do **WcfCommunicationClient** criado pela **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

O código do cliente pode usar a **WcfCommunicationClientFactory** juntamente com o **WcfCommunicationClient** que implementa o **ServicePartitionClient** para determinar o ponto final do serviço e comunicar com o serviço.

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
> O ServicePartitionResolver, default, assume que o cliente está a funcionar no mesmo cluster que o serviço. Se não for esse o caso, crie um objeto ServicePartitionResolver e passe nos pontos finais da ligação do cluster.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Chamada de procedimento remoto com serviços fiáveis remoting](service-fabric-reliable-services-communication-remoting.md)
* [Web API com OWIN em Serviços Fiáveis](service-fabric-reliable-services-communication-webapi.md)
* [Garantir a comunicação de serviços fiáveis](service-fabric-reliable-services-secure-communication-wcf.md)

