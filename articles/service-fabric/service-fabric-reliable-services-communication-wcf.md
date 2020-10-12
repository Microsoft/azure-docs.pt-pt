---
title: Pilha de comunicação WCF de serviços fiáveis
description: A pilha de comunicação incorporada do WCF em Service Fabric fornece comunicação WCF de serviço ao cliente para serviços fiáveis.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: c5b5d413eee2528e2d5c7d04d06f1607949beaae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89012709"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Pilha de comunicações baseada em WCF para serviços fiáveis
O quadro de Serviços Fiáveis permite que os autores de serviços escolham a pilha de comunicação que querem usar para o seu serviço. Podem ligar a pilha de comunicação à sua escolha através dos **métodos ICommunicationListener** devolvidos dos métodos [CreateServiceReplicaListeners ou CreateServiceInstanceListeners.](service-fabric-reliable-services-communication.md) O quadro fornece uma implementação da pilha de comunicação com base na Windows Communication Foundation (WCF) para autores de serviços que queiram utilizar comunicações baseadas em WCF.

## <a name="wcf-communication-listener"></a>Ouvinte de Comunicação WCF
A implementação específica do **ICommunicationListener** é fornecida pela classe **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener.**

Para não dizer que temos um contrato de serviço de tipo `ICalculator`

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Escrever clientes para a pilha de comunicação do WCF
Para a escrita dos clientes para comunicar com os serviços utilizando o WCF, o quadro fornece **wcfClientCommunicationFactory,** que é a implementação específica do WCF da [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

O canal de comunicação WCF pode ser acedido a **WcfCommunicationClientFactory**partir do **WcfCommunicationClientcientcory** .

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

O código do cliente pode utilizar o **WcfCommunicationClientFactory** juntamente com o **WcfCommunicationClient** que implementa **o ServicePartitionClient** para determinar o ponto final de serviço e comunicar com o serviço.

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
> O ServicePartitionResolver predefinido assume que o cliente está a funcionar no mesmo cluster que o serviço. Se não for esse o caso, crie um objeto ServicePartitionResolver e passe nos pontos finais de ligação do cluster.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Chamada de procedimento remoto com remoting de Serviços Fiáveis](service-fabric-reliable-services-communication-remoting.md)
* [Web API com OWIN em Serviços Fiáveis](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Assegurar a comunicação para serviços fiáveis](service-fabric-reliable-services-secure-communication-wcf.md)
