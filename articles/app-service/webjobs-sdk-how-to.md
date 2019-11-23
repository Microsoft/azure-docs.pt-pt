---
title: Como usar o SDK de trabalhos Web-Azure
description: Saiba mais sobre como escrever código para o SDK de trabalhos Web. Crie trabalhos de processamento em segundo plano controlados por eventos que acessam dados nos serviços do Azure e serviços de terceiros.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 67cd7f82597d306c8bf3c463d11457199aec7277
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815749"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Como usar o SDK do Azure WebJobs para processamento em segundo plano controlado por eventos

Este artigo fornece orientação sobre como trabalhar com o SDK do Azure WebJobs. Para começar a usar os trabalhos Web imediatamente, consulte Introdução ao [SDK do Azure WebJobs para processamento em segundo plano orientado a eventos](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Versões do SDK de trabalhos Web

Essas são as principais diferenças entre a versão 3. *x* e versão 2. *x* do SDK de trabalhos Web:

* Versão 3. *x* adiciona suporte para .NET Core.
* Na versão 3. *x*, você precisa instalar explicitamente a extensão de associação de armazenamento exigida pelo SDK de trabalhos Web. Na versão 2. *x*, as associações de armazenamento foram incluídas no SDK.
* Ferramentas do Visual Studio para .NET Core (3. *x*) os projetos diferem das ferramentas para .NET Framework (2. *x*) projetos. Para saber mais, confira [desenvolver e implantar trabalhos Web usando o Visual Studio-Azure app Service](webjobs-dotnet-deploy-vs.md).

Quando possível, são fornecidos exemplos para a versão 3. *x* e versão 2. *x*.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) é criado no SDK de trabalhos Web, e este artigo fornece links para Azure Functions documentação de alguns tópicos. Observe essas diferenças entre as funções e o SDK de trabalhos Web:
> * Azure Functions versão 2. *x* corresponde à versão 3 do SDK de trabalhos Web. *x*e Azure Functions 1. *x* corresponde ao SDK do webjobs 2. *x*. Os repositórios de código-fonte usam a numeração do SDK de trabalhos Web.
> * O código de exemplo C# para Azure Functions bibliotecas de classe é como o código do SDK de trabalhos Web, exceto pelo fato de que você não precisa de um atributo `FunctionName` em um projeto do SDK de trabalhos Web.
> * Alguns tipos de ligação têm suporte apenas em funções, como HTTP (WebHooks) e na grade de eventos (que é baseada em HTTP).
>
> Para obter mais informações, consulte [comparar o SDK de trabalhos Web e Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host de trabalhos Web

O host é um contêiner de tempo de execução para funções.  Ele escuta gatilhos e chama funções. Na versão 3. *x*, o host é uma implementação de `IHost`. Na versão 2. *x*, você usa o objeto `JobHost`. Você cria uma instância de host em seu código e escreve o código para personalizar seu comportamento.

Essa é uma diferença importante entre usar o SDK de trabalhos Web diretamente e usá-lo indiretamente por meio de Azure Functions. No Azure Functions, o serviço controla o host e você não pode personalizar o host escrevendo código. Azure Functions permite que você personalize o comportamento do host por meio de configurações no arquivo host. JSON. Essas configurações são cadeias de caracteres, não código, e isso limita os tipos de personalizações que você pode fazer.

### <a name="host-connection-strings"></a>Cadeias de conexão do host

O SDK de trabalhos Web procura as cadeias de conexão do armazenamento do Azure e do barramento de serviço do Azure no arquivo local. Settings. JSON quando você executa localmente ou no ambiente do WebJob quando você executa no Azure. Por padrão, uma configuração de cadeia de conexão de armazenamento chamada `AzureWebJobsStorage` é necessária.  

Versão 2. o *x* do SDK permite que você use seus próprios nomes para essas cadeias de conexão ou armazene-os em outro lugar. Você pode definir nomes no código usando o [`JobHostConfiguration`], conforme mostrado aqui:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Porque a versão 3. *x* usa as APIs de configuração padrão do .NET Core, não há API para alterar os nomes da cadeia de conexão.

### <a name="host-development-settings"></a>Configurações de desenvolvimento de host

Você pode executar o host no modo de desenvolvimento para tornar o desenvolvimento local mais eficiente. Aqui estão algumas das configurações que são alteradas quando você executa no modo de desenvolvimento:

| Propriedade | Configuração de desenvolvimento |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` para maximizar a saída de log. |
| `Queues.MaxPollingInterval`  | Um valor baixo para garantir que os métodos de fila sejam disparados imediatamente.  |
| `Singleton.ListenerLockPeriod` | 15 segundos para ajudar no desenvolvimento iterativo rápido. |

O processo para habilitar o modo de desenvolvimento depende da versão do SDK. 

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* usa as APIs de ASP.NET Core padrão. Chame o método [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) na instância de [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . Passe uma cadeia de caracteres chamada `development`, como neste exemplo:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

A classe `JobHostConfiguration` tem um método `UseDevelopmentSettings` que habilita o modo de desenvolvimento.  O exemplo a seguir mostra como usar as configurações de desenvolvimento. Para fazer `config.IsDevelopment` retornar `true` quando ele é executado localmente, defina uma variável de ambiente local chamada `AzureWebJobsEnv` com o valor `Development`.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>Gerenciando conexões simultâneas (versão 2. *x*)

Na versão 3. *x*, o limite de conexão assume como padrão conexões infinitas. Se, por alguma razão, você precisar alterar esse limite, poderá usar a propriedade [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) da classe [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) .

Na versão 2. *x*, você controla o número de conexões simultâneas com um host usando a API [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . Em 2. *x*, você deve aumentar esse valor a partir do padrão 2 antes de iniciar o host de trabalhos Web.

Todas as solicitações HTTP de saída feitas por meio de uma função usando `HttpClient` fluxo por meio de `ServicePointManager`. Depois de alcançar o valor definido em `DefaultConnectionLimit`, `ServicePointManager` iniciará as solicitações de enfileiramento antes de enviá-las. Suponha que sua `DefaultConnectionLimit` seja definida como 2 e seu código faça solicitações HTTP 1.000. Inicialmente, apenas duas solicitações são permitidas até o sistema operacional. Os outros 998 são enfileirados até que haja espaço para eles. Isso significa que seu `HttpClient` pode atingir o tempo limite porque parece ter feito a solicitação, mas a solicitação nunca foi enviada pelo sistema operacional para o servidor de destino. Você pode ver um comportamento que parece não fazer sentido: sua `HttpClient` local está levando 10 segundos para concluir uma solicitação, mas seu serviço está retornando todas as solicitações em 200 ms. 

O valor padrão para aplicativos ASP.NET é `Int32.MaxValue`, e é provável que funcione bem para trabalhos Web em execução em um plano do serviço de aplicativo básico ou superior. Os trabalhos Web normalmente precisam da configuração de Always On, e há suporte apenas para planos de serviço de aplicativo básicos e superiores.

Se seu WebJob estiver sendo executado em um plano do serviço de aplicativo gratuito ou compartilhado, seu aplicativo será restrito pela área restrita do serviço de aplicativo, que atualmente tem um [limite de conexão de 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Com um limite de conexão não associado no `ServicePointManager`, é mais provável que o limite de conexão da área restrita seja atingido e o site será desligado. Nesse caso, definir `DefaultConnectionLimit` como algo menor, como 50 ou 100, pode impedir que isso aconteça e ainda permitir uma taxa de transferência suficiente.

A configuração deve ser definida antes de qualquer solicitação HTTP ser feita. Por esse motivo, o host de trabalhos Web não deve ajustar a configuração automaticamente. Pode haver solicitações HTTP que ocorrem antes do início do host, o que pode levar a um comportamento inesperado. A melhor abordagem é definir o valor imediatamente no método `Main` antes de inicializar `JobHost`, conforme mostrado aqui:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Acionadores

As funções devem ser métodos públicos e devem ter um atributo Trigger ou o atributo [`NoAutomaticTrigger`](#manual-triggers) .

### <a name="automatic-triggers"></a>Gatilhos automáticos

Os gatilhos automáticos chamam uma função em resposta a um evento. Considere este exemplo de uma função que é disparada por uma mensagem adicionada ao armazenamento de filas do Azure. Ele responde lendo um blob do armazenamento de BLOBs do Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

O atributo `QueueTrigger` informa o tempo de execução para chamar a função sempre que uma mensagem de fila aparecer na fila de `myqueue-items`. O atributo `Blob` informa ao tempo de execução para usar a mensagem da fila para ler um blob no contêiner *Sample-WorkItems* . O conteúdo da mensagem da fila, passado para a função no parâmetro `myQueueItem`, é o nome do blob.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Gatilhos manuais

Para disparar uma função manualmente, use o atributo `NoAutomaticTrigger`, como mostrado aqui:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

O processo para disparar a função manualmente depende da versão do SDK.

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Associações de entrada e saída

As associações de entrada fornecem uma maneira declarativa de tornar os dados do Azure ou de serviços de terceiros disponíveis para seu código. As associações de saída fornecem uma maneira de atualizar os dados. O [artigo de introdução mostra](webjobs-sdk-get-started.md) um exemplo de cada um.

Você pode usar um valor de retorno de método para uma associação de saída aplicando o atributo ao valor de retorno do método. Consulte o exemplo em [usando o valor de retorno da função do Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Tipos de associação

O processo de instalação e gerenciamento de tipos de associação depende se você está usando a versão 3. *x* ou versão 2. *x* do SDK. Você pode encontrar o pacote a ser instalado para um tipo de associação específico na seção "pacotes" do [artigo de referência](#binding-reference-information)de Azure Functions do tipo de associação. Uma exceção é o gatilho de arquivos e a associação (para o sistema de arquivos local), que não tem suporte pelo Azure Functions.

#### <a name="version-3x"></a>Versão 3. *x*

Na versão 3. *x*, as associações de armazenamento são incluídas no pacote de `Microsoft.Azure.WebJobs.Extensions.Storage`. Chame o método de extensão `AddAzureStorage` no método `ConfigureWebJobs`, como mostrado aqui:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Para usar outros tipos de gatilho e de associação, instale o pacote NuGet que os contém e chame o método de extensão `Add<binding>` implementado na extensão. Por exemplo, se você quiser usar uma associação de Azure Cosmos DB, instale `Microsoft.Azure.WebJobs.Extensions.CosmosDB` e chame `AddCosmosDB`, desta forma:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Para usar o gatilho de temporizador ou a associação de arquivos, que fazem parte dos serviços principais, chame os métodos de extensão `AddTimers` ou `AddFiles`, respectivamente.

#### <a name="version-2x"></a>Versão 2. *x*

Esses tipos de gatilho e de associação estão incluídos na versão 2. *x* do pacote de `Microsoft.Azure.WebJobs`:

* Armazenamento de blobs
* Armazenamento de filas
* Table Storage

Para usar outros tipos de gatilho e de associação, instale o pacote NuGet que os contém e chame um método `Use<binding>` no objeto `JobHostConfiguration`. Por exemplo, se você quiser usar um gatilho de temporizador, instale `Microsoft.Azure.WebJobs.Extensions` e chame `UseTimers` no método `Main`, como mostrado aqui:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Para usar a associação de arquivos, instale `Microsoft.Azure.WebJobs.Extensions` e chame `UseFiles`.

### <a name="executioncontext"></a>executionContext

O webjobs permite que você se associe a um [`ExecutionContext`]. Com essa associação, você pode acessar o [`ExecutionContext`] como um parâmetro em sua assinatura de função. Por exemplo, o código a seguir usa o objeto de contexto para acessar a ID de invocação, que pode ser usada para correlacionar todos os logs produzidos por uma determinada invocação de função.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

O processo de associação ao [`ExecutionContext`] depende da versão do SDK.

#### <a name="version-3x"></a>Versão 3. *x*

Chame o método de extensão `AddExecutionContextBinding` no método `ConfigureWebJobs`, como mostrado aqui:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

O pacote de `Microsoft.Azure.WebJobs.Extensions` mencionado anteriormente também fornece um tipo de ligação especial que você pode registrar chamando o método `UseCore`. Essa associação permite definir um parâmetro de [`ExecutionContext`] em sua assinatura de função, que é habilitada da seguinte maneira:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Configuração de associação

Você pode configurar o comportamento de alguns gatilhos e associações. O processo para configurá-los depende da versão do SDK.

* **Versão 3. *x*:** defina a configuração quando o método de `Add<Binding>` for chamado em `ConfigureWebJobs`.
* **Versão 2. *x*:** defina a configuração definindo as propriedades em um objeto de configuração que você passa para `JobHost`.

Essas configurações específicas de associação são equivalentes às configurações no [arquivo de projeto host. JSON](../azure-functions/functions-host-json.md) em Azure functions.

Você pode configurar as seguintes associações:

* [Gatilho CosmosDB do Azure](#azure-cosmosdb-trigger-configuration-version-3x)
* [Gatilho de hubs de eventos](#event-hubs-trigger-configuration-version-3x)
* [Gatilho de armazenamento de filas](#queue-storage-trigger-configuration)
* [Associação de SendGrid](#sendgrid-binding-configuration-version-3x)
* [Gatilho do barramento de serviço](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configuração do gatilho CosmosDB do Azure (versão 3. *x*)

Este exemplo mostra como configurar o gatilho de Azure Cosmos DB:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação de CosmosDB do Azure](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) .

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configuração do gatilho de hubs de eventos (versão 3. *x*)

Este exemplo mostra como configurar o gatilho de hubs de eventos:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação de hubs de eventos](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) .

### <a name="queue-storage-trigger-configuration"></a>Configuração do gatilho de armazenamento de filas

Estes exemplos mostram como configurar o gatilho de armazenamento de filas:

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação de armazenamento de filas](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) .

#### <a name="version-2x"></a>Versão 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Para obter mais detalhes, consulte a [referência de host. JSON v1. x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configuração de associação SendGrid (versão 3. *x*)

Este exemplo mostra como configurar a associação de saída SendGrid:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação de SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) .

### <a name="service-bus-trigger-configuration-version-3x"></a>Configuração do gatilho do barramento de serviço (versão 3. *x*)

Este exemplo mostra como configurar o gatilho do barramento de serviço:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Para obter mais detalhes, consulte o artigo [associação do barramento de serviço](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) .

### <a name="configuration-for-other-bindings"></a>Configuração para outras associações

Alguns tipos de gatilho e de associação definem seus próprios tipos de configuração personalizados. Por exemplo, o gatilho de arquivo permite especificar o caminho raiz a ser monitorado, como nestes exemplos:

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Expressões de associação

Em parâmetros de construtor de atributo, você pode usar expressões que resolvem valores de várias fontes. Por exemplo, no código a seguir, o caminho para o atributo `BlobTrigger` cria uma expressão chamada `filename`. Quando usado para a associação de saída, `filename` resolve para o nome do blob de gatilho.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Para obter mais informações sobre expressões de associação, consulte [expressões e padrões de associação](../azure-functions/functions-bindings-expressions-patterns.md) na documentação do Azure functions.

### <a name="custom-binding-expressions"></a>Expressões de associação personalizadas

Às vezes, você deseja especificar um nome de fila, um nome de BLOB ou um contêiner ou um nome de tabela no código, em vez de codificá-lo embutidamente. Por exemplo, talvez você queira especificar o nome da fila para o atributo `QueueTrigger` em um arquivo de configuração ou variável de ambiente.

Você pode fazer isso passando um objeto `NameResolver` para o objeto `JobHostConfiguration`. Você inclui espaços reservados em parâmetros de construtor de atributos de gatilho ou de associação, e seu código de `NameResolver` fornece os valores reais a serem usados no lugar desses espaços reservados. Você identifica espaços reservados ao redor deles com percentual (%) os sinais, como mostrado aqui:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Esse código permite que você use uma fila chamada `logqueuetest` no ambiente de teste e uma chamada `logqueueprod` na produção. Em vez de um nome de fila embutido em código, você especifica o nome de uma entrada na coleção de `appSettings`.

Há um `NameResolver` padrão que entra em vigor se você não fornecer um personalizado. O padrão obtém os valores das configurações do aplicativo ou das variáveis de ambiente.

Sua classe de `NameResolver` Obtém o nome da fila de `appSettings`, conforme mostrado aqui:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Versão 3. *x*

Você configura o resolvedor usando injeção de dependência. Estes exemplos exigem a seguinte instrução de `using`:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Você adiciona o resolvedor chamando o método de extensão [`ConfigureServices`] em [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder), como neste exemplo:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

Passe sua classe `NameResolver` para o objeto `JobHost`, como mostrado aqui:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions implementa `INameResolver` para obter valores das configurações do aplicativo, conforme mostrado no exemplo. Ao usar o SDK de trabalhos Web diretamente, você pode escrever uma implementação personalizada que obtém valores de substituição de espaço reservado de qualquer fonte preferida.

## <a name="binding-at-runtime"></a>Associação em tempo de execução

Se você precisar fazer algum trabalho em sua função antes de usar um atributo de associação como `Queue`, `Blob`ou `Table`, poderá usar a interface `IBinder`.

O exemplo a seguir usa uma mensagem de fila de entrada e cria uma nova mensagem com o mesmo conteúdo em uma fila de saída. O nome da fila de saída é definido pelo código no corpo da função.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Para obter mais informações, consulte [Binding in Runtime](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) na documentação do Azure functions.

## <a name="binding-reference-information"></a>Informações de referência de associação

A documentação Azure Functions fornece informações de referência sobre cada tipo de associação. Você encontrará as seguintes informações em cada artigo de referência de associação. (Este exemplo é baseado na fila de armazenamento.)

* [Pacotes](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). O pacote que você precisa instalar para incluir suporte para a associação em um projeto do SDK de trabalhos Web.
* [Exemplos](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Exemplos de código. O C# exemplo de biblioteca de classes se aplica ao SDK de trabalhos Web. Basta omitir o atributo `FunctionName`.
* [Atributos](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Os atributos a serem usados para o tipo de associação.
* [Configuração](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Explicações das propriedades do atributo e dos parâmetros do construtor.
* [Utilização](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Os tipos aos quais você pode associar e informações sobre como a associação funciona. Por exemplo: algoritmo de sondagem, processamento de fila suspeita.
  
Para obter uma lista de artigos de referência de associação, consulte "associações com suporte" no artigo [gatilhos e associações](../azure-functions/functions-triggers-bindings.md#supported-bindings) para Azure functions. Nessa lista, as associações de grade de eventos, HTTP e WebHooks têm suporte apenas pelo Azure Functions, não pelo SDK de trabalhos Web.

## <a name="disable-attribute"></a>Desabilitar atributo 

O atributo [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) permite que você controle se uma função pode ser disparada. 

No exemplo a seguir, se a configuração do aplicativo `Disable_TestJob` tiver um valor de `1` ou `True` (não diferencia maiúsculas de minúsculas), a função não será executada. Nesse caso, o tempo de execução cria uma função de mensagem de log *"functions. TestJob" está desabilitada*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando você altera os valores de configuração do aplicativo no portal do Azure, o WebJob é reiniciado para selecionar a nova configuração.

O atributo pode ser declarado no nível de parâmetro, método ou classe. O nome da configuração também pode conter expressões de associação.

## <a name="timeout-attribute"></a>Atributo timeout

O atributo [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) faz com que uma função seja cancelada se não for concluída dentro de um período de tempo especificado. No exemplo a seguir, a função seria executada por um dia sem o atributo timeout. Timeout faz com que a função seja cancelada após 15 segundos.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Você pode aplicar o atributo timeout no nível de classe ou método e pode especificar um tempo limite global usando `JobHostConfiguration.FunctionTimeout`. Tempos limite de nível de classe ou de nível de método substituem tempos limite globais.

## <a name="singleton-attribute"></a>Atributo singleton

O atributo [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) garante que apenas uma instância de uma função seja executada, mesmo quando há várias instâncias do aplicativo Web host. Ele faz isso usando o [bloqueio distribuído](#viewing-lease-blobs).

Neste exemplo, apenas uma única instância da função `ProcessImage` é executada em um determinado momento:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Alguns gatilhos têm suporte interno para gerenciamento de simultaneidade:

* **QueueTrigger**. Defina `JobHostConfiguration.Queues.BatchSize` como `1`.
* **ServiceBusTrigger**. Defina `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` como `1`.
* **FileTrigger**. Defina `FileProcessor.MaxDegreeOfParallelism` como `1`.

Você pode usar essas configurações para garantir que sua função seja executada como um singleton em uma única instância. Para garantir que apenas uma única instância da função esteja em execução quando o aplicativo Web é dimensionado para várias instâncias, aplique um bloqueio singleton no nível do ouvinte na função (`[Singleton(Mode = SingletonMode.Listener)]`). Os bloqueios de ouvinte são adquiridos quando o JobHost é iniciado. Se três instâncias escaladas horizontalmente começarem ao mesmo tempo, apenas uma das instâncias adquirirá o bloqueio e apenas um ouvinte será iniciado.

### <a name="scope-values"></a>Valores de escopo

Você pode especificar uma *expressão/valor de escopo* em um singleton. A expressão/valor garante que todas as execuções da função em um escopo específico serão serializadas. Implementar um bloqueio mais granular dessa maneira pode permitir algum nível de paralelismo para sua função ao serializar outras invocações conforme determinado pelos seus requisitos. Por exemplo, no código a seguir, a expressão de escopo é vinculada ao valor `Region` da mensagem de entrada. Quando a fila contém três mensagens nas regiões leste, leste e oeste, respectivamente, as mensagens que têm a região leste são executadas em série enquanto a mensagem com a região oeste é executada em paralelo com aquelas no leste.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

O escopo padrão de um bloqueio é `SingletonScope.Function`, o que significa que o escopo de bloqueio (o caminho de concessão de BLOB) está vinculado ao nome da função totalmente qualificada. Para bloquear entre funções, especifique `SingletonScope.Host` e use um nome de ID de escopo que seja o mesmo em todas as funções que você não deseja executar simultaneamente. No exemplo a seguir, apenas uma instância de `AddItem` ou `RemoveItem` é executada por vez:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Exibindo blobs de concessão

O SDK de trabalhos Web usa as [concessões de blob do Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) nos bastidores para implementar o bloqueio distribuído. Os blobs de concessão usados pelo singleton podem ser encontrados no contêiner de `azure-webjobs-host` na conta de armazenamento de `AzureWebJobsStorage` no caminho "bloqueios". Por exemplo, o caminho do blob de concessão para o primeiro `ProcessImage` exemplo mostrado anteriormente pode ser `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Todos os caminhos incluem a ID JobHost, neste caso, 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Funções assíncronas

Para obter informações sobre como codificar funções assíncronas, consulte a [documentação do Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Para obter informações sobre como lidar com tokens de cancelamento, consulte a documentação do Azure Functions sobre [tokens de cancelamento e desligamento normal](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Múltiplas instâncias

Se seu aplicativo Web for executado em várias instâncias, um WebJob contínuo será executado em cada instância, escutando gatilhos e chamando funções. As várias associações de gatilho são projetadas para compartilhar com eficiência o trabalho colaborativo entre instâncias, de forma que o dimensionamento para mais instâncias permite que você manipule mais carga.

Embora alguns gatilhos possam resultar em processamento duplo, os gatilhos de armazenamento de fila e de blob impedem automaticamente que uma função processe uma mensagem de fila ou BLOB mais de uma vez. Para obter mais informações, consulte [projetando para entrada idêntica](../azure-functions/functions-idempotent.md) na documentação do Azure functions.

O gatilho de temporizador garante automaticamente que apenas uma instância do temporizador seja executada, para que você não obtenha mais de uma instância de função em execução em um determinado horário agendado.

Se você quiser garantir que apenas uma instância de uma função seja executada mesmo quando houver várias instâncias do aplicativo Web host, você poderá usar o atributo [`Singleton`](#singleton-attribute) .

## <a name="filters"></a>Filtros

Filtros de função (visualização) fornecem uma maneira de personalizar o pipeline de execução de trabalhos Web com sua própria lógica. Filtros são semelhantes a [filtros de ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Você pode implementá-los como atributos declarativos que são aplicados às suas funções ou classes. Para obter mais informações, consulte [filtros de função](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

Recomendamos a estrutura de registro em log desenvolvida para ASP.NET. O [artigo de introdução mostra](webjobs-sdk-get-started.md) como usá-lo. 

### <a name="log-filtering"></a>Filtragem de log

Cada log criado por uma instância de `ILogger` tem um `Category` e `Level`associados. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração e o código inteiro indica importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreio       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítica    | 5 |
|Nenhum        | 6 |

Você pode filtrar de forma independente cada categoria para um determinado [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel). Por exemplo, talvez você queira ver todos os logs para o processamento do gatilho de BLOB, mas apenas `Error` e superior para todo o resto.

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* do SDK depende da filtragem interna do .NET Core. A classe `LogCategories` permite que você defina categorias para funções, gatilhos ou usuários específicos. Ele também define filtros para Estados de host específicos, como `Startup` e `Results`. Isso permite que você ajuste a saída de log. Se nenhuma correspondência for encontrada nas categorias definidas, o filtro retornará ao valor `Default` ao decidir se a mensagem deve ser filtrada.

`LogCategories` requer a seguinte instrução Using:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no nível de `Warning`. As categorias `Function` e `results` (equivalente a `Host.Results` na versão 2. *x*) são filtrados no nível de `Error`. O filtro compara a categoria atual com todos os níveis registrados na instância de `LogCategories` e escolhe a correspondência mais longa. Isso significa que o nível de `Debug` registrado para `Host.Triggers` corresponde a `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

Na versão 2. *x* do SDK, você usa a classe `LogCategoryFilter` para controlar a filtragem. O `LogCategoryFilter` tem uma propriedade `Default` com um valor inicial de `Information`, o que significa que todas as mensagens nos níveis `Information`, `Warning`, `Error`ou `Critical` são registradas, mas todas as mensagens nos níveis `Debug` ou `Trace` são filtradas fora.

Assim como ocorre com `LogCategories` na versão 3. *x*, a propriedade `CategoryLevels` permite especificar níveis de log para categorias específicas, de modo que você possa ajustar a saída de log. Se nenhuma correspondência for encontrada no dicionário de `CategoryLevels`, o filtro retornará ao valor de `Default` ao decidir se deseja filtrar a mensagem.

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no nível de `Warning`. As categorias `Function` e `Host.Results` são filtradas no nível de `Error`. O `LogCategoryFilter` compara a categoria atual com todas as `CategoryLevels` registradas e escolhe a correspondência mais longa. Portanto, o nível de `Debug` registrado para `Host.Triggers` corresponderá `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Telemetria personalizada para Application Insights

O processo de implementação de telemetria personalizada para [Application insights](../azure-monitor/app/app-insights-overview.md) depende da versão do SDK. Para saber como configurar Application Insights, consulte [Adicionar log de Application insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versão 3. *x*

Porque a versão 3. *x* do SDK de trabalhos Web depende do host genérico do .NET Core, uma fábrica de telemetria personalizada não é mais fornecida. Mas você pode adicionar telemetria personalizada ao pipeline usando injeção de dependência. Os exemplos nesta seção exigem as seguintes instruções de `using`:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

A implementação personalizada do [`ITelemetryInitializer`] a seguir permite que você adicione seus próprios [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) ao [`TelemetryConfiguration`]padrão.

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Chame [`ConfigureServices`] no construtor para adicionar o [`ITelemetryInitializer`] personalizado ao pipeline.

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Quando o [`TelemetryConfiguration`] é construído, todos os tipos registrados de [`ITelemetryInitializer`] são incluídos. Para saber mais, consulte [API de Application insights para métricas e eventos personalizados](../azure-monitor/app/api-custom-events-metrics.md).

Na versão 3. *x*, você não precisa mais liberar o [`TelemetryClient`] quando o host parar. O sistema de injeção de dependência do .NET Core descarta automaticamente o `ApplicationInsightsLoggerProvider`registrado, que libera o [`TelemetryClient`].

#### <a name="version-2x"></a>Versão 2. *x*

Na versão 2. *x*, o [`TelemetryClient`] criado internamente pelo provedor de Application insights para o SDK de trabalhos Web usa [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Quando o ponto de extremidade de Application Insights não está disponível ou limitando as solicitações de entrada, esse canal [salva as solicitações no sistema de arquivos do aplicativo Web e as envia novamente mais tarde](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

O [`TelemetryClient`] é criado por uma classe que implementa `ITelemetryClientFactory`. Por padrão, essa é a [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Se você quiser modificar qualquer parte do pipeline de Application Insights, poderá fornecer seu próprio `ITelemetryClientFactory`e o host usará sua classe para construir uma [`TelemetryClient`]. Por exemplo, esse código substitui `DefaultTelemetryClientFactory` para modificar uma propriedade de `ServerTelemetryChannel`:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

O objeto `SamplingPercentageEstimatorSettings` configura a [amostragem adaptável](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Isso significa que em determinados cenários de alto volume, o Application insights envia um subconjunto selecionado de dados de telemetria para o servidor.

Depois de criar a fábrica de telemetria, passe-a para o provedor de log de Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Passos seguintes

Este artigo fornece trechos de código que mostram como lidar com cenários comuns para trabalhar com o SDK de trabalhos Web. Para obter exemplos completos, consulte [Azure-webjobs-SDK-Samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[ITelemetryInitializer]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
