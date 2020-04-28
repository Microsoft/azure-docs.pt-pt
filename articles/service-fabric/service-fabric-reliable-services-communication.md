---
title: Visão geral da comunicação de Serviços Fiáveis
description: Visão geral do modelo de comunicação de Serviços Fiáveis, incluindo a abertura de ouvintes em serviços, a resolução de pontos finais e a comunicação entre serviços.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 3c1a6cfa5227369bf1cde4af087019727c22c0c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75462950"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Como utilizar as APIs de comunicação de Serviços Fiáveis
O Azure Service Fabric como plataforma é completamente agnóstico em relação à comunicação entre serviços. Todos os protocolos e pilhas são aceitáveis, desde a UDP até http. Cabe ao desenvolvedor de serviços escolher como os serviços devem comunicar. A estrutura de aplicação de Serviços Fiáveis fornece pilhas de comunicação incorporadas, bem como APIs que pode usar para construir os seus componentes de comunicação personalizados.

## <a name="set-up-service-communication"></a>Configurar a comunicação de serviço
A API de Serviços Fiáveis utiliza uma interface simples para comunicação de serviço. Para abrir um ponto final para o seu serviço, basta implementar esta interface:

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

Em seguida, pode adicionar a sua implementação de ouvinte de comunicação devolvendo-a num método de classe baseado em serviço.

Para serviços apátridas:

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

Para serviços estatais:

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

Em ambos os casos, devolve-se uma coleção de ouvintes. Isto permite que o seu serviço ouça vários pontos finais, potencialmente utilizando diferentes protocolos, utilizando vários ouvintes. Por exemplo, pode ter um ouvinte HTTP e um ouvinte WebSocket separado. Cada ouvinte obtém um nome, e a recolha de nome resultante : os pares de *endereços* são representados como um objeto JSON quando um cliente solicita os endereços de escuta para uma instância de serviço ou uma partição.

Num serviço apátrida, a substituição devolve uma coleção de ServiceInstanceListeners. A `ServiceInstanceListener` contém uma função para criar um `ICommunicationListener(C#) / CommunicationListener(Java)` e dá-lhe um nome. Para serviços estatais, a substituição devolve uma coleção de ServiceReplicaListeners. Isto é ligeiramente diferente do seu `ServiceReplicaListener` homólogo apátrida, porque um tem a opção de abrir uma `ICommunicationListener` réplica secundária. Não só pode utilizar vários ouvintes de comunicação num serviço, como também pode especificar quais os ouvintes que aceitam pedidos em réplicas secundárias e quais os que ouvem apenas em réplicas primárias.

Por exemplo, pode ter um ServiceRemotingListener que recebe chamadas RPC apenas em réplicas primárias, e um segundo ouvinte personalizado que leva pedidos de leitura sobre réplicas secundárias em HTTP:

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
> Ao criar vários ouvintes para um serviço, cada ouvinte **deve** ter um nome único.
>
>

Por fim, descreva os pontos finais necessários para o serviço no manifesto de [serviço](service-fabric-application-and-service-manifests.md) sob a secção em pontos finais.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

O ouvinte de comunicação pode aceder aos recursos `CodePackageActivationContext` finais que lhe são atribuídos a `ServiceContext`partir do . O ouvinte pode então começar a ouvir pedidos quando é aberto.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Os recursos endpoint são comuns a todo o pacote de serviços, e são atribuídos pela Service Fabric quando o pacote de serviço é ativado. Várias réplicas de serviço alojadas no mesmo ServiceHost podem partilhar a mesma porta. Isto significa que o ouvinte de comunicação deve apoiar a partilha de portas. A forma recomendada de o fazer é que o ouvinte de comunicação utilize o ID da partilha e o ID de réplica/instância quando gera o endereço de escuta.
>
>

### <a name="service-address-registration"></a>Registo de endereço de serviço
Um serviço de sistema chamado *Serviço de Naming* funciona em clusters de Tecido de Serviço. O Serviço de Nomeação é um registo de serviços e seus endereços que cada instância ou réplica do serviço está a ouvir. Quando `OpenAsync(C#) / openAsync(Java)` o método `ICommunicationListener(C#) / CommunicationListener(Java)` de um completo, o seu valor de devolução é registado no Serviço de Nomeação. Este valor de retorno que é publicado no Serviço de Nomeação é uma cadeia cujo valor pode ser qualquer coisa. Este valor de cadeia é o que os clientes vêem quando pedem uma morada para o serviço do Serviço de Nomeação.

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

O Service Fabric fornece APIs que permitem aos clientes e outros serviços pedir este endereço pelo nome do serviço. Isto é importante porque o endereço de serviço não é estático. Os serviços são movidos no cluster para fins de equilíbrio e disponibilidade de recursos. Este é o mecanismo que permite aos clientes resolver o endereço de escuta para um serviço.

> [!NOTE]
> Para obter uma completa apresentação de como escrever um ouvinte de comunicação, consulte os serviços de API da Web do [Serviço fabrica com auto-hospedagem OWIN](service-fabric-reliable-services-communication-webapi.md) para C#, enquanto para Java pode escrever a sua própria implementação de servidor HTTP, consulte o exemplo da aplicação EchoServer em https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Comunicar com um serviço
A API de Serviços Fiáveis fornece as seguintes bibliotecas para escrever clientes que comunicam com serviços.

### <a name="service-endpoint-resolution"></a>Resolução do ponto final do serviço
O primeiro passo para a comunicação com um serviço é resolver um endereço final da partição ou instância do serviço com que pretende falar. A `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` classe de utilidade é um primitivo básico que ajuda os clientes a determinar o ponto final de um serviço em tempo de execução. Na terminologia do Tecido de Serviço, o processo de determinação do ponto final de um serviço é referido como a *resolução*do ponto final do serviço .

Para ligar a serviços dentro de um cluster, o ServicePartitionResolver pode ser criado usando definições predefinidas. Esta é a utilização recomendada para a maioria das situações:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Para ligar a serviços num cluster diferente, um ServicePartitionResolver pode ser criado com um conjunto de pontos finais de gateway cluster. Note que os pontos finais do gateway são apenas pontos finais diferentes para a ligação ao mesmo cluster. Por exemplo:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternativamente, `ServicePartitionResolver` pode ser dada `FabricClient` uma função para criar um a utilizar internamente:

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

`FabricClient`é o objeto que é usado para comunicar com o cluster Service Fabric para várias operações de gestão no cluster. Isto é útil quando se quer mais controlo sobre como uma divisória de serviço interage com o seu cluster. `FabricClient`executa o cache internamente e é geralmente caro de `FabricClient` criar, por isso é importante reutilizar os casos tanto quanto possível.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Um método de resolução é então utilizado para recuperar o endereço de um serviço ou uma partição de serviço para serviços divididos.

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

Um endereço de serviço pode ser resolvido facilmente usando um ServicePartitionResolver, mas é necessário mais trabalho para garantir que o endereço resolvido pode ser usado corretamente. O seu cliente precisa de detetar se a tentativa de ligação falhou devido a um erro transitório e pode ser novamente tentada (por exemplo, o serviço deslocado ou está temporariamente indisponível), ou um erro permanente (por exemplo, o serviço foi apagado ou o recurso solicitado já não existe). As instâncias de serviço ou réplicas podem mover-se do nó para o nó a qualquer momento por múltiplas razões. O endereço de serviço resolvido através do ServicePartitionResolver pode estar velho quando o seu código cliente tentar ligar. Nesse caso, mais uma vez, o cliente precisa de reresolver o endereço. O fornecimento `ResolvedServicePartition` do anterior indica que o resolver precisa de tentar novamente em vez de simplesmente recuperar um endereço em cache.

Normalmente, o código do cliente não precisa de trabalhar diretamente com o ServicePartition. É criado e passado para as fábricas de clientes de comunicação na API de Serviços Fiáveis. As fábricas usam o resolver internamente para gerar um objeto cliente que pode ser usado para comunicar com serviços.

### <a name="communication-clients-and-factories"></a>Clientes e fábricas de comunicação
A biblioteca da fábrica de comunicação implementa um padrão típico de retry de manipulação de falhas que facilita a reexperimentação das ligações a pontos finais de serviço resolvidos. A biblioteca da fábrica fornece o mecanismo de retry enquanto fornece os manipuladores de erros.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`define a interface base implementada por uma fábrica de clientes de comunicação que produz clientes que podem falar com um serviço de Tecido de Serviço. A implementação da CommunicationClientFactory depende da pilha de comunicação utilizada pelo serviço De Serviço Fabric onde o cliente quer comunicar. A API de Serviços Fiáveis fornece um `CommunicationClientFactoryBase<TCommunicationClient>`. Isto fornece uma implementação base da interface CommunicationClientFactory e executa tarefas que são comuns a todas as pilhas de comunicação. (Estas tarefas incluem a utilização de um ServicePartitionResolver para determinar o ponto final do serviço). Os clientes geralmente implementam a classe abstrata CommunicationClientFactoryBase para lidar com lógica específica para a pilha de comunicações.

O cliente de comunicação apenas recebe um endereço e usa-o para ligar a um serviço. O cliente pode usar o protocolo que quiser.

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

A fábrica de clientes é a principal responsável pela criação de clientes de comunicação. Para clientes que não mantenham uma ligação persistente, como um cliente HTTP, a fábrica só precisa de criar e devolver o cliente. Outros protocolos que mantenham uma ligação persistente, como alguns protocolos binários, também devem ser validados pela fábrica para determinar se a ligação precisa de ser recriada.  

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

Finalmente, um manipulador de exceções é responsável por determinar que medidas tomar quando ocorre uma exceção. As exceções são **categorizadas como retáveis** e não **retáveis.**

* **Exceções não retsuaíveis** simplesmente são relançadas de volta para o chamador.
* As exceções **retáveis** são ainda categorizadas em **transitórias** e **não transitórias.**
  * **Exceções transitórias** são aquelas que podem simplesmente ser rejulgadas sem reresolver o endereço final do serviço. Estes incluirão problemas transitórios de rede ou respostas de erro de serviço que não as que indicam que o endereço final do serviço não existe.
  * **Exceções não transitórias** são as que exigem que o endereço final do serviço seja reresolvido. Estas incluem exceções que indicam que o ponto final do serviço não foi alcançado, indicando que o serviço passou para um nó diferente.

A `TryHandleException` decisão toma uma decisão sobre uma determinada exceção. Se **não sabe** que decisões tomar sobre uma exceção, deve devolver **falsas.** Se **souber** que decisão tomar, deverá definir o resultado em conformidade e voltar **a ser verdadeiro.**

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
Com `ICommunicationClient(C#) / CommunicationClient(Java)`um `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, `IExceptionHandler(C#) / ExceptionHandler(Java)` e construído em `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` torno de um protocolo de comunicação, um envolve tudo junto e fornece o ciclo de resolução de endereços de endereço de manipulação de falhas e de serviço em torno destes componentes.

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
* [ASP.NET Core com Serviços Fiáveis](service-fabric-reliable-services-communication-aspnetcore.md)
* [Chamadas de procedimento remoto com serviços fiáveis remoting](service-fabric-reliable-services-communication-remoting.md)
* [Comunicação WCF utilizando Serviços Fiáveis](service-fabric-reliable-services-communication-wcf.md)
