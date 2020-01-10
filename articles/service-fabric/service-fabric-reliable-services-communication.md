---
title: Visão geral da comunicação do Reliable Services
description: Visão geral do modelo de comunicação Reliable Services, incluindo a abertura de ouvintes em serviços, a resolução de pontos de extremidade e a comunicação entre serviços.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 3c1a6cfa5227369bf1cde4af087019727c22c0c2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462950"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Como usar as APIs de comunicação do Reliable Services
O Azure Service Fabric como uma plataforma é completamente independente da comunicação entre os serviços. Todos os protocolos e pilhas são aceitáveis, de UDP a HTTP. Cabe ao desenvolvedor do serviço escolher como os serviços devem se comunicar. A estrutura de aplicativo Reliable Services fornece pilhas de comunicação internas, bem como APIs que você pode usar para criar seus componentes de comunicação personalizados.

## <a name="set-up-service-communication"></a>Configurar a comunicação do serviço
A API de Reliable Services usa uma interface simples para comunicação de serviço. Para abrir um ponto de extremidade para seu serviço, basta implementar esta interface:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

Em seguida, você pode adicionar sua implementação de ouvinte de comunicação retornando-a em uma substituição de método de classe baseada em serviço.

Para serviços sem estado:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

Para serviços com estado:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Em ambos os casos, você retorna uma coleção de ouvintes. Isso permite que o serviço escute em vários pontos de extremidade, potencialmente usando protocolos diferentes, usando vários ouvintes. Por exemplo, você pode ter um ouvinte HTTP e um ouvinte WebSocket separado. Cada ouvinte recebe um nome e a coleção resultante de pares *nome: endereço* é representada como um objeto JSON quando um cliente solicita os endereços de escuta para uma instância de serviço ou uma partição.

Em um serviço sem estado, a substituição retorna uma coleção de ServiceInstanceListeners. Uma `ServiceInstanceListener` contém uma função para criar um `ICommunicationListener(C#) / CommunicationListener(Java)` e fornece um nome a ele. Para serviços com estado, a substituição retorna uma coleção de ServiceReplicaListeners. Isso é um pouco diferente de sua contraparte sem estado, porque um `ServiceReplicaListener` tem uma opção para abrir um `ICommunicationListener` em réplicas secundárias. Você não só pode usar vários ouvintes de comunicação em um serviço, mas também pode especificar quais ouvintes aceitam solicitações em réplicas secundárias e quais escutam somente em réplicas primárias.

Por exemplo, você pode ter um ServiceRemotingListener que usa chamadas RPC somente em réplicas primárias e um segundo ouvinte personalizado que usa solicitações de leitura em réplicas secundárias por HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Ao criar vários ouvintes para um serviço, cada ouvinte **deve** receber um nome exclusivo.
>
>

Por fim, descreva os pontos de extremidade necessários para o serviço no manifesto do [serviço](service-fabric-application-and-service-manifests.md) sob a seção sobre pontos de extremidade.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

O ouvinte de comunicação pode acessar os recursos de ponto de extremidade alocados a ele do `CodePackageActivationContext` no `ServiceContext`. O ouvinte pode começar a escutar solicitações quando ele for aberto.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Os recursos de ponto de extremidade são comuns a todo o pacote de serviço e são alocados por Service Fabric quando o pacote de serviço é ativado. Várias réplicas de serviço hospedadas no mesmo ServiceHost podem compartilhar a mesma porta. Isso significa que o ouvinte de comunicação deve dar suporte ao compartilhamento de porta. A maneira recomendada de fazer isso é que o ouvinte de comunicação use a ID de partição e a ID de réplica/instância ao gerar o endereço de escuta.
>
>

### <a name="service-address-registration"></a>Registro de endereço de serviço
Um serviço de sistema chamado de *serviço de nomenclatura* é executado em clusters Service Fabric. O Serviço de Nomenclatura é um registrador para serviços e seus endereços que cada instância ou réplica do serviço está escutando. Quando o método de `OpenAsync(C#) / openAsync(Java)` de um `ICommunicationListener(C#) / CommunicationListener(Java)` é concluído, seu valor de retorno é registrado no Serviço de Nomenclatura. Esse valor retornado que é publicado no Serviço de Nomenclatura é uma cadeia de caracteres cujo valor pode ser qualquer coisa. Esse valor de cadeia de caracteres é o que os clientes veem quando solicitam um endereço para o serviço do Serviço de Nomenclatura.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Service Fabric fornece APIs que permitem que clientes e outros serviços solicitem esse endereço pelo nome do serviço. Isso é importante porque o endereço do serviço não é estático. Os serviços são movidos no cluster para fins de disponibilidade e balanceamento de recursos. Esse é o mecanismo que permite que os clientes resolvam o endereço de escuta de um serviço.

> [!NOTE]
> Para obter um passo a passo completo de como escrever um ouvinte de comunicação, consulte [Service Fabric Web API Services with OWIN auto-Hosting](service-fabric-reliable-services-communication-webapi.md) for C#, enquanto para Java você pode escrever sua própria implementação de servidor http, consulte o exemplo de aplicativo EchoServer em https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Comunicando-se com um serviço
A API de Reliable Services fornece as bibliotecas a seguir para gravar clientes que se comunicam com serviços do.

### <a name="service-endpoint-resolution"></a>Resolução de ponto de extremidade de serviço
A primeira etapa para a comunicação com um serviço é resolver um endereço de ponto de extremidade da partição ou instância do serviço ao qual você deseja falar. A classe de utilitário `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` é um primitivo básico que ajuda os clientes a determinar o ponto de extremidade de um serviço em tempo de execução. Na terminologia Service Fabric, o processo de determinar o ponto de extremidade de um serviço é chamado de *resolução de ponto de extremidade de serviço*.

Para se conectar aos serviços em um cluster, o ServicePartitionResolver pode ser criado usando as configurações padrão. Esse é o uso recomendado para a maioria das situações:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Para se conectar aos serviços em um cluster diferente, um ServicePartitionResolver pode ser criado com um conjunto de pontos de extremidade de gateway de cluster. Observe que os pontos de extremidade do gateway são apenas pontos de extremidade diferentes para se conectar ao mesmo cluster. Por exemplo:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Como alternativa, `ServicePartitionResolver` pode receber uma função para criar um `FabricClient` para usar internamente:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient` é o objeto usado para se comunicar com o Cluster Service Fabric para várias operações de gerenciamento no cluster. Isso é útil quando você deseja mais controle sobre como um resolvedor de partição de serviço interage com o cluster. `FabricClient` executa o Caching internamente e é geralmente caro de criar, portanto, é importante reutilizar `FabricClient` instâncias tanto quanto possível.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Um método resolve é usado para recuperar o endereço de um serviço ou uma partição de serviço para serviços particionados.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

Um endereço de serviço pode ser resolvido facilmente usando um ServicePartitionResolver, mas é necessário mais trabalho para garantir que o endereço resolvido possa ser usado corretamente. O cliente precisa detectar se a tentativa de conexão falhou devido a um erro transitório e pode ser repetida (por exemplo, o serviço foi movido ou está temporariamente indisponível) ou um erro permanente (por exemplo, o serviço foi excluído ou o recurso solicitado não existe mais). As réplicas ou instâncias de serviço podem ser movidas do nó para o nó a qualquer momento por vários motivos. O endereço de serviço resolvido por meio de ServicePartitionResolver pode estar obsoleto no momento em que o código do cliente tenta se conectar. Nesse caso, o cliente precisa resolver o endereço novamente. Fornecer a `ResolvedServicePartition` anterior indica que o resolvedor precisa tentar novamente em vez de simplesmente recuperar um endereço armazenado em cache.

Normalmente, o código do cliente não precisa trabalhar diretamente com o ServicePartitionResolver. Ele é criado e passado para as fábricas de cliente de comunicação na API Reliable Services. As fábricas usam o resolvedor internamente para gerar um objeto de cliente que pode ser usado para se comunicar com os serviços.

### <a name="communication-clients-and-factories"></a>Clientes e fábricas de comunicação
A biblioteca do Communication Factory implementa um padrão típico de repetição de tratamento de falhas que facilita a repetição de conexões a pontos de extremidade de serviço resolvidos. A biblioteca de fábrica fornece o mecanismo de repetição enquanto você fornece os manipuladores de erro.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` define a interface base implementada por uma fábrica de cliente de comunicação que produz clientes que podem se comunicar com um serviço de Service Fabric. A implementação do CommunicationClientFactory depende da pilha de comunicação usada pelo serviço de Service Fabric em que o cliente deseja se comunicar. A API Reliable Services fornece uma `CommunicationClientFactoryBase<TCommunicationClient>`. Isso fornece uma implementação básica da interface CommunicationClientFactory e executa tarefas que são comuns a todas as pilhas de comunicação. (Essas tarefas incluem o uso de um ServicePartitionResolver para determinar o ponto de extremidade de serviço). Os clientes geralmente implementam a classe abstrata CommunicationClientFactoryBase para manipular a lógica específica para a pilha de comunicação.

O cliente de comunicação apenas recebe um endereço e o usa para se conectar a um serviço. O cliente pode usar qualquer protocolo que desejar.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

A fábrica do cliente é responsável principalmente pela criação de clientes de comunicação. Para clientes que não mantêm uma conexão persistente, como um cliente HTTP, a fábrica precisa apenas criar e retornar o cliente. Outros protocolos que mantêm uma conexão persistente, como alguns protocolos binários, também devem ser validados pela fábrica para determinar se a conexão precisa ser recriada.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Por fim, um manipulador de exceção é responsável por determinar a ação a ser tomada quando ocorre uma exceção. As exceções são categorizadas em **repetição** e **sem nova tentativa**.

* Exceções **sem nova tentativa** simplesmente são relançadas de volta para o chamador.
* exceções com **nova tentativa** são categorizadas em **transitórios** e **não transitórias**.
  * As exceções **transitórias** são aquelas que podem simplesmente ser repetidas sem resolver novamente o endereço do ponto de extremidade de serviço. Isso incluirá problemas de rede transitórios ou respostas de erro de serviço diferentes daqueles que indicam que o endereço do ponto de extremidade de serviço não existe.
  * Exceções **não transitórias** são aquelas que exigem que o endereço do ponto de extremidade de serviço seja resolvido novamente. Isso inclui exceções que indicam que o ponto de extremidade de serviço não pôde ser alcançado, indicando que o serviço foi movido para um nó diferente.

A `TryHandleException` toma uma decisão sobre uma determinada exceção. Se **não souber** quais decisões tomar sobre uma exceção, ela deverá retornar **false**. Se ele **souber** qual decisão deve ser tomada, ele deverá definir o resultado de acordo e retornar **true**.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Juntar tudo
Com um `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`e `IExceptionHandler(C#) / ExceptionHandler(Java)` criados com base em um protocolo de comunicação, um `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` encapsula tudo isso e fornece o loop de resolução de endereço da partição de serviço e de tratamento de falhas em volta desses componentes.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Passos seguintes
* [ASP.NET Core com Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Chamadas de procedimento remoto com Reliable Services comunicação remota](service-fabric-reliable-services-communication-remoting.md)
* [Comunicação do WCF usando o Reliable Services](service-fabric-reliable-services-communication-wcf.md)
