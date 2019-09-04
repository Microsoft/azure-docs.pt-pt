---
title: Comunicação remota do serviço C# usando no Service Fabric | Microsoft Docs
description: Service Fabric comunicação remota permite que clientes e serviços se C# comuniquem com serviços usando uma chamada de procedimento remoto.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: a9ef2cd695f9591f299bb85b95d14d60b987c38d
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258697"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Comunicação remota do C# serviço no com o Reliable Services

> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java em Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Para serviços que não estão vinculados a um determinado protocolo de comunicação ou pilha, como uma API Web, Windows Communication Foundation ou outros, a estrutura de Reliable Services fornece um mecanismo de comunicação remota para configurar de forma rápida e fácil as chamadas de procedimento remoto para serviços. Este artigo discute como configurar chamadas de procedimento remoto para serviços escritos com C#o.

## <a name="set-up-remoting-on-a-service"></a>Configurar a comunicação remota em um serviço

Você pode configurar a comunicação remota para um serviço em duas etapas simples:

1. Crie uma interface para o serviço implementar. Essa interface define os métodos que estão disponíveis para uma chamada de procedimento remoto em seu serviço. Os métodos devem ser métodos assíncronos que retornam tarefas. A interface deve implementar `Microsoft.ServiceFabric.Services.Remoting.IService` para sinalizar que o serviço tem uma interface de comunicação remota.
2. Use um ouvinte de comunicação remota em seu serviço. Um ouvinte de comunicação `ICommunicationListener` remota é uma implementação que fornece recursos de comunicação remota. O `Microsoft.ServiceFabric.Services.Remoting.Runtime` namespace contém o método `CreateServiceRemotingListener` de extensão para serviços com e sem estado que podem ser usados para criar um ouvinte de comunicação remota usando o protocolo de transporte remoto padrão.

>[!NOTE]
>O `Remoting` namespace está disponível como um pacote NuGet separado chamado `Microsoft.ServiceFabric.Services.Remoting`.

Por exemplo, o serviço sem estado a seguir expõe um único método para obter "Olá, Mundo" sobre uma chamada de procedimento remoto.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> Os argumentos e os tipos de retorno na interface de serviço podem ser de tipos simples, complexos ou personalizados, mas eles devem ser capazes de serem serializados pelo [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)do .net.
>
>

## <a name="call-remote-service-methods"></a>Chamar métodos de serviço remoto

Chamar métodos em um serviço usando a pilha de comunicação remota é feito usando um proxy local para o serviço por meio `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` da classe. O `ServiceProxy` método cria um proxy local usando a mesma interface implementada pelo serviço. Com esse proxy, você pode chamar métodos na interface remotamente.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A estrutura de comunicação remota propaga exceções geradas pelo serviço para o cliente. Como resultado, quando `ServiceProxy`é usado, o cliente é responsável por manipular as exceções geradas pelo serviço.

## <a name="service-proxy-lifetime"></a>Tempo de vida do proxy de serviço

A criação de proxy de serviço é uma operação leve, para que você possa criar quantos forem necessários. As instâncias de proxy de serviço podem ser reutilizadas desde que sejam necessárias. Se uma chamada de procedimento remoto lançar uma exceção, você ainda poderá reutilizar a mesma instância de proxy. Cada proxy de serviço contém um cliente de comunicação usado para enviar mensagens pela conexão. Ao invocar chamadas remotas, verificações internas são executadas para determinar se o cliente de comunicação é válido. Com base nos resultados dessas verificações, o cliente de comunicação é recriado, se necessário. Portanto, se ocorrer uma exceção, você não precisará recriá `ServiceProxy`-la.

### <a name="service-proxy-factory-lifetime"></a>Tempo de vida da fábrica de proxy de serviço

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) é uma fábrica que cria instâncias de proxy para diferentes interfaces de comunicação remota. Se você usar a API `ServiceProxyFactory.CreateServiceProxy` para criar um proxy, a estrutura criará um proxy de serviço singleton.
É útil criar um manualmente quando você precisa substituir as propriedades [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) .
A criação de fábrica é uma operação cara. Uma fábrica de proxy de serviço mantém um cache interno do cliente de comunicação.
Uma prática recomendada é armazenar em cache a fábrica de proxy de serviço pelo máximo de tempo possível.

## <a name="remoting-exception-handling"></a>Manipulação de exceção de comunicação remota

Todas as exceções remotas geradas pela API de serviço são enviadas de volta ao cliente como AggregateException. Exceções remotas devem ser capazes de ser serializadas pelo DataContract. Se não forem, a API de proxy lançará [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) com o erro de serialização nele.

O proxy de serviço trata todas as exceções de failover para a partição de serviço para a qual ela é criada. Ele resolverá novamente os pontos de extremidade se houver exceções de failover (exceções não transitórias) e tentará novamente a chamada com o ponto de extremidade correto. O número de repetições para exceções de failover é indefinido.
Se ocorrerem exceções transitórias, o proxy tentará novamente a chamada.

Os parâmetros de repetição padrão são fornecidos pelo [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Um usuário pode configurar esses valores passando o objeto OperationRetrySettings para o Construtor ServiceProxyFactory.

## <a name="use-the-remoting-v2-stack"></a>Usar a pilha v2 de comunicação remota

A partir da versão 2,8 do pacote de comunicação remota do NuGet, você tem a opção de usar a pilha v2 de comunicação remota. A pilha v2 de comunicação remota apresenta melhor desempenho. Ele também fornece recursos como serialização personalizada e mais APIs conectáveis.
O código do modelo continua a usar a pilha remota v1.
A comunicação remota v2 não é compatível com V1 (a pilha remota anterior). Siga as instruções no artigo [Atualizar de v1 para v2](#upgrade-from-remoting-v1-to-remoting-v2) para evitar efeitos sobre a disponibilidade do serviço.

As abordagens a seguir estão disponíveis para habilitar a pilha v2.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Usar um atributo de assembly para usar a pilha v2

Essas etapas alteram o código do modelo para usar a pilha v2 usando um atributo de assembly.

1. Altere o recurso de ponto `"ServiceEndpoint"` de `"ServiceEndpointV2"` extremidade de para no manifesto do serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Use o `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` método de extensão para criar ouvintes de comunicação remota (igual para v1 e v2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Marque o assembly que contém as interfaces de comunicação remota `FabricTransportServiceRemotingProvider` com um atributo.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Nenhuma alteração de código é necessária no projeto cliente.
Crie o assembly de cliente com o assembly de interface para certificar-se de que o atributo de assembly mostrado anteriormente é usado.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Usar classes v2 explícitas para usar a pilha v2

Como alternativa ao uso de um atributo de assembly, a pilha V2 também pode ser habilitada usando classes v2 explícitas.

Essas etapas alteram o código do modelo para usar a pilha v2 usando classes v2 explícitas.

1. Altere o recurso de ponto `"ServiceEndpoint"` de `"ServiceEndpointV2"` extremidade de para no manifesto do serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Use [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) do `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` namespace.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. Use [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) do `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` namespace para criar clientes.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Atualização do Remoting v1 para o Remoting v2

Para atualizar de v1 para v2, são necessárias atualizações em duas etapas. Siga as etapas nesta sequência.

1. Atualize o serviço v1 para o serviço v2 usando este atributo.
Essa alteração certifica-se de que o serviço escuta no ouvinte v1 e v2.

    a. Adicione um recurso de ponto de extremidade com o nome "ServiceEndpointV2" no manifesto do serviço.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Use o método de extensão a seguir para criar um ouvinte de comunicação remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Adicione um atributo de assembly em interfaces de comunicação remota para usar o ouvinte v1 e V2 e o cliente v2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Atualize o cliente v1 para um cliente v2 usando o atributo cliente v2.
Essa etapa garante que o cliente use a pilha v2.
Nenhuma alteração no projeto/serviço do cliente é necessária. A criação de projetos de cliente com o assembly de interface atualizado é suficiente.

3. Este passo é opcional. Use o atributo de ouvinte V2 e, em seguida, atualize o serviço v2.
Essa etapa garante que o serviço esteja escutando apenas no ouvinte v2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Usar a pilha do Remoting v2 (compatível com a interface)

 A pilha remota v2 (compatível com a interface, conhecida como V2_1) tem todos os recursos da pilha de comunicação remota v2. Sua pilha de interface é compatível com a pilha de comunicação remota v1, mas não é compatível com a versão V2 e v1. Para atualizar de v1 para V2_1 sem afetar a disponibilidade do serviço, siga as etapas no artigo atualizar de v1 para v2 (compatível com a interface).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Usar um atributo de assembly para usar a pilha do Remoting v2 (compatível com a interface)

Siga estas etapas para mudar para uma pilha V2_1.

1. Adicione um recurso de ponto de extremidade com o nome "ServiceEndpointV2_1" no manifesto do serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Use o método de extensão de comunicação remota para criar um ouvinte de comunicação remota.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Adicione um [atributo de assembly](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) em interfaces de comunicação remota.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Nenhuma alteração é necessária no projeto cliente.
Crie o assembly de cliente com o assembly de interface para certificar-se de que o atributo de assembly anterior está sendo usado.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Usar classes de comunicação remota explícitas para criar uma fábrica de ouvinte/cliente para a versão v2 (compatível com interface)

Siga estes passos.

1. Adicione um recurso de ponto de extremidade com o nome "ServiceEndpointV2_1" no manifesto do serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Use o [ouvinte remoto v2](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). O nome de recurso do ponto de extremidade de serviço padrão usado é "ServiceEndpointV2_1". Ele deve ser definido no manifesto do serviço.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. Use a [fábrica de cliente](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)v2.
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Atualização do Remoting v1 para o Remoting v2 (compatível com a interface)

Para atualizar da v1 para a v2 (compatível com a interface, conhecida como V2_1), são necessárias atualizações em duas etapas. Siga as etapas nesta sequência.

> [!NOTE]
> Ao atualizar do v1 para o v2, verifique `Remoting` se o namespace está atualizado para usar v2. Exemplo: ' Microsoft. FabricTransport. Services. Remoting. v2. Client '
>
>

1. Atualize o serviço v1 para o serviço V2_1 usando o atributo a seguir.
Essa alteração garante que o serviço esteja escutando no ouvinte v1 e V2_1.

    a. Adicione um recurso de ponto de extremidade com o nome "ServiceEndpointV2_1" no manifesto do serviço.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Use o método de extensão a seguir para criar um ouvinte de comunicação remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Adicione um atributo de assembly em interfaces de comunicação remota para usar o ouvinte v1, V2_1 e o cliente V2_1.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Atualize o cliente v1 para o cliente V2_1 usando o atributo de cliente V2_1.
Essa etapa garante que o cliente esteja usando a pilha V2_1.
Nenhuma alteração no projeto/serviço do cliente é necessária. A criação de projetos de cliente com o assembly de interface atualizado é suficiente.

3. Este passo é opcional. Remova a versão v1 do ouvinte do atributo e, em seguida, atualize o serviço v2.
Essa etapa garante que o serviço esteja escutando apenas no ouvinte v2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Usar a serialização personalizada com uma mensagem encapsulada de comunicação remota

Para uma mensagem encapsulada de comunicação remota, criamos um único objeto encapsulado com todos os parâmetros como um campo nele.
Siga estes passos.

1. Implemente `IServiceRemotingMessageSerializationProvider` a interface para fornecer implementação para serialização personalizada.
    Esse trecho de código mostra a aparência da implementação.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Substitua o provedor `JsonSerializationProvider` de serialização padrão por um ouvinte de comunicação remota.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. Substitua o provedor `JsonSerializationProvider` de serialização padrão por para uma fábrica de cliente remoto.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Passos Seguintes

* [API Web com OWIN em Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Windows Communication Foundation comunicação com Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Comunicação segura para Reliable Services](service-fabric-reliable-services-secure-communication.md)
