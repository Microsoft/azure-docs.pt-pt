---
title: Comunicação remota do serviço com c# no Service Fabric | Documentos da Microsoft
description: Comunicação remota do Service Fabric permite que clientes e serviços comunicar com serviços de c#, usando uma chamada de procedimento remoto.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 366ab452d1693315e7e71301d953248008c1eac8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079133"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Comunicação remota do serviço em c# com o Reliable Services

> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java em Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Para os serviços que não ficam amarrados a um protocolo de comunicação específico ou a pilha, como uma API web, Windows Communication Foundation ou outros, a estrutura de Reliable Services fornece um mecanismo de comunicação remota de forma rápida e fácil configurar chamadas de procedimento remoto para serviços. Este artigo aborda como configurar a chamadas de procedimento remoto para os serviços escritos com o c#.

## <a name="set-up-remoting-on-a-service"></a>Configurar remotamente num serviço

Pode configurar a comunicação remota para um serviço em duas etapas simples:

1. Crie uma interface para o seu serviço implementar. Esta interface define os métodos disponíveis para uma chamada de procedimento remoto no seu serviço. Tem de ser os métodos que retornam task métodos assíncronos. Tem de implementar a interface `Microsoft.ServiceFabric.Services.Remoting.IService` para sinalizar que o serviço tem uma interface de comunicação remota.
2. Utilize um serviço de escuta de comunicação remota no seu serviço. Um serviço de escuta de comunicação remota é um `ICommunicationListener` implementação que fornece capacidades de comunicação remota. O `Microsoft.ServiceFabric.Services.Remoting.Runtime` espaço de nomes contém o método de extensão `CreateServiceRemotingListener` para serviços com e sem monitoração de estado que podem ser utilizados para criar um serviço de escuta de comunicação remota utilizando o protocolo de transporte de comunicação remota do padrão.

>[!NOTE]
>O `Remoting` espaço de nomes está disponível como um pacote de NuGet separado chamado `Microsoft.ServiceFabric.Services.Remoting`.

Por exemplo, o serviço sem estado seguinte expõe um único método para obter "Hello World" através de uma chamada de procedimento remoto.

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
> Os argumentos e os tipos de retorno na interface de serviço podem ser qualquer tipo personalizado, simple ou complexo, mas têm de ser capazes de ser serializado por do .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Chamar os métodos de serviço remoto

Chamar métodos num serviço utilizando a pilha de comunicação remota é feito usando um proxy local para o serviço através do `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` classe. O `ServiceProxy` método cria um proxy local com a mesma interface, que implementa o serviço. Com que o proxy, pode chamar métodos na interface remotamente.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A estrutura de comunicação remota propaga exceções geradas pelo serviço ao cliente. Como resultado, quando `ServiceProxy`é utilizada, o cliente é responsável por processar as exceções geradas pelo serviço.

## <a name="service-proxy-lifetime"></a>O tempo de vida do serviço proxy

Criação de proxy do serviço é uma operação simples, para que possa criar tantos conforme necessário. Instâncias de proxy de serviço podem ser reutilizadas para, desde que eles são necessários. Se uma chamada de procedimento remoto lança uma exceção, ainda pode reutilizar a mesma instância de proxy. Cada proxy do serviço contém um cliente de comunicação utilizado para enviar mensagens durante a transmissão. Ao invocar chamadas remotas, são executadas verificações de internas para determinar se o cliente de comunicação é válido. Com base nos resultados dessas verificações, o cliente de comunicação é recriado se for necessário. Por conseguinte, se ocorrer uma exceção, não terá de voltar a criar `ServiceProxy`.

### <a name="service-proxy-factory-lifetime"></a>Duração de fábrica do proxy de serviço

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) é uma fábrica que cria instâncias de proxy para interfaces de comunicação remota diferente. Se usar a API `ServiceProxyFactory.CreateServiceProxy` para criar um proxy, o framework cria um proxy de serviço de singleton.
É útil criar um manualmente quando precisa substituir [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) propriedades.
A criação do Factory é uma operação dispendiosa. Uma fábrica de proxy de serviço mantém um cache interna do cliente de comunicação.
É recomendado para colocar em cache a fábrica de proxy de serviço durante mais tempo.

## <a name="remoting-exception-handling"></a>Manipulação de exceção de comunicação remota

Todas as exceções remotas geradas pela API do serviço são enviadas ao cliente como AggregateException. Exceções remotas devem ser capazes de ser serializado por DataContract. Se não forem, o proxy de API emite [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) com o erro de serialização no mesmo.

O proxy de serviço processa todas as exceções de ativação pós-falha para a partição de serviço que é criado para. Ele resolve novamente os pontos de extremidade, se existirem exceções de ativação pós-falha (não transitórias exceções) e repete a chamada com o ponto final correto. O número de tentativas de exceções de ativação pós-falha é indefinido.
Se ocorrerem exceções transitórias, o proxy repete a chamada.

Padrão repetição parâmetros são fornecidos pela [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Um utilizador pode configurar estes valores, passando o objeto de OperationRetrySettings para o construtor de ServiceProxyFactory.

## <a name="use-the-remoting-v2-stack"></a>Utilize a pilha de comunicação remota V2

A partir da versão 2.8 do pacote de comunicação remota do NuGet, tem a opção para utilizar a pilha de V2 de comunicação remota. A pilha de comunicação remota V2 apresenta um desempenho melhor. Ele também fornece funcionalidades, como serialização personalizada e APIs mais conectáveis.
Código de modelo continua a utilizar a pilha de V1 de comunicação remota.
Gestão remota V2 não é compatível com V1 (a pilha de comunicação remota anterior). Siga as instruções no artigo [atualização do V1 para V2](#upgrade-from-remoting-v1-to-remoting-v2) para evitar efeitos na disponibilidade do serviço.

As seguintes abordagens estão disponíveis para permitir a pilha de V2.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Usar um atributo de assembly para usar as inúmeras V2

Estes passos alterar o código de modelo para utilizar a pilha de V2 com um atributo de assembly.

1. Alterar o recurso de ponto final da `"ServiceEndpoint"` para `"ServiceEndpointV2"` no manifesto do serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Utilize o `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` método de extensão para criar serviços de escuta de comunicação remota (igual para a V1 e V2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. O Assembly que contém as interfaces de comunicação remota com um `FabricTransportServiceRemotingProvider` atributo.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Sem alterações de código são necessárias no projeto cliente.
Crie o assembly de cliente com o assembly de interface para se certificar de que é utilizado o atributo de assembly mostrado anteriormente.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Usar as classes de V2 explícitos para utilizar a pilha de V2

Como alternativa à utilização de um atributo de assembly, a pilha de V2 também pode ser ativada através da utilização de classes de V2 explícitas.

Estes passos alterar o código de modelo para utilizar a pilha de V2 com classes de V2 explícitas.

1. Alterar o recurso de ponto final da `"ServiceEndpoint"` para `"ServiceEndpointV2"` no manifesto do serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Uso [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) partir o `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` espaço de nomes.

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

3. Uso [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) partir o `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` espaço de nomes para a criação de clientes.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Atualizar de comunicação remota do V1 para comunicação remota V2

Para atualizar a partir do V1 para V2, as atualizações de dois passos são necessárias. Siga os passos nesta sequência.

1. Atualize o serviço de V1 para V2 do serviço, utilizando este atributo.
Esta alteração torna-se de que o serviço de escuta no serviço de escuta V1 e V2.

    a. Adicione um recurso de ponto final com o nome "ServiceEndpointV2" no manifesto do serviço.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Utilize o seguinte método de extensão para criar um serviço de escuta de comunicação remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Adicione um atributo de assembly em interfaces de comunicação remota para utilizar o serviço de escuta V1 e V2 e o cliente de V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Atualize o cliente de V1 para um cliente V2, utilizando o atributo de cliente da V2.
Este passo torna-se de que o cliente utiliza a pilha de V2.
É necessária qualquer alteração no projeto/serviço de cliente. A criação de projetos de cliente com o assembly da interface atualizada é suficiente.

3. Este passo é opcional. Utilize o atributo de serviço de escuta V2 e, em seguida, atualize o serviço de V2.
Este passo certifica-se de que o serviço está a escutar apenas o serviço de escuta do V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Utilize a pilha de (compatível com a interface) de comunicação remota V2

 A comunicação remota V2 (interface compatível, conhecido como V2_1) pilha possui todos os recursos da pilha de comunicação remota V2. Sua pilha de interface é compatível com a pilha de V1 de comunicação remota, mas não é compatível com versões anteriores com V2 e V1. Para atualizar do V1 para V2_1, sem afetar a disponibilidade do serviço, siga os passos no artigo da atualização do V1 para V2 (interface compatível).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Utilize um atributo de assembly para utilizar a pilha de (compatível com a interface) de comunicação remota V2

Siga estes passos para alterar para uma pilha de V2_1.

1. Adicione um recurso de ponto final com o nome "ServiceEndpointV2_1" no manifesto do serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Utilize o método de extensão de comunicação remota para criar um serviço de escuta de comunicação remota.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Adicionar uma [atributo de assembly](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) nas interfaces de comunicação remota.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Sem alterações são necessárias no projeto cliente.
Crie o assembly de cliente com o assembly de interface para se certificar de que está a ser utilizado o atributo de assembly anterior.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Usar as classes de comunicação remota explícita para criar uma fábrica de serviço de escuta/cliente para a versão do V2 (compatível com a interface)

Siga estes passos.

1. Adicione um recurso de ponto final com o nome "ServiceEndpointV2_1" no manifesto do serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Utilize o [serviço de escuta de comunicação remota V2](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). O nome de recurso do ponto final do padrão serviço utilizado é "ServiceEndpointV2_1." Tem de ser definido no manifesto do serviço.

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

3. Utilizar o V2 [fábrica de cliente](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Atualizar de comunicação remota do V1 para comunicação remota V2 (interface compatível)

Para atualizar do V1 para V2 (interface compatível, conhecido como V2_1), atualizações de dois passos são necessárias. Siga os passos nesta sequência.

1. Atualize o serviço de V1 para o serviço de V2_1, utilizando o atributo seguinte.
Esta alteração torna-se de que o serviço está a escutar o V1 e o serviço de escuta V2_1.

    a. Adicione um recurso de ponto final com o nome "ServiceEndpointV2_1" no manifesto do serviço.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Utilize o seguinte método de extensão para criar um serviço de escuta de comunicação remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Adicione um atributo de assembly em interfaces de comunicação remota a utilizar a V1, serviço de escuta de V2_1 e V2_1 cliente.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Atualize o cliente de V1 para o cliente de V2_1, utilizando o atributo de cliente V2_1.
Este passo torna-se de que o cliente está a utilizar a pilha de V2_1.
É necessária qualquer alteração no projeto/serviço de cliente. A criação de projetos de cliente com o assembly da interface atualizada é suficiente.

3. Este passo é opcional. Remova a versão de serviço de escuta do V1 do atributo e, em seguida, atualize o serviço de V2.
Este passo certifica-se de que o serviço está a escutar apenas o serviço de escuta do V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Usar a serialização personalizada com uma mensagem de encapsulada de comunicação remota

Para uma mensagem de comunicação remota encapsulada, criamos um único objeto encapsulado com todos os parâmetros como um campo no mesmo.
Siga estes passos.

1. Implementar o `IServiceRemotingMessageSerializationProvider` interface para fornecer implementação para a serialização personalizada.
    Este fragmento de código mostra a aparência a implementação.

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

2. Substituir o provedor de serialização padrão com `JsonSerializationProvider` para um serviço de escuta de comunicação remota.

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

3. Substituir o provedor de serialização padrão com `JsonSerializationProvider` para uma fábrica de cliente de comunicação remota.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Passos Seguintes

* [Web API com o OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação do Windows Communication Foundation com o Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Proteger a comunicação de Reliable Services](service-fabric-reliable-services-secure-communication.md)
