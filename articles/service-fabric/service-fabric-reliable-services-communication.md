---
title: Descrição geral de comunicação do Reliable Services | Documentos da Microsoft
description: Descrição geral sobre o modelo de comunicação de Reliable Services, incluindo serviços de escuta de abertura em serviços, resolução de pontos finais e a comunicação entre serviços.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 49f5a74c2fcd45d03119bffbffad6fcf30e72440
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570560"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Como utilizar a APIs de comunicação de Reliable Services
O Azure Service Fabric, como uma plataforma é completamente independente sobre a comunicação entre serviços. Todos os protocolos e pilhas são aceitáveis, de UDP para HTTP. Cabe-lhe para o desenvolvedor de serviço para escolher como serviços devem se comunicar. A estrutura da aplicação de Reliable Services fornece as pilhas de comunicação incorporada, bem como as APIs que pode usar para criar os componentes da comunicação personalizado.

## <a name="set-up-service-communication"></a>Configurar a comunicação de serviço
A API de serviços fiáveis utiliza uma interface simples para a comunicação de serviço. Para abrir um ponto de extremidade para o seu serviço, simplesmente implementa essa interface:

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

Em seguida, pode adicionar sua implementação do serviço de escuta de comunicação, retorná-la numa substituição do método de classe baseadas no serviço.

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

Em ambos os casos, retornar uma coleção de serviços de escuta. Isso permite que o serviço escute em vários pontos de extremidade, potencialmente utilizando protocolos diferentes, utilizando várias escutas. Por exemplo, pode ter um serviço de escuta HTTP e um serviço de escuta separado do WebSocket. Cada serviço de escuta obtém um nome e a coleção resultante de *name: endereço* pares são representados como um objeto JSON quando um cliente solicita os endereços de escuta de uma instância de serviço ou de uma partição.

Num serviço sem estado, a substituição retorna uma coleção de ServiceInstanceListeners. R `ServiceInstanceListener` contém uma função para criar um `ICommunicationListener(C#) / CommunicationListener(Java)` e atribui-lhe um nome. Para serviços com estado, a substituição retorna uma coleção de ServiceReplicaListeners. Isso é ligeiramente diferente de sua contraparte sem monitoração de estado, uma vez que um `ServiceReplicaListener` tem uma opção para abrir um `ICommunicationListener` em réplicas secundárias. Não só pode usar vários serviços de escuta de comunicação num serviço, mas também pode especificar quais serviços de escuta aceitarem pedidos em réplicas secundárias e quais os ouvir apenas em réplicas primárias.

Por exemplo, pode ter um ServiceRemotingListener que usa chamadas RPC apenas em réplicas primárias e uma escuta de segunda, personalizada que usa leitura pedidos em réplicas secundárias, através de HTTP:

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
> Ao criar várias escutas para um serviço, cada serviço de escuta **tem** ser fornecido um nome exclusivo.
>
>

Por fim, descrevem os pontos finais que são necessários para o serviço no [manifesto do serviço](service-fabric-application-and-service-manifests.md) sob a secção sobre pontos de extremidade.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

O serviço de escuta de comunicação poderá acessar os recursos de ponto final atribuídos ao mesmo a partir da `CodePackageActivationContext` no `ServiceContext`. O serviço de escuta, em seguida, pode começar a escutar para pedidos quando for aberta.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Recursos de ponto de extremidade são comuns para o pacote de todo o serviço e são alocados pelo Service Fabric quando o pacote de serviço é ativado. Várias réplicas de serviço hospedadas no mesmo ServiceHost podem partilhar a mesma porta. Isso significa que o serviço de escuta de comunicação deve dar suporte a partilha de portas. A forma recomendada de fazer isso é para o serviço de escuta de comunicação utilizar a partição de ID e o ID de réplica/instância ao gerar o endereço de escuta.
>
>

### <a name="service-address-registration"></a>Registo de endereço do serviço
Um serviço do sistema chamado os *Naming Service* é executado em clusters do Service Fabric. O serviço de nomenclatura é uma entidade de registo para serviços e os respetivos endereços de que cada instância ou a réplica do serviço está a escutar. Quando o `OpenAsync(C#) / openAsync(Java)` método de um `ICommunicationListener(C#) / CommunicationListener(Java)` for concluído, seu retorno valor é registado no serviço de nomenclatura. Este valor de retorno que é publicado no serviço de nomenclatura é uma cadeia de caracteres cujo valor pode ser absolutamente qualquer coisa. Este valor de cadeia de caracteres é o que veem os clientes quando eles pedirem um endereço para o serviço do serviço de nomenclatura.

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

O Service Fabric fornece APIs que permitem que os clientes e outros serviços, em seguida, fazer para este endereço ao nome do serviço. Isso é importante porque o endereço do serviço não é estático. Os serviços são movidos em torno do cluster para fins de balanceamento e a disponibilidade de recursos. Esse é o mecanismo que permitem aos clientes resolver o endereço de escuta para um serviço.

> [!NOTE]
> Para ver uma introdução completa de como escrever um serviço de escuta de comunicação, consulte [serviços de API de Web de recursos de infraestrutura do serviço com hospedagem interna OWIN](service-fabric-reliable-services-communication-webapi.md) para C#, enquanto que para Java pode escrever sua própria implementação de servidor HTTP, consulte EchoServer exemplo de aplicativo no https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Comunicação com um serviço
A API de serviços fiáveis fornece as seguintes bibliotecas para gravar clientes que se comunicam com os serviços.

### <a name="service-endpoint-resolution"></a>Resolução de ponto final de serviço
É o primeiro passo para comunicação com um serviço resolver um endereço de ponto final da partição ou a instância do serviço que pretende comunicar com o. O `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` classe de utilitário é um primitivo básico que ajuda os clientes a determinar o ponto final de um serviço em tempo de execução. Na terminologia do Service Fabric, o processo de determinar o ponto final de um serviço é referido como o *resolução do ponto final de serviço*.

Para ligar aos serviços dentro de um cluster, ServicePartitionResolver pode ser criada através das predefinições. Esta é a utilização recomendada na maioria das situações:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Para ligar aos serviços num cluster diferente, um ServicePartitionResolver pode ser criada com um conjunto de pontos finais de gateway do cluster. Tenha em atenção que os pontos finais de gateway são apenas diferentes pontos de extremidade para ligar ao mesmo cluster. Por exemplo:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Em alternativa, `ServicePartitionResolver` pode ser atribuído uma função para a criação de um `FabricClient` utilizar internamente:

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

`FabricClient` é o objeto que é utilizado para comunicar com o cluster do Service Fabric para várias operações de gestão no cluster. Isto é útil quando quiser mais controle sobre como um resolvedor de partição de serviço interage com o seu cluster. `FabricClient` realiza a colocação em cache internamente e é geralmente dispendiosas de criar, pelo que é importante reutilizar `FabricClient` instâncias tanto quanto possíveis.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Um método de resolver, em seguida, é utilizado para obter o endereço de um serviço ou de uma partição de serviço para os serviços particionadas.

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

Um endereço de serviço pode ser resolvido facilmente usando um ServicePartitionResolver, mas mais de trabalho é necessária para garantir que o endereço resolvido corretamente pode ser utilizado. O cliente precise detetar se a tentativa de ligação falhou devido a um erro transitório e pode ser repetida (por exemplo, serviço movido ou está temporariamente indisponível), ou um erro permanente (por exemplo, o serviço foi eliminado ou o recurso solicitado já não existe). Instâncias de serviço ou de réplicas podem mover em torno de nó para nó em qualquer altura por vários motivos. O endereço do serviço resolvido por meio de ServicePartitionResolver poderão ficar obsoleto no momento em que seu código de cliente tenta estabelecer ligação. Nesse caso novamente o cliente tem de voltar a resolver o endereço. Fornecendo anterior `ResolvedServicePartition` indica que o resolvedor precisa tentar novamente em vez de simplesmente obter um endereço em cache.

Normalmente, o código de cliente necessário não funcionar com o ServicePartitionResolver diretamente. É criado e passado para as fábricas de cliente de comunicação na API de serviços fiáveis. As fábricas de utilizam o resolvedor internamente para gerar um objeto de cliente que pode ser utilizado para comunicar com os serviços.

### <a name="communication-clients-and-factories"></a>Clientes de comunicação e fábricas
A biblioteca de fábrica de comunicação implementa um padrão de repetição de processamento de falhas normal que facilita a repetir ligações a pontos finais de serviço resolvidos. A biblioteca de factory fornece o mecanismo de repetição enquanto fornecer os manipuladores de erro.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` Define a interface base implementada por uma fábrica de cliente de comunicação que produz os clientes que podem se comunicar com um serviço do Service Fabric. A implementação do CommunicationClientFactory depende a pilha de comunicação utilizada pelo serviço do Service Fabric, onde o cliente deseja se comunicar. A API de serviços fiáveis fornece um `CommunicationClientFactoryBase<TCommunicationClient>`. Isso fornece uma implementação de base da CommunicationClientFactory interface e executa tarefas comuns a todas as pilhas de comunicação. (Essas tarefas incluem o uso de um ServicePartitionResolver para determinar o ponto final de serviço). Os clientes normalmente implementam a classe abstrata do CommunicationClientFactoryBase para lidar com lógica específica para a pilha de comunicações.

O cliente de comunicação apenas recebe um endereço e a utiliza para ligar a um serviço. O cliente pode utilizar qualquer protocolo que ele deseja.

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

O alocador de cliente é responsável principalmente para a criação de clientes de comunicação. Para clientes que não mantêm uma ligação persistente, como um cliente HTTP, a fábrica só tem de criar e retornar o cliente. Outros protocolos que mantêm uma ligação persistente como, por exemplo, alguns protocolos binários, também devem ser validados pela fábrica para determinar se a ligação tem de ser recriadas.  

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

Por fim, um manipulador de exceção é responsável por determinar a ação a tomar quando ocorre uma exceção. Exceções são categorizadas em **repetição** e **sem repetição**.

* **Sem repetição** exceções simplesmente obterem relançadas para o chamador.
* **repetição** exceções mais são categorizadas em **transitória** e **não transitórias**.
  * **Transitório** exceções são aqueles que podem simplesmente ser repetidas sem voltar a resolver o endereço de ponto final de serviço. Estes irão incluir problemas de rede transitórios ou respostas de erro de serviço que não tenham que indicam que o endereço de ponto final de serviço não existe.
  * **Não transitórias** exceções são aqueles que exigem o endereço de ponto final de serviço ser resolvidos novamente. Estes incluem exceções que indicam que o ponto final de serviço não está acessível, que indica que o serviço foi movido para um nó diferente.

O `TryHandleException` toma uma decisão sobre uma determinada exceção. Se ele **não sabe** quais decisões a tomar sobre uma exceção, ele deverá retornar **falso**. Se ele **saber** quais decisão de fazer, deve definir o resultado em conformidade e retornar **verdadeiro**.

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
Com uma `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, e `IExceptionHandler(C#) / ExceptionHandler(Java)` criado em torno de um protocolo de comunicação, um `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` encapsula-lo a todas as ferramentas e fornece o loop de resolução de endereço de partição de serviço e de processamento de falhas em torno desses componentes.

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

## <a name="next-steps"></a>Passos Seguintes
* [ASP.NET Core com o Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Chamadas de procedimento remoto com a comunicação remota do Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Comunicação do WCF com o Reliable Services](service-fabric-reliable-services-communication-wcf.md)
