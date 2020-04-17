---
title: Como usar o WebJobs SDK
description: Saiba mais sobre como escrever código para o WebJobs SDK. Criar empregos de processamento de fundo orientados por eventos que acedam a dados em serviços Azure e de terceiros.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: a1fd22772e72cba4cce3f9fa2751dc0df0e15bb9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535603"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>How to use the Azure WebJobs SDK for event-driven background processing (Como utilizar o SDK de WebJobs do Azure para processamento em segundo plano condicionado por eventos)

Este artigo fornece orientações sobre como trabalhar com o Azure WebJobs SDK. Para começar com webJobs imediatamente, veja [Get started com o Azure WebJobs SDK para processamento de fundo orientado para eventos](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Versões WebJobs SDK

Estas são as principais diferenças entre a versão 3. *x* e versão 2. *x* do WebJobs SDK:

* Versão 3. *x* adiciona suporte para .NET Core.
* Na versão 3. *x,* é necessário instalar explicitamente a extensão de encadernação de armazenamento exigida pelo WebJobs SDK. Na versão 2. *x,* as encadernações de armazenamento foram incluídas no SDK.
* Ferramenta seleção do Estúdio Visual para .NET Core (3.* x*) os projetos diferem da ferramenta para o Quadro .NET (2.* x*) projetos. Para saber mais, consulte [Desenvolver e implementar WebJobs utilizando o Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md).

Quando possível, são fornecidos exemplos para ambas as versões 3. *x* e versão 2. *x*.

> [!NOTE]
> [A Azure Functions](../azure-functions/functions-overview.md) é construída no WebJobs SDK, e este artigo fornece links para a documentação de Funções Azure para alguns tópicos. Note estas diferenças entre funções e o WebJobs SDK:
> * Azure Functions versão 2. *x* corresponde à versão 3 do WebJobs SDK. *x,* e Funções Azure 1. *x* corresponde a WebJobs SDK 2. *x*. Os repositórios de código fonte utilizam a numeração SDK webJobs.
> * O código de amostra para as bibliotecas da classe Azure C# é `FunctionName` como o código WebJobs SDK, exceto que não precisa de um atributo num projeto WebJobs SDK.
> * Alguns tipos de encadernação são suportados apenas em Funções, como HTTP (Webhooks) e Event Grid (que é baseado em HTTP).
>
> Para mais informações, consulte [Compare as funções WebJobs SDK e Azure](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Anfitrião WebJobs

O hospedeiro é um recipiente de tempo de funcionamento para funções.  Ouve os gatilhos e chama as funções. Na versão 3. *x,* o hospedeiro é `IHost`uma implementação de . Na versão 2. *x,* você `JobHost` usa o objeto. Cria uma instância hospedeira no seu código e escreve código para personalizar o seu comportamento.

Esta é uma diferença fundamental entre usar o WebJobs SDK diretamente e usá-lo indiretamente através de Funções Azure. Nas Funções Azure, o serviço controla o anfitrião e não pode personalizar o anfitrião escrevendo código. As Funções Azure permitem personalizar o comportamento do hospedeiro através de definições no ficheiro host.json. Essas definições são cordas, não código, e isto limita os tipos de personalizações que você pode fazer.

### <a name="host-connection-strings"></a>Cordas de ligação do hospedeiro

O WebJobs SDK procura cordas de ligação azure storage e azure service bus no ficheiro local.settings.json quando executa localmente, ou no ambiente do WebJob quando corre em Azure. Por defeito, é necessária `AzureWebJobsStorage` uma definição de corda de ligação de armazenamento.  

Versão 2. *x* do SDK permite-lhe usar os seus próprios nomes para estas cordas de ligação ou armazená-los em outro lugar. Pode definir nomes em [`JobHostConfiguration`]código utilizando o , como mostrado aqui:

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

Porque a versão 3. *x* utiliza as APIs de configuração padrão .NET Core, não existe API para alterar os nomes das cordas de ligação.

### <a name="host-development-settings"></a>Configurações de desenvolvimento de anfitriões

Você pode executar o anfitrião em modo de desenvolvimento para tornar o desenvolvimento local mais eficiente. Aqui estão algumas das definições que são alteradas quando executa o modo de desenvolvimento:

| Propriedade | Definição de desenvolvimento |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`para maximizar a saída de log. |
| `Queues.MaxPollingInterval`  | Um valor baixo para garantir que os métodos de fila são acionados imediatamente.  |
| `Singleton.ListenerLockPeriod` | 15 segundos para ajudar no rápido desenvolvimento iterativo. |

O processo de habilitação do modo de desenvolvimento depende da versão SDK. 

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* utiliza as APIs centrais padrão ASP.NET. Ligue [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) para o [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) método na instância. Passe uma `development`corda chamada, como neste exemplo:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

A `JobHostConfiguration` classe `UseDevelopmentSettings` tem um método que permite o modo de desenvolvimento.  O exemplo que se segue mostra como utilizar as definições de desenvolvimento. Para `config.IsDevelopment` fazer `true` o retorno quando funciona localmente, detete uma variável ambiental local chamada `AzureWebJobsEnv` com o valor `Development`.

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

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Gerir ligações simultâneas (versão 2.* x*)

Na versão 3. *x,* o limite de ligação predefinido para ligações infinitas. Se por alguma razão precisar de alterar [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) este limite, pode usar a propriedade da [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) classe.

Na versão 2. *x,* controla o número de ligações simultâneas a um hospedeiro utilizando o [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. Em 2. *x,* deve aumentar este valor a partir do padrão de 2 antes de iniciar o seu anfitrião WebJobs.

Todos os pedidos de HTTP de saída `HttpClient` que `ServicePointManager`faça a partir de uma função utilizando o fluxo através de . Depois de atingir o `DefaultConnectionLimit` `ServicePointManager` valor definido, comece a fazer fila de pedidos antes de enviá-los. Suponha que o seu `DefaultConnectionLimit` está definido para 2 e o seu código faz 1.000 pedidos HTTP. Inicialmente, apenas dois pedidos são autorizados a passar para o Sistema Operativo. Os outros 998 estão na fila até haver espaço para eles. Isso significa `HttpClient` que o seu tempo pode sair porque parece ter feito o pedido, mas o pedido nunca foi enviado pelo S para o servidor de destino. Portanto, pode ver um comportamento que não parece fazer `HttpClient` sentido: o seu local está a demorar 10 segundos a completar um pedido, mas o seu serviço está a devolver todos os pedidos em 200 ms. 

O valor padrão para `Int32.MaxValue`aplicações ASP.NET é , e é provável que funcione bem para webJobs funcionando em um Plano de Serviço de Aplicações Básico ou superior. WebJobs normalmente precisa da definição Always On, e isso é suportado apenas por Planos básicos e superiores de serviço de aplicações.

Se o seu WebJob estiver a funcionar num Plano de Serviço de Aplicações Grátis ou Partilhado, a sua aplicação é restrita pela caixa de areia do Serviço de Aplicações, que atualmente tem um [limite de ligação de 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Com um limite de `ServicePointManager`ligação desvinculado, é mais provável que o limiar de ligação à caixa de areia seja atingido e o site será desligado. Nesse caso, `DefaultConnectionLimit` a fixação para algo mais baixo, como 50 ou 100, pode impedir que isso aconteça e ainda permitir uma entrada suficiente.

A definição deve ser configurada antes de serem feitos quaisquer pedidos HTTP. Por esta razão, o anfitrião webJobs não deve ajustar a definição automaticamente. Pode haver pedidos http que ocorrem antes do início do hospedeiro, o que pode levar a um comportamento inesperado. A melhor abordagem é definir o `Main` valor imediatamente `JobHost`no seu método antes de inicializar, como mostrado aqui:

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

As funções devem ser métodos públicos [`NoAutomaticTrigger`](#manual-triggers) e devem ter um atributo ou atributo.

### <a name="automatic-triggers"></a>Gatilhos automáticos

Os gatilhos automáticos chamam uma função em resposta a um evento. Considere este exemplo de uma função que é desencadeada por uma mensagem adicionada ao armazenamento da Fila Azure. Responde lendo uma bolha do armazenamento de Azure Blob:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

O `QueueTrigger` atributo diz o tempo de funcionamento para ligar `myqueue-items` para a função sempre que uma mensagem de fila aparece na fila. O `Blob` atributo diz o tempo de funcionao para usar a mensagem de fila para ler uma bolha no recipiente *de trabalhos de amostra.* O nome do item de `samples-workitems` bolha no recipiente é obtido diretamente a`{queueTrigger}`partir do gatilho da fila como expressão de ligação ( ).

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Gatilhos manuais

Para desencadear uma função `NoAutomaticTrigger` manualmente, utilize o atributo, como mostrado aqui:

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

O processo de ativação manual da função depende da versão SDK.

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

## <a name="input-and-output-bindings"></a>Encadernações de entrada e saída

As encadernações de entrada fornecem uma forma declarativa de disponibilizar dados de serviços Azure ou de terceiros ao seu código. As encadernações de saída fornecem uma forma de atualizar os dados. O artigo do [Get started](webjobs-sdk-get-started.md) mostra um exemplo de cada um.

Pode utilizar um valor de devolução de método para uma ligação de saída aplicando o atributo ao valor de retorno do método. Consulte o exemplo em [Utilizar o valor de devolução da função Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Tipos de encadernação

O processo de instalação e gestão de tipos de encadernação depende se está a utilizar a versão 3. *x* ou versão 2. *x* do SDK. Pode encontrar a embalagem para instalar para um tipo de ligação específico na secção "Pacotes" do artigo de [referência](#binding-reference-information)das Funções Azure desse tipo de ligação . Uma exceção é o gatilho e a ligação dos Ficheiros (para o sistema de ficheiros local), que não é suportado pelas Funções Azure.

#### <a name="version-3x"></a>Versão 3. *x*

Na versão 3. *x,* as encadernações `Microsoft.Azure.WebJobs.Extensions.Storage` de armazenamento estão incluídas na embalagem. Ligue `AddAzureStorage` para o `ConfigureWebJobs` método de extensão no método, como mostrado aqui:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para utilizar outros tipos de gatilho e encadernação, `Add<binding>` instale o pacote NuGet que os contém e ligue para o método de extensão implementado na extensão. Por exemplo, se quiser utilizar uma ligação, `Microsoft.Azure.WebJobs.Extensions.CosmosDB` instalae `AddCosmosDB`e ligue para o Azure Cosmos DB, instale e ligue, assim:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para utilizar o gatilho do Temporizador ou a ligação `AddTimers` `AddFiles` dos Ficheiros, que fazem parte dos serviços centrais, ligue para os métodos de extensão ou extensão, respectivamente.

#### <a name="version-2x"></a>Versão 2. *x*

Estes tipos de gatilho e de ligação estão incluídos na versão 2. *x* do `Microsoft.Azure.WebJobs` pacote:

* Armazenamento de blobs
* Armazenamento de filas
* Table Storage

Para utilizar outros tipos de gatilho e de ligação, `Use<binding>` instale `JobHostConfiguration` o pacote NuGet que os contém e ligue para um método no objeto. Por exemplo, se pretender utilizar um gatilho `Microsoft.Azure.WebJobs.Extensions` do `UseTimers` Temporizador, instale e ligue para o `Main` método, como mostrado aqui:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Para utilizar a ligação, instalação `Microsoft.Azure.WebJobs.Extensions` e chamada `UseFiles`dos Ficheiros.

### <a name="executioncontext"></a>Contexto de execução

WebJobs permite-lhe ligar-se a um [`ExecutionContext`]. Com esta ligação, [`ExecutionContext`] pode aceder ao parâmetro como parâmetro na sua assinatura de função. Por exemplo, o seguinte código utiliza o objeto de contexto para aceder ao ID de invocação, que pode utilizar para correlacionar todos os registos produzidos por uma determinada invocação de função.  

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

O processo de [`ExecutionContext`] ligação depende da sua versão SDK.

#### <a name="version-3x"></a>Versão 3. *x*

Ligue `AddExecutionContextBinding` para o `ConfigureWebJobs` método de extensão no método, como mostrado aqui:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

O `Microsoft.Azure.WebJobs.Extensions` pacote mencionado anteriormente também fornece um tipo de `UseCore` encadernação especial que pode registar chamando o método. Esta ligação permite [`ExecutionContext`] definir um parâmetro na sua assinatura de função, que está ativado desta forma:

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

## <a name="binding-configuration"></a>Configuração de encadernação

Pode configurar o comportamento de alguns gatilhos e encadernações. O processo de configuração depende da versão SDK.

* **Versão 3. *x*:** Definir `Add<Binding>` a configuração `ConfigureWebJobs`quando o método for chamado .
* **Versão 2. *x*:** Definir a configuração definindo propriedades `JobHost`num objeto de configuração a que passa para .

Estas definições específicas de ligação são equivalentes às configurações no ficheiro do [projeto host.json](../azure-functions/functions-host-json.md) em Funções Azure.

Pode configurar as seguintes encadernações:

* [Gatilho Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Gatilho de Hubs de Eventos](#event-hubs-trigger-configuration-version-3x)
* [Acionador do armazenamento de filas](#queue-storage-trigger-configuration)
* [Ligação SendGrid](#sendgrid-binding-configuration-version-3x)
* [Gatilho de ônibus de serviço](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configuração do gatilho Azure CosmosDB (versão 3.* x*)

Este exemplo mostra como configurar o gatilho Do Azure Cosmos DB:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para mais detalhes, consulte o artigo [de ligação azure CosmosDB.](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configuração do gatilho do gatilho do Event Hubs (versão 3.* x*)

Este exemplo mostra como configurar o gatilho dos Centros de Eventos:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para mais detalhes, consulte o artigo vinculativo do [Event Hubs.](../azure-functions/functions-bindings-event-hubs-output.md#hostjson-settings)

### <a name="queue-storage-trigger-configuration"></a>Configuração do gatilho de armazenamento de fila

Estes exemplos mostram como configurar o gatilho de armazenamento da fila:

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para mais detalhes, consulte o artigo [de encadernação](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties) de armazenamento da fila.

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

Para mais detalhes, consulte o [host.json v1.x reference](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configuração de ligação SendGrid (versão 3.* x*)

Este exemplo mostra como configurar a ligação de saída sendGrid:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para mais detalhes, consulte o artigo [de ligação sendGrid.](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)

### <a name="service-bus-trigger-configuration-version-3x"></a>Configuração do gatilho do bus de serviço (versão 3.* x*)

Este exemplo mostra como configurar o gatilho do Ônibus de Serviço:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para mais detalhes, consulte o artigo de ligação do [Bus de Serviço.](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings)

### <a name="configuration-for-other-bindings"></a>Configuração para outras ligações

Alguns tipos de gatilho e de ligação definem os seus próprios tipos de configuração personalizadas. Por exemplo, o gatilho do Ficheiro permite especificar o caminho raiz a monitorizar, como nestes exemplos:

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static async Task Main()
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
        await host.RunAsync();
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

## <a name="binding-expressions"></a>Expressões de enlace

Nos parâmetros de atributos do construtor, pode utilizar expressões que resolvem valores de várias fontes. Por exemplo, no seguinte código, `BlobTrigger` o caminho para `filename`o atributo cria uma expressão denominada . Quando utilizado para a `filename` ligação de saída, resolve-se com o nome da bolha de desencadeamento.

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

Para obter mais informações sobre expressões vinculativas, consulte [expressões e padrões vinculativos](../azure-functions/functions-bindings-expressions-patterns.md) na documentação das Funções Azure.

### <a name="custom-binding-expressions"></a>Expressões de ligação personalizadas

Por vezes, pretende especificar um nome de fila, um nome ou recipiente blob, ou um nome de mesa em código em vez de o codificar duramente. Por exemplo, é melhor especificar o `QueueTrigger` nome da fila para o atributo num ficheiro de configuração ou variável ambiental.

Pode sê-lo `NameResolver` passando um `JobHostConfiguration` objeto para o objeto. Você inclui espaços reservados em parâmetros de construção `NameResolver` de gatilho ou de ligação, e o seu código fornece os valores reais a serem usados no lugar desses espaços reservados. Identifica-se os espaços reservados rodeando-os com por cento (%) sinais, como mostrado aqui:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Este código permite-lhe usar `logqueuetest` uma fila com `logqueueprod` o nome no ambiente de teste e uma chamada em produção. Em vez de um nome de fila codificado, especifica `appSettings` o nome de uma entrada na coleção.

Há um padrão `NameResolver` que faz efeito se não fornecer um personalizado. O padrão obtém valores a partir de configurações de aplicativos ou variáveis ambientais.

Sua `NameResolver` aula obtém `appSettings`o nome da fila de, como mostrado aqui:

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

Configura o resolver utilizando a injeção de dependência. Estas amostras `using` requerem a seguinte declaração:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Adicione o resolver chamando o método de [`ConfigureServices`] extensão, [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)como neste exemplo:

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

Passe `NameResolver` a sua `JobHost` aula para o objeto, como mostrado aqui:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

O Azure Functions implementa para obter valores `INameResolver` a partir das definições da aplicação, como mostra o exemplo. Quando utilizar o WebJobs SDK diretamente, pode escrever uma implementação personalizada que obtém valores de substituição de espaço reservado a partir de qualquer fonte que prefira.

## <a name="binding-at-runtime"></a>Encadernação no tempo de execução

Se precisar de fazer algum trabalho na sua função `Queue` `Blob`antes `Table`de utilizar um `IBinder` atributo de ligação como, ou , pode utilizar a interface.

O exemplo seguinte pega numa mensagem de fila de entrada e cria uma nova mensagem com o mesmo conteúdo numa fila de saída. O nome da fila de saída é definido por código no corpo da função.

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

Para mais informações, consulte Binding no tempo de [funcionamento](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) na documentação das Funções Azure.

## <a name="binding-reference-information"></a>Informações vinculativas de referência

A documentação das Funções Azure fornece informações de referência sobre cada tipo de encadernação. Encontrará as seguintes informações em cada artigo de referência vinculativo. (Este exemplo baseia-se na fila de armazenamento.)

* [Pacotes.](../azure-functions/functions-bindings-storage-queue.md) O pacote que precisa de instalar para incluir suporte para a ligação num projeto WebJobs SDK.
* [Exemplos.](../azure-functions/functions-bindings-storage-queue-trigger.md) Amostras de código. O exemplo da biblioteca da classe C# aplica-se ao WebJobs SDK. Basta omiti-lo. `FunctionName`
* [Atributos](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). Os atributos a utilizar para o tipo de ligação.
* [Configuração](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Explicações das propriedades do atributo e parâmetros do construtor.
* [Utilização](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Os tipos a que se pode ligar e informações sobre como funciona a ligação. Por exemplo: algoritmo de sondagem, processamento de fila de veneno.
  
Para obter uma lista de artigos de referência vinculativos, consulte "encadernações suportadas" nos [Gatilhos e ligações](../azure-functions/functions-triggers-bindings.md#supported-bindings) para funções azure. Nessa lista, as ligações HTTP, Webhooks e Event Grid são suportadas apenas pelas Funções Azure, e não pelo WebJobs SDK.

## <a name="disable-attribute"></a>Desativar o atributo 

O [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atributo permite controlar se uma função pode ser desencadeada. 

No exemplo seguinte, se `Disable_TestJob` a definição `1` `True` da aplicação tiver um valor ou (caso insensível), a função não será executada. Nesse caso, o tempo de funcionamento cria uma função de mensagem de registo *'Funções.TestJob' é desativada*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando altera os valores de definição de aplicações no portal Azure, o WebJob reinicia para captar a nova configuração.

O atributo pode ser declarado no parâmetro, método ou nível de classe. O nome de definição também pode conter expressões vinculativas.

## <a name="timeout-attribute"></a>Atributo timeout

O [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) atributo faz com que uma função seja cancelada se não terminar dentro de um determinado período de tempo. No exemplo seguinte, a função funcionaria durante um dia sem o atributo timeout. O tempo de paragem faz com que a função seja cancelada após 15 segundos.

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

Pode aplicar o atributo timeout a nível de classe ou método, `JobHostConfiguration.FunctionTimeout`e pode especificar um tempo de tempo global utilizando . Os intervalos de nível de classe ou de nível de método sobrepõem-se aos intervalos globais.

## <a name="singleton-attribute"></a>Atributo singleton

O [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) atributo garante que apenas uma instância de uma função corre, mesmo quando existem vários casos da aplicação web do anfitrião. Fá-lo utilizando [o bloqueio distribuído](#viewing-lease-blobs).

Neste exemplo, apenas uma `ProcessImage` instância da função funciona a qualquer momento:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Alguns gatilhos têm apoio incorporado para a gestão da moeda:

* **QueueTrigger**. Definido `JobHostConfiguration.Queues.BatchSize` `1`para .
* **ServiceBusTrigger**. Definido `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` `1`para .
* **FileTrigger**. Definido `FileProcessor.MaxDegreeOfParallelism` `1`para .

Pode utilizar estas definições para garantir que a sua função funciona como singleton numa única instância. Para garantir que apenas uma instância da função está em execução quando a aplicação web se`[Singleton(Mode = SingletonMode.Listener)]`baseia em várias instâncias, aplique um bloqueio singleton de nível de ouvinte na função (). As fechaduras do ouvinte são adquiridas quando o JobHost começa. Se três instâncias escaldadas começarem ao mesmo tempo, apenas uma das instâncias adquire o cadeado e apenas um ouvinte começa.

### <a name="scope-values"></a>Valores de âmbito

Pode especificar uma *expressão/valor* de âmbito num singleton. A expressão/valor garante que todas as execuções da função num âmbito específico serão serializadas. Implementar um bloqueio mais granular desta forma pode permitir algum nível de paralelismo para a sua função, ao mesmo tempo que serializa outras invocações conforme ditado pelos seus requisitos. Por exemplo, no seguinte código, a expressão `Region` de âmbito liga-se ao valor da mensagem de entrada. Quando a fila contém três mensagens nas regiões Leste, Leste e Oeste, respectivamente, as mensagens que têm região Leste são executadas em série enquanto a mensagem com a região oeste é executada em paralelo com as do Leste.

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

O âmbito padrão para `SingletonScope.Function`um bloqueio é , o que significa que o âmbito de bloqueio (o caminho de locação blob) está ligado ao nome de função totalmente qualificado. Para bloquear funções, `SingletonScope.Host` especificar e usar um nome de identificação de âmbito que é o mesmo em todas as funções que não pretende executar simultaneamente. No exemplo seguinte, apenas `AddItem` `RemoveItem` um exemplo de ou funciona de cada vez:

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

### <a name="viewing-lease-blobs"></a>Ver bolhas de arrendamento

O WebJobs SDK utiliza [arrendamentos de blob Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) sob as coberturas para implementar o bloqueio distribuído. As bolhas de aluguer utilizadas pela `azure-webjobs-host` Singleton podem `AzureWebJobsStorage` ser encontradas no contentor na conta de armazenamento sob o caminho "fechaduras". Por exemplo, o caminho do `ProcessImage` bloco de arrendamento para `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`o primeiro exemplo mostrado anteriormente pode ser . Todos os caminhos incluem o ID JobHost, neste caso 061851c758f04938a4426a9a9ab3869c0.

## <a name="async-functions"></a>Funções de asincronização

Para obter informações sobre como codificar funções de asincronização, consulte a documentação das [Funções Azure](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Fichas de cancelamento

Para obter informações sobre como lidar com fichas de cancelamento, consulte a documentação das Funções Azure sobre [fichas](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)de cancelamento e paragem graciosa .

## <a name="multiple-instances"></a>Múltiplas instâncias

Se a sua aplicação web funcionar em várias instâncias, um WebJob contínuo corre em cada instância, ouvindo os gatilhos e as funções de chamada. As várias encadernações do gatilho são projetadas para partilhar eficazmente o trabalho em conjunto entre instâncias, de modo que o escalonamento para mais instâncias permite-lhe lidar com mais carga.

Embora alguns gatilhos possam resultar em duplo processamento, filas e botões de armazenamento de bolhas, impedindo automaticamente uma função de processar uma mensagem de fila ou uma bolha mais de uma vez. Para mais informações, consulte [Design para obter informações idênticas](../azure-functions/functions-idempotent.md) na documentação das Funções Azure.

O gatilho do temporizador garante automaticamente que apenas uma instância do temporizador funciona, para que não obtenha mais do que uma instância de função a funcionar num dado horário programado.

Se quiser garantir que apenas uma instância de uma função funciona mesmo quando existem [`Singleton`](#singleton-attribute) várias instâncias da aplicação web do anfitrião, pode utilizar o atributo.

## <a name="filters"></a>Filtros

Os Filtros de Função (pré-visualização) fornecem uma forma de personalizar o pipeline de execução WebJobs com a sua própria lógica. Os filtros são semelhantes aos [filtros ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Pode implementá-los como atributos declarativos que são aplicados às suas funções ou classes. Para mais informações, consulte Filtros de [Função](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

Recomendamos o quadro de exploração madeireira que foi desenvolvido para ASP.NET. O artigo do [Get começou](webjobs-sdk-get-started.md) mostra como usá-lo. 

### <a name="log-filtering"></a>Filtragem de log

Cada registo criado `ILogger` por um `Category` caso `Level`tem um associado e . [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)é uma enumeração, e o código inteiro indica importância relativa:

|Nível de Registo    |Código|
|------------|---|
|Rastreio       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítica    | 5 |
|Nenhuma        | 6 |

Pode filtrar de forma [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)independente cada categoria para um determinado . Por exemplo, você pode querer ver todos os registos para processamento de gatilho blob, mas apenas `Error` e mais alto para todo o resto.

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* do SDK depende da filtragem incorporada em .NET Core. A `LogCategories` classe permite definir categorias para funções específicas, gatilhos ou utilizadores. Também define filtros para estados `Startup` anfitriões específicos, como e `Results`. Isto permite-lhe afinar a saída de exploração madeireira. Se não for encontrada correspondência nas categorias definidas, o filtro volta ao `Default` valor ao decidir se filtra a mensagem.

`LogCategories`requer a seguinte declaração de utilização:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

O exemplo seguinte constrói um filtro que, por padrão, `Warning` filtra todos os troncos ao nível. As `Function` `results` e categorias `Host.Results` (equivalentes à versão 2.* x*) são filtrados ao `Error` nível. O filtro compara a categoria atual a `LogCategories` todos os níveis registados no caso e escolhe a correspondência mais longa. Isto significa `Debug` que o `Host.Triggers` `Host.Triggers.Queue` nível registado para jogos ou `Host.Triggers.Blob`. Isto permite-lhe controlar categorias mais amplas sem precisar de adicionar cada uma.

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

Na versão 2. *x* do SDK, você `LogCategoryFilter` usa a classe para controlar a filtragem. O `LogCategoryFilter` tem `Default` uma propriedade com `Information`um valor inicial `Information`de, `Error`o `Critical` que significa que quaisquer mensagens `Trace` nos níveis `Warning`, ou níveis são registados, mas quaisquer mensagens nos `Debug` níveis ou níveis são filtradas.

Como `LogCategories` na versão 3. *x,* `CategoryLevels` a propriedade permite especificar níveis de registo para categorias específicas para que possa afinar a saída de registo. Se não for encontrada `CategoryLevels` correspondência dentro do dicionário, `Default` o filtro volta ao valor ao decidir se filtra a mensagem.

O exemplo seguinte constrói um filtro que por padrão `Warning` filtra todos os troncos ao nível. As `Function` `Host.Results` e categorias são `Error` filtradas ao nível. O `LogCategoryFilter` compara a categoria `CategoryLevels` atual a todos os inscritos e escolhe a correspondência mais longa. Assim, `Debug` o nível registado para `Host.Triggers` corresponder á corresponder `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Isto permite-lhe controlar categorias mais amplas sem precisar de adicionar cada uma.

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

### <a name="custom-telemetry-for-application-insights"></a>Telemetria personalizada para Insights de Aplicação

O processo de implementação da telemetria personalizada para Insights de [Aplicação](../azure-monitor/app/app-insights-overview.md) depende da versão SDK. Para aprender a configurar insights de aplicação, consulte adicionar insights de [aplicação a registar](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versão 3. *x*

Porque a versão 3. *x* do WebJobs SDK depende do hospedeiro genérico .NET Core, uma fábrica de telemetria personalizada já não é fornecida. Mas pode adicionar telemetria personalizada ao oleoduto usando a injeção de dependência. Os exemplos desta secção `using` requerem as seguintes declarações:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

A seguinte implementação personalizada de [`ITelemetryInitializer`] [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) permite adicionar [`TelemetryConfiguration`]o seu ao padrão .

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Chame [`ConfigureServices`] o construtor para [`ITelemetryInitializer`] adicionar o seu costume ao oleoduto.

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Quando [`TelemetryConfiguration`] o é construído, todos [`ITelemetryInitializer`] os tipos registados estão incluídos. Para saber mais, consulte [a Application Insights API para eventos e métricas personalizadas.](../azure-monitor/app/api-custom-events-metrics.md)

Na versão 3. *x,* já não é [`TelemetryClient`] preciso descarregar o quando o hospedeiro para. O sistema de injeção de dependência do núcleo `ApplicationInsightsLoggerProvider`.NET elimina [`TelemetryClient`]automaticamente o registado, que descarrega o .

#### <a name="version-2x"></a>Versão 2. *x*

Na versão 2. *x*, [`TelemetryClient`] criado internamente pelo fornecedor de Insights de [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)Aplicação para as utilizações do WebJobs SDK . Quando o ponto final do Application Insights não está disponível ou a estrangular os pedidos de entrada, este canal [guarda pedidos no sistema de ficheiros da aplicação web e reenvia-os mais tarde](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

O [`TelemetryClient`] é criado por uma `ITelemetryClientFactory`classe que implementa. Por defeito, [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)este é o .

Se quiser modificar qualquer parte do pipeline Application Insights, `ITelemetryClientFactory`pode fornecer o seu próprio [`TelemetryClient`], e o anfitrião usará a sua classe para construir a . Por exemplo, este `DefaultTelemetryClientFactory` código substitui para `ServerTelemetryChannel`modificar uma propriedade de:

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

O `SamplingPercentageEstimatorSettings` objeto configura a [amostragem adaptável.](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) Isto significa que em certos cenários de grande volume, o Applications Insights envia um subconjunto selecionado de dados de telemetria para o servidor.

Depois de criar a fábrica de telemetria, passe-a para o fornecedor de registo sinuoso de Insights de Aplicação:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a>Próximos passos

Este artigo forneceu fragmentos de código que mostram como lidar com cenários comuns para trabalhar com o WebJobs SDK. Para obter amostras completas, consulte [as amostras azure-webjobs-sdk .](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost)

['Contexto de execução']: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
['TelemettryClient']: /dotnet/api/microsoft.applicationinsights.telemetryclient
['Serviços de Configuração']: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemettryInitializer']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
['TelemetriaConfiguration']: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostConfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
