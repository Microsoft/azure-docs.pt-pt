---
title: Remoing de serviço usando C# em Tecido de Serviço
description: A remoing de tecido de serviço permite que clientes e serviços se comuniquem com os serviços C# utilizando uma chamada de procedimento remoto.
ms.topic: conceptual
ms.date: 09/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: c3659fea73abae3c9c5264f227b90d0af95a93e7
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576660"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Remoing de serviço em C# com Serviços Fiáveis

> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java em Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Para serviços que não estão ligados a um determinado protocolo de comunicação ou stack, como uma API web, Windows Communication Foundation, ou outros, o quadro de Serviços Fiáveis fornece um mecanismo de remoagem para configurar rapidamente e facilmente o procedimento remoto chamadas para serviços. Este artigo discute como configurar pedidos de procedimento remoto para serviços escritos com C#.

## <a name="set-up-remoting-on-a-service"></a>Configurar a remoing em um serviço

Pode configurar a remoing para um serviço em dois passos simples:

1. Crie uma interface para o seu serviço implementar. Esta interface define os métodos disponíveis para uma chamada de procedimento remoto no seu serviço. Os métodos devem ser métodos assíncronos de retorno de tarefas. A interface deve implementar `Microsoft.ServiceFabric.Services.Remoting.IService` para sinalizar que o serviço tem uma interface de remoting.
2. Utilize um ouvinte de remoing ao seu serviço. Um ouvinte de remoing é uma `ICommunicationListener` implementação que fornece capacidades de remoing. O `Microsoft.ServiceFabric.Services.Remoting.Runtime` espaço de nome contém o método de extensão para `CreateServiceRemotingInstanceListeners` serviços apátridas e stateful que podem ser usados para criar um ouvinte de remoing usando o protocolo de transporte de remoing padrão.

>[!NOTE]
>O `Remoting` espaço de nome está disponível como um pacote NuGet separado chamado `Microsoft.ServiceFabric.Services.Remoting` .

Por exemplo, o seguinte serviço apátrida expõe um único método para obter "Hello World" sobre uma chamada de procedimento remoto.

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
> Os argumentos e os tipos de devolução na interface de serviço podem ser quaisquer tipos simples, complexos ou personalizados, mas devem ser capazes de ser serializados pelo .NET [DataContractSerializer](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer).
>
>

## <a name="call-remote-service-methods"></a>Ligue para métodos de serviço remoto

Os métodos de chamada de um serviço utilizando a pilha de remoting são feitos usando um representante local para o serviço através da `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` classe. O `ServiceProxy` método cria um proxy local utilizando a mesma interface que o serviço implementa. Com esse proxy, pode ligar para os métodos da interface remotamente.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

O quadro de remoing propaga exceções lançadas pelo serviço ao cliente. Como resultado, quando `ServiceProxy` é utilizado, o cliente é responsável pelo tratamento das exceções lançadas pelo serviço.

## <a name="service-proxy-lifetime"></a>Vida útil de procuração de serviço

A criação de procuração de serviço é uma operação leve, para que possa criar quantas precisar. As instâncias de procuração de serviço podem ser reutilizadas durante o tempo que forem necessárias. Se uma chamada de procedimento remoto abrir uma exceção, ainda pode reutilizar a mesma instância de procuração. Cada procuração de serviço contém um cliente de comunicação usado para enviar mensagens pelo fio. Ao invocar chamadas remotas, são realizadas verificações internas para determinar se o cliente de comunicação é válido. Com base nos resultados dessas verificações, o cliente de comunicação é recriado se necessário. Portanto, se ocorrer uma exceção, não é necessário recriar `ServiceProxy` .

### <a name="service-proxy-factory-lifetime"></a>Vida útil da fábrica de procuração de serviço

[ServiceProxyFactory](/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) é uma fábrica que cria instâncias de procuração para diferentes interfaces de remoting. Se utilizar a API `ServiceProxyFactory.CreateServiceProxy` para criar um representante, a estrutura cria um representante de serviço singleton.
É útil criar um manualmente quando é necessário sobrepor as propriedades [IServiceRemotingClientFactory.](/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory)
A criação de fábricas é uma operação dispendiosa. Uma fábrica de procuração de serviço mantém uma cache interna do cliente de comunicação.
Uma boa prática é cache a fábrica de procuração de serviço durante o maior tempo possível.

## <a name="remoting-exception-handling"></a>Tratamento de exceções de remoing

Todas as exceções remotas lançadas pela API de serviço são enviadas de volta para o cliente como AggregateException. Exceções remotas devem ser serializadas por DataContract. Se não o forem, a API proxy lança [o ServiceException](/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) com o erro de serialização nele.

O proxy de serviço lida com todas as exceções de failover para a partição de serviço para a qual é criada. Reensi para o efeito dos pontos finais se houver exceções por falhas (exceções não transitórias) e recauchutado a chamada com o ponto final correto. O número de recauchutagens por exceções por falhas é indefinido.
Se ocorrerem exceções transitórias, o representante recauchutado retira a chamada.

Os parâmetros de relagem predefinidos são fornecidos por [OperaçãoRerySettings](/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Um utilizador pode configurar estes valores passando o objeto OperationRetrySettings para o construtor ServiceProxyFactory.

## <a name="use-the-remoting-v2-stack"></a>Use a pilha V2 de remoing

A partir da versão 2.8 do pacote de remoing NuGet, tem a opção de utilizar a pilha V2 de remoting. A pilha V2 de remoing tem um melhor desempenho. Também fornece funcionalidades como serialização personalizada e APIs mais pluggable.
O código do modelo continua a utilizar a pilha V1 de remoting.
O remoting V2 não é compatível com V1 (a pilha de remoting anterior). Siga as instruções do artigo  [Faça upgrade de V1 para V2](#upgrade-from-remoting-v1-to-remoting-v2) para evitar efeitos na disponibilidade do serviço.

As seguintes abordagens estão disponíveis para ativar a pilha V2.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Use um atributo de montagem para usar a pilha V2

Estes passos alteram o código do modelo para utilizar a pilha V2 utilizando um atributo de montagem.

1. Altere o recurso ponto final de `"ServiceEndpoint"` para dentro do manifesto de `"ServiceEndpointV2"` serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Utilize o `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners`  método de extensão para criar ouvintes de remoing (igual tanto para V1 como V2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Marque o conjunto que contém as interfaces de remoting com um `FabricTransportServiceRemotingProvider` atributo.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Não são necessárias alterações de código no projeto do cliente.
Construa o conjunto do cliente com o conjunto de interfaces para se certificar de que o atributo de montagem anteriormente indicado é utilizado.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Use aulas de V2 explícitas para usar a pilha V2

Como alternativa à utilização de um atributo de montagem, a pilha V2 também pode ser ativada utilizando classes V2 explícitas.

Estes passos alteram o código de modelo para usar a pilha V2 utilizando classes V2 explícitas.

1. Altere o recurso ponto final de `"ServiceEndpoint"` para dentro do manifesto de `"ServiceEndpointV2"` serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Utilize [o FabricTransportServiceRemotingListener](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet) do `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` espaço de nomes.

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

3. Utilize [o FabricTransportServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) a partir do `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` espaço de nomes para criar clientes.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Upgrade de V1 de remoing para V2 remoting

Para atualizar de V1 para V2, são necessárias atualizações em duas etapas. Siga os passos desta sequência.

1. Atualize o serviço V1 para o serviço V2 utilizando este atributo.
Esta alteração garante que o serviço ouve o ouvinte V1 e V2.

    a. Adicione um recurso de ponto final com o nome "ServiceEndpointV2" no manifesto de serviço.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Utilize o seguinte método de extensão para criar um ouvinte de remoting.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Adicione um atributo de montagem em interfaces de remoagem para utilizar o ouvinte V1 e V2 e o cliente V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Atualize o cliente V1 a um cliente V2 utilizando o atributo do cliente V2.
Este passo garante que o cliente usa a pilha V2.
Não é necessária qualquer alteração no projeto/serviço do cliente. Construir projetos de clientes com montagem de interface atualizada é suficiente.

3. Este passo é opcional. Utilize o atributo do ouvinte V2 e, em seguida, atualize o serviço V2.
Este passo garante que o serviço está a ouvir apenas no ouvinte V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Utilize a pilha V2 de remoting (compatível com interface)

 A pilha de remoting V2 (compatível com interface, conhecida como V2_1) tem todas as características da pilha de remoing V2. A sua stack de interface é compatível com a pilha V1 de remoting, mas não é compatível com V2 e V1. Para atualizar de V1 para V2_1 sem afetar a disponibilidade do serviço, siga os passos no artigo Upgrade de V1 para V2 (compatível com interface).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Utilize um atributo de montagem para utilizar a pilha V2 (compatível com interface)

Siga estes passos para mudar para uma pilha de V2_1.

1. Adicione um recurso de ponto final com o nome "ServiceEndpointV2_1" no manifesto de serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Utilize o método de extensão de remoing para criar um ouvinte de remoing.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Adicione um [atributo de montagem](/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) em interfaces de remoagem.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Não são necessárias alterações no projeto do cliente.
Construa o conjunto do cliente com o conjunto de interfaces para se certificar de que o atributo de montagem anterior está a ser utilizado.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Utilize aulas explícitas de remoing para criar uma fábrica de ouvintes/clientes para a versão V2 (compatível com interface)

Siga estes passos:

1. Adicione um recurso de ponto final com o nome "ServiceEndpointV2_1" no manifesto de serviço.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Utilize o [ouvinte V2 de remoing](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). O nome de recurso de ponto final de serviço padrão utilizado é "ServiceEndpointV2_1". Deve ser definido no manifesto de serviço.

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

3. Utilize a [fábrica de clientes](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)V2.
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Upgrade de V1 de remoting para V2 remoting (compatível com interface)

Para atualizar de V1 para V2 (interface compatível, conhecido como V2_1), são necessárias atualizações em duas etapas. Siga os passos desta sequência.

> [!NOTE]
> Ao atualizar de V1 para V2, certifique-se de que o `Remoting` espaço de identificação é atualizado para utilizar o V2. Exemplo: 'Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client'
>
>

1. Atualize o serviço V1 para V2_1 serviço utilizando o seguinte atributo.
Esta alteração garante que o serviço está a ouvir no V1 e no ouvinte V2_1.

    a. Adicione um recurso de ponto final com o nome "ServiceEndpointV2_1" no manifesto de serviço.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Utilize o seguinte método de extensão para criar um ouvinte de remoting.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Adicione um atributo de montagem em interfaces de remoting para usar o V1, V2_1 ouvinte e V2_1 cliente.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Atualize o cliente V1 para o cliente V2_1 utilizando o atributo V2_1 cliente.
Este passo garante que o cliente está a usar a pilha de V2_1.
Não é necessária qualquer alteração no projeto/serviço do cliente. Construir projetos de clientes com montagem de interface atualizada é suficiente.

3. Este passo é opcional. Retire a versão do ouvinte V1 do atributo e, em seguida, atualize o serviço V2.
Este passo garante que o serviço está a ouvir apenas no ouvinte V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Use serialização personalizada com uma mensagem embrulhada de remoting

Para uma mensagem embrulhada de remoing, criamos um único objeto embrulhado com todos os parâmetros como campo.
Siga estes passos:

1. Implemente a `IServiceRemotingMessageSerializationProvider` interface para fornecer implementação para serialização personalizada.
    Este código mostra como é a implementação.

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

2. Substitua o fornecedor de serialização predefinido com `JsonSerializationProvider` um ouvinte de remoting.

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

3. Substitua o fornecedor de serialização padrão `JsonSerializationProvider` com uma fábrica de clientes de remoing.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Passos seguintes

* [Web API com OWIN em Serviços Fiáveis](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Comunicação da Fundação de Comunicação do Windows com Serviços Fiáveis](service-fabric-reliable-services-communication-wcf.md)
* [Comunicação segura para serviços fiáveis](service-fabric-reliable-services-secure-communication.md)
