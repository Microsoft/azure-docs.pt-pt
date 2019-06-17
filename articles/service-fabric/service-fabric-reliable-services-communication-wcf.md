---
title: Pilha de comunicações WCF de serviços fiável | Documentos da Microsoft
description: A pilha de comunicações WCF incorporada no Service Fabric fornece comunicações de WCF do serviço de cliente para Reliable Services.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: ae8a0ab0382083ebfca0834d2238403668efa71d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725606"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Pilha de comunicações baseadas no WCF de Reliable Services
A estrutura de Reliable Services permite que os autores de serviços escolher a pilha de comunicação que pretende utilizar o seu serviço. Eles podem de plug-in a pilha de comunicações de sua preferência por meio do **ICommunicationListener** devolvido do [CreateServiceReplicaListeners ou CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) métodos. O framework fornece uma implementação da pilha de comunicações com base no Windows Communication Foundation (WCF) para autores de serviço que pretendem utilizar a comunicação baseada em WCF.

## <a name="wcf-communication-listener"></a>Serviço de escuta de comunicação de WCF
A implementação de específicas do WCF de **ICommunicationListener** fornecido pela **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** classe.

Que acabam sendo Digamos que exista um contrato de serviço do tipo `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Podemos criar um serviço de escuta de comunicação do WCF no serviço da seguinte forma.

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Escrita de clientes para a pilha de comunicações de WCF
Para escrever os clientes comuniquem com os serviços usando o WCF, o framework fornece **WcfClientCommunicationFactory**, que é a implementação de específicas do WCF de [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

O canal de comunicação do WCF pode ser acedido a partir da **WcfCommunicationClient** criado pela **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Código de cliente pode utilizar o **WcfCommunicationClientFactory** juntamente com o **WcfCommunicationClient** quais implementa **ServicePartitionClient** para determinar o ponto final de serviço e comunicar com o serviço.

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
> A predefinição ServicePartitionResolver parte do princípio de que o cliente está em execução no mesmo cluster que o serviço. Se não for o caso, crie um objeto de ServicePartitionResolver e passar os pontos de extremidade de ligação do cluster.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
* [Chamada de procedimento remoto com a comunicação remota do Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Web API com o OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Proteger a comunicação de Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)

