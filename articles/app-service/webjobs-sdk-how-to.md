---
title: Como utilizar o WebJobs SDK
description: Saiba mais sobre como escrever código para o WebJobs SDK. Criar trabalhos de processamento de fundo orientados para eventos que acedam a dados em Azure e serviços de terceiros.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 063924dccb7d7b95b962b24ecc1af1870a855194
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617136"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>How to use the Azure WebJobs SDK for event-driven background processing (Como utilizar o SDK de WebJobs do Azure para processamento em segundo plano condicionado por eventos)

Este artigo fornece orientações sobre como trabalhar com o Azure WebJobs SDK. Para começar com webJobs imediatamente, consulte [Começar com o Azure WebJobs SDK para processamento de fundo orientado por eventos](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Versões WebJobs SDK

Estas são as principais diferenças entre a versão 3. *x* e versão 2. *x* do WebJobs SDK:

* Versão 3. *x* adiciona suporte para .NET Core.
* Na versão 3. *x*, você precisa instalar explicitamente a extensão de ligação de armazenamento exigida pelo WebJobs SDK. Na versão 2. *x*, as ligações de armazenamento foram incluídas no SDK.
* Ferramentas de Estúdio Visual para .NET Core (3.*x*) os projetos diferem da ferramenta para .NET Framework (2.*x*) projetos. Para saber mais, consulte [Desenvolver e implementar WebJobs utilizando o Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md).

Quando possível, são fornecidos exemplos para ambas as versões 3. *x* e versão 2. *x*.

> [!NOTE]
> [As Funções Azure](../azure-functions/functions-overview.md) são construídas no WebJobs SDK, e este artigo fornece links para documentação de Funções Azure para alguns tópicos. Note estas diferenças entre funções e o WebJobs SDK:
> * Azure Funções versão 2. *x* corresponde à versão 3 da WebJobs SDK. *x*, e Funções Azure 1. *x* corresponde ao WebJobs SDK 2. *x*. Os repositórios de código fonte utilizam a numeragem SDK do WebJobs.
> * O código de amostra para bibliotecas de classe Azure Functions C# é como o código WebJobs SDK, exceto que não precisa de um `FunctionName` atributo num projeto WebJobs SDK.
> * Alguns tipos de ligação são suportados apenas em Funções, como HTTP (Webhooks) e Event Grid (que é baseado em HTTP).
>
> Para obter mais informações, consulte [as funções WebJobs SDK e Azure](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>WebJobs anfitrião

O hospedeiro é um recipiente de tempo de funcionamento para funções.  Ouve funções de gatilhos e chamadas. Na versão 3. *x*, o hospedeiro é uma implementação de `IHost` . Na versão 2. *x,* usa-se o `JobHost` objeto. Cria uma instância de anfitrião no seu código e escreve código para personalizar o seu comportamento.

Esta é uma diferença fundamental entre usar o WebJobs SDK diretamente e usá-lo indiretamente através de Funções Azure. Em Funções Azure, o serviço controla o anfitrião e não é possível personalizar o anfitrião escrevendo código. As Funções Azure permitem personalizar o comportamento do anfitrião através de configurações no host.jsno ficheiro. Essas definições são cordas, não código, e isto limita os tipos de personalizações que você pode fazer.

### <a name="host-connection-strings"></a>Cadeias de conexão do anfitrião

O WebJobs SDK procura as cadeias de conexão Azure Storage e Azure Service Bus no local.settings.jsno ficheiro quando funciona localmente, ou no ambiente do WebJob quando corre em Azure. Por predefinição, é necessária uma definição de cadeia de ligação de armazenamento `AzureWebJobsStorage` nomeada.  

Versão 2. *x* do SDK permite-lhe usar os seus próprios nomes para estas cordas de ligação ou armazená-las em outro lugar. Pode definir nomes em código usando o [`JobHostConfiguration`] , como mostrado aqui:

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

> [!NOTE]
> Porque a versão 3. *x* utiliza as APIs de configuração de núcleo padrão.NET, não existe API para alterar nomes de cadeias de ligação. Ver [Desenvolver e implementar WebJobs usando o Visual Studio](webjobs-dotnet-deploy-vs.md)

### <a name="host-development-settings"></a>Configurações de desenvolvimento do anfitrião

Você pode executar o anfitrião em modo de desenvolvimento para tornar o desenvolvimento local mais eficiente. Aqui estão algumas das definições que são alteradas quando funciona no modo de desenvolvimento:

| Propriedade | Definição de desenvolvimento |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` para maximizar a saída de log. |
| `Queues.MaxPollingInterval`  | Um baixo valor para garantir que os métodos de fila são desencadeados imediatamente.  |
| `Singleton.ListenerLockPeriod` | 15 segundos para ajudar no rápido desenvolvimento iterativo. |

O processo de ativação do modo de desenvolvimento depende da versão SDK. 

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* usa as APIs core ASP.NET padrão. Ligue para o [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) método do [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) caso. Passe uma corda chamada `development` , como neste exemplo:

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

A `JobHostConfiguration` classe tem um método que permite o modo de `UseDevelopmentSettings` desenvolvimento.  O exemplo a seguir mostra como utilizar as definições de desenvolvimento. Para fazer `config.IsDevelopment` retorno quando for executado `true` localmente, deslote uma variável ambiental local com `AzureWebJobsEnv` o valor `Development` .

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

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Gestão de ligações simultâneas (versão 2.*x*)

Na versão 3. *x*, o limite de ligação predefinido às ligações infinitas. Se por alguma razão precisar de alterar este limite, pode utilizar a [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) propriedade da [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) classe.

Na versão 2. *x*, controla o número de ligações simultâneas a um anfitrião utilizando o [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. Em 2. *x*, deve aumentar este valor a partir do padrão de 2 antes de iniciar o seu anfitrião WebJobs.

Todos os pedidos HTTP de saída que escorria a partir de uma função utilizando `HttpClient` o fluxo através `ServicePointManager` . Depois de atingir o valor `DefaultConnectionLimit` definido, `ServicePointManager` inicia os pedidos de fila antes de os enviar. Suponha que o seu `DefaultConnectionLimit` está definido para 2 e o seu código faz 1.000 pedidos HTTP. Inicialmente, apenas dois pedidos são permitidos através do SO. Os outros 998 estão na fila até que haja espaço para eles. Isso significa que o seu `HttpClient` pode ter feito o tempo de ô-out porque parece ter feito o pedido, mas o pedido nunca foi enviado pelo SO para o servidor de destino. Por isso, pode ver um comportamento que não parece fazer sentido: o seu local `HttpClient` está a demorar 10 segundos a completar um pedido, mas o seu serviço está a devolver todos os pedidos em 200 ms. 

O valor padrão para aplicações ASP.NET é `Int32.MaxValue` , e é provável que funcione bem para webJobs em execução em um Plano de Serviço de Aplicações Básica ou superior. Os WebJobs normalmente precisam da definição Always On, e isso é suportado apenas por Planos básicos e superiores de serviço de aplicações.

Se o seu WebJob estiver a funcionar num Plano de Serviço de Aplicações Gratuitas ou Partilhadas, a sua aplicação é restringida pela caixa de areia do Serviço de Aplicações, que atualmente tem um [limite de ligação de 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Com um limite de ligação não `ServicePointManager` vinculado, é mais provável que o limiar de ligação da caixa de areia seja atingido e o site seja desligado. Nesse caso, a fixação `DefaultConnectionLimit` para algo mais baixo, como 50 ou 100, pode impedir que isso aconteça e ainda permitir uma produção suficiente.

A definição deve ser configurada antes de serem feitos quaisquer pedidos HTTP. Por esta razão, o anfitrião WebJobs não deve ajustar a definição automaticamente. Pode haver pedidos HTTP que ocorrem antes do início do hospedeiro, o que pode levar a um comportamento inesperado. A melhor abordagem é definir o valor imediatamente no seu `Main` método antes de `JobHost` rubricar, como mostrado aqui:

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

As funções devem ser métodos públicos e devem ter um atributo de gatilho ou o [`NoAutomaticTrigger`](#manual-triggers) atributo.

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

O `QueueTrigger` atributo diz ao tempo de execução para ligar para a função sempre que uma mensagem de fila aparece na `myqueue-items` fila. O `Blob` atributo diz ao tempo de execução para utilizar a mensagem de fila para ler uma bolha no recipiente de trabalho de *amostra.* O nome do item blob no `samples-workitems` recipiente é obtido diretamente a partir do gatilho da fila como expressão de encadernação `{queueTrigger}` ().

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Gatilhos manuais

Para ativar uma função manualmente, utilize o `NoAutomaticTrigger` atributo, como mostrado aqui:

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

As ligações de entrada fornecem uma forma declarativa de disponibilizar dados de Azure ou serviços de terceiros ao seu código. As ligações de saída fornecem uma forma de atualizar os dados. O artigo [Get start](webjobs-sdk-get-started.md) mostra um exemplo de cada um.

Pode utilizar um valor de retorno de método para uma ligação de saída aplicando o atributo ao valor de retorno do método. Veja o exemplo na [Utilização do valor de retorno da função Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Tipos de ligação

O processo de instalação e gestão de tipos de ligação depende se está a utilizar a versão 3. *x* ou versão 2. *x* do SDK. Pode encontrar a embalagem para instalar para um tipo de encadernação específico na secção "Pacotes" do artigo de [referência](#binding-reference-information)do tipo de ligação Azure Functions . Uma exceção é o gatilho de Ficheiros e a ligação (para o sistema de ficheiros local), que não é suportado por Funções Azure.

#### <a name="version-3x"></a>Versão 3. *x*

Na versão 3. *x*, as ligações de armazenamento estão incluídas na `Microsoft.Azure.WebJobs.Extensions.Storage` embalagem. Ligue para o `AddAzureStorage` método de extensão `ConfigureWebJobs` no método, como mostrado aqui:

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

Para utilizar outros tipos de gatilho e de ligação, instale o pacote NuGet que os contém e chame o `Add<binding>` método de extensão implementado na extensão. Por exemplo, se quiser utilizar uma ligação DB Azure Cosmos, instale `Microsoft.Azure.WebJobs.Extensions.CosmosDB` e `AddCosmosDB` ligue, como este:

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

Para utilizar o gatilho do Temporizador ou a ligação ficheiros, que fazem parte dos serviços essenciais, ligue para os `AddTimers` métodos ou `AddFiles` extensões, respectivamente.

#### <a name="version-2x"></a>Versão 2. *x*

Estes tipos de gatilho e de ligação estão incluídos na versão 2. *x* do `Microsoft.Azure.WebJobs` pacote:

* Armazenamento de blobs
* Armazenamento de filas
* Table Storage

Para utilizar outros tipos de gatilho e de ligação, instale o pacote NuGet que os contém e chame um `Use<binding>` método no `JobHostConfiguration` objeto. Por exemplo, se pretender utilizar um gatilho do Temporizador, instale `Microsoft.Azure.WebJobs.Extensions` e ligue `UseTimers` o `Main` método, como mostrado aqui:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Para utilizar a ligação dos Ficheiros, instale `Microsoft.Azure.WebJobs.Extensions` e ligue `UseFiles` .

### <a name="executioncontext"></a>ExecuçãoContexto

WebJobs permite-lhe ligar-se a [`ExecutionContext`] um . Com esta ligação, pode aceder ao [`ExecutionContext`] como parâmetro na sua assinatura de função. Por exemplo, o seguinte código utiliza o objeto de contexto para aceder ao ID de invocação, que pode utilizar para correlacionar todos os registos produzidos por uma determinada invocação de função.  

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

O processo de ligação ao [`ExecutionContext`] do sdk depende da sua versão SDK.

#### <a name="version-3x"></a>Versão 3. *x*

Ligue para o `AddExecutionContextBinding` método de extensão `ConfigureWebJobs` no método, como mostrado aqui:

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

O `Microsoft.Azure.WebJobs.Extensions` pacote mencionado anteriormente também fornece um tipo especial de encadernação que você pode registar chamando o `UseCore` método. Esta ligação permite definir um [`ExecutionContext`] parâmetro na sua assinatura de função, que está ativado desta forma:

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

* **Versão 3. *x*:** Definir a configuração quando o `Add<Binding>` método for chamado `ConfigureWebJobs` .
* **Versão 2. *x*:** Definir a configuração definindo as propriedades num objeto de configuração para o que passa `JobHost` .

Estas definições específicas de encadernação equivalem às definições no [host.jsno ficheiro do projeto](../azure-functions/functions-host-json.md) em Funções Azure.

Pode configurar as seguintes ligações:

* [Gatilho Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Gatilho de Centros de Eventos](#event-hubs-trigger-configuration-version-3x)
* [Acionador do armazenamento de filas](#queue-storage-trigger-configuration)
* [Ligação SendGrid](#sendgrid-binding-configuration-version-3x)
* [Gatilho do ônibus de serviço](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configuração do gatilho Azure CosmosDB (versão 3).*x*)

Este exemplo mostra como configurar o gatilho Azure Cosmos DB:

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

Para mais detalhes, consulte o artigo [de vindca Azure CosmosDB.](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configuração do gatilho do Event Hubs (versão 3).*x*)

Este exemplo mostra como configurar o gatilho do Event Hubs:

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

Para mais detalhes, consulte o artigo [de vindcação do Event Hubs.](../azure-functions/functions-bindings-event-hubs.md#host-json)

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

Para mais detalhes, consulte o artigo [de encadernação de armazenamento da fila.](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)

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

Para mais detalhes, consulte a [host.jsna referência v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configuração de ligação SendGrid (versão 3.*x*)

Este exemplo mostra como configurar a ligação de saída SendGrid:

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

Para mais detalhes, consulte o artigo [de ligação enviar a Golh.](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)

### <a name="service-bus-trigger-configuration-version-3x"></a>Configuração do gatilho do autocarro de serviço (versão 3.*x*)

Este exemplo mostra como configurar o gatilho do Service Bus:

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

Para mais detalhes, consulte o artigo [de vindca de serviço do Service Bus.](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings)

### <a name="configuration-for-other-bindings"></a>Configuração para outras encadernações

Alguns tipos de gatilho e de ligação definem os seus próprios tipos de configuração personalizada. Por exemplo, o gatilho do Ficheiro permite especificar o caminho raiz a monitorizar, como nestes exemplos:

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

Nos parâmetros de construção de atributos, pode utilizar expressões que se resolvem a valores de várias fontes. Por exemplo, no seguinte código, o caminho para o `BlobTrigger` atributo cria uma expressão chamada `filename` . Quando utilizado para a ligação de saída, `filename` resolve-se o nome da bolha de desencadeamento.

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

Para obter mais informações sobre expressões de encadernação, consulte [expressões e padrões de ligação](../azure-functions/functions-bindings-expressions-patterns.md) na documentação Azure Functions.

### <a name="custom-binding-expressions"></a>Expressões de encadernação personalizadas

Por vezes, pretende especificar um nome de fila, um nome de bolha ou recipiente, ou um nome de mesa em código em vez de codificar duramente. Por exemplo, é possível especificar o nome da fila para o `QueueTrigger` atributo num ficheiro de configuração ou variável ambiental.

Podes fazer isso passando um `NameResolver` objeto para o `JobHostConfiguration` objeto. Você inclui espaços reservados nos parâmetros de gatilho ou atributo de ligação, e o seu `NameResolver` código fornece os valores reais a serem usados no lugar desses espaços reservados. Identifica-se os espaços reservados ao rodeá-los com por cento (%) sinais, como mostrado aqui:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Este código permite-lhe utilizar uma fila nomeada `logqueuetest` no ambiente de teste e uma nomeada em `logqueueprod` produção. Em vez de um nome de fila codificado, especifica o nome de uma entrada na `appSettings` coleção.

Há um padrão `NameResolver` que faz efeito se não fornecer um personalizado. O padrão obtém valores a partir de configurações de aplicações ou variáveis ambientais.

A sua `NameResolver` turma obtém o nome da `appSettings` fila, como mostrado aqui:

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

Configura o resolver utilizando a injeção de dependência. Estas amostras requerem a seguinte `using` declaração:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Adicione o resolver chamando o método de [`ConfigureServices`]  [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) extensão, como neste exemplo:

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

Passe a sua `NameResolver` classe para o `JobHost` objeto, como mostrado aqui:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

A Azure Functions implementa `INameResolver` para obter valores a partir das definições de aplicações, como mostra o exemplo. Quando utilizar o WebJobs SDK diretamente, pode escrever uma implementação personalizada que obtém valores de substituição de espaços reservados a partir de qualquer fonte que prefira.

## <a name="binding-at-runtime"></a>Encadernação no tempo de execução

Se precisar de fazer algum trabalho na sua função antes de utilizar um atributo de encadernação como `Queue` , ou , pode utilizar a `Blob` `Table` `IBinder` interface.

O exemplo a seguir pega numa mensagem de fila de entrada e cria uma nova mensagem com o mesmo conteúdo numa fila de saída. O nome da fila de saída é definido por código no corpo da função.

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

Para obter mais informações, consulte [Binding at runtime](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) na documentação Azure Functions.

## <a name="binding-reference-information"></a>Informações vinculativas de referência

A documentação Azure Functions fornece informações de referência sobre cada tipo de encadernação. Encontrará as seguintes informações em cada artigo de referência vinculativo. (Este exemplo baseia-se na fila de armazenamento.)

* [Pacotes](../azure-functions/functions-bindings-storage-queue.md). O pacote que precisa de instalar para incluir suporte para a encadernação num projeto WebJobs SDK.
* [Exemplos.](../azure-functions/functions-bindings-storage-queue-trigger.md) Amostras de código. O exemplo da biblioteca de classes C# aplica-se ao WebJobs SDK. Apenas omita o `FunctionName` atributo.
* [Atributos](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). Os atributos a utilizar para o tipo de encadernação.
* [Configuração](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Explicações das propriedades do atributo e dos parâmetros do construtor.
* [Utilização](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Os tipos a que se pode ligar e informações sobre como funciona a ligação. Por exemplo: algoritmo de sondagens, processamento de fila venenosa.
  
Para obter uma lista de artigos de referência vinculativos, consulte "Encadernações suportadas" no artigo [de gatilhos e encadernações](../azure-functions/functions-triggers-bindings.md#supported-bindings) para funções azure. Nessa lista, as ligações HTTP, Webhooks e Event Grid são suportadas apenas por Funções Azure, e não pelo WebJobs SDK.

## <a name="disable-attribute"></a>Atribuir desativar 

O [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atributo permite-lhe controlar se uma função pode ser ativada. 

No exemplo seguinte, se a definição da aplicação `Disable_TestJob` tiver um valor ou `1` `True` (caso insensível), a função não será executada. Nesse caso, o tempo de execução cria uma função de mensagem de registo *'Funções.TestJob' é desativada*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando altera os valores de definição de aplicações no portal Azure, o WebJob reinicia para recolher a nova definição.

O atributo pode ser declarado ao nível do parâmetro, método ou classe. O nome de definição também pode conter expressões de encadernação.

## <a name="timeout-attribute"></a>Atributo timeout

O [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) atributo faz com que uma função seja cancelada se não terminar dentro de um determinado período de tempo. No exemplo seguinte, a função funcionaria durante um dia sem o atributo Timeout. O intervalo faz com que a função seja cancelada após 15 segundos.

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

Pode aplicar o atributo Timeout ao nível da classe ou do método, e pode especificar um tempo limite global utilizando `JobHostConfiguration.FunctionTimeout` . Os intervalos de tempo de nível de classe ou de nível metodológico substituem os intervalos globais.

## <a name="singleton-attribute"></a>Atributo Singleton

O [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) atributo garante que apenas uma instância de uma função é executado, mesmo quando existem múltiplas instâncias da aplicação web anfitriã. Fá-lo utilizando [o bloqueio distribuído.](#viewing-lease-blobs)

Neste exemplo, apenas um único caso da `ProcessImage` função funciona a qualquer momento:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Alguns gatilhos têm apoio integrado para a gestão da concuência:

* **QueueTrigger**. Definir `JobHostConfiguration.Queues.BatchSize` para `1` .
* **ServiceBusTrigger**. Definir `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` para `1` .
* **FileTrigger**. Definir `FileProcessor.MaxDegreeOfParallelism` para `1` .

Pode utilizar estas definições para garantir que a sua função funciona como singleton num único caso. Para garantir que apenas uma instância da função está a funcionar quando a aplicação web se escala para várias instâncias, aplique um bloqueio singleton de nível de ouvinte na função `[Singleton(Mode = SingletonMode.Listener)]` (). As fechaduras dos ouvintes são adquiridas quando o JobHost começa. Se três casos de escala começarem todos ao mesmo tempo, apenas uma das instâncias adquire o bloqueio e apenas um ouvinte começa.

> [!NOTE]
> Consulte este [GitHub Repo](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonMode.cs) para saber mais sobre o funcionamento do SingletonMode.Function.

### <a name="scope-values"></a>Valores de âmbito

Pode especificar uma *expressão/valor de âmbito* num singleton. A expressão/valor garante que todas as execuções da função num âmbito específico serão serializadas. A implementação de mais bloqueio granular desta forma pode permitir algum nível de paralelismo para a sua função, ao mesmo tempo que serializa outras invocações ditadas pelas suas necessidades. Por exemplo, no seguinte código, a expressão de âmbito liga-se ao `Region` valor da mensagem de entrada. Quando a fila contém três mensagens nas regiões Leste, Leste e Oeste, respectivamente, as mensagens que têm região Leste são executadas em série enquanto a mensagem com a região Oeste é executada em paralelo com as do Leste.

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

O âmbito padrão para uma fechadura `SingletonScope.Function` é, o que significa que o âmbito de bloqueio (o caminho de locação blob) está ligado ao nome de função totalmente qualificado. Para bloquear as funções, especifique `SingletonScope.Host` e use um nome de identificação de âmbito que seja o mesmo em todas as funções que não pretende executar simultaneamente. No exemplo seguinte, apenas um caso de `AddItem` ou corre de cada `RemoveItem` vez:

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

O WebJobs SDK utiliza [locações blob Azure](../storage/blobs/concurrency-manage.md#pessimistic-concurrency-for-blobs) sob as capas para implementar bloqueio distribuído. As bolhas de locação utilizadas pela Singleton podem ser encontradas `azure-webjobs-host` no recipiente na conta de armazenamento sob o caminho `AzureWebJobsStorage` "fechaduras". Por exemplo, o caminho da bolha de arrendamento para o primeiro `ProcessImage` exemplo mostrado anteriormente pode ser `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage` . Todos os caminhos incluem o ID JobHost, neste caso 061851c758f04938a426aaaa9ab3869c0.

## <a name="async-functions"></a>Funções assínc

Para obter informações sobre como codificar funções async, consulte a documentação do [Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Fichas de cancelamento

Para obter informações sobre como lidar com fichas de cancelamento, consulte a documentação do Azure Functions sobre [fichas de cancelamento e paragem graciosa](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Múltiplas instâncias

Se a sua aplicação web funcionar em várias instâncias, um WebJob contínuo funciona em cada instância, ouvindo os gatilhos e as funções de chamada. As várias ligações ao gatilho são projetadas para partilhar eficientemente o trabalho colaborativamente em todos os casos, de modo que a escala para mais instâncias permite-lhe lidar com mais carga.

Embora alguns gatilhos possam resultar em duplo processamento, os gatilhos de armazenamento de fila e bolhas impedem automaticamente uma função de processar uma mensagem de fila ou uma bolha mais de uma vez. Para obter mais informações, consulte [design para obter uma entrada idêntica](../azure-functions/functions-idempotent.md) na documentação Azure Functions.

O gatilho do temporizador assegura automaticamente que apenas uma instância do temporizador é executado, para que não obtenha mais do que uma instância de função a funcionar numa determinada hora programada.

Se quiser garantir que apenas uma instância de uma função é executado mesmo quando existem múltiplas instâncias da aplicação web anfitriã, pode usar o [`Singleton`](#singleton-attribute) atributo.

## <a name="filters"></a>Filtros

Os Filtros de Função (pré-visualização) fornecem uma forma de personalizar o pipeline de execução WebJobs com a sua própria lógica. Os filtros são semelhantes aos [filtros do núcleo ASP.NET.](/aspnet/core/mvc/controllers/filters) Pode implementá-los como atributos declarativos que são aplicados às suas funções ou classes. Para obter mais informações, consulte [filtros de funções.](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

Recomendamos o quadro de exploração madeireira que foi desenvolvido para ASP.NET. O artigo ["Get started"](webjobs-sdk-get-started.md) mostra como usá-lo. 

### <a name="log-filtering"></a>Filtragem de registos

Cada registo criado por um `ILogger` caso tem um associado e `Category` `Level` . [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração, e o código inteiro indica importância relativa:

|Nível de Registo    |Código|
|------------|---|
|Rastreio       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

Pode filtrar independentemente cada categoria para um determinado [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) . Por exemplo, é melhor ver todos os registos para processamento de gatilhos blob, mas apenas `Error` e mais alto para tudo o resto.

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* do SDK depende da filtragem incorporada em .NET Core. A `LogCategories` classe permite definir categorias para funções específicas, gatilhos ou utilizadores. Também define filtros para estados de acolhimento específicos, como `Startup` e `Results` . Isto permite-lhe afinar a saída de registo. Se não for encontrada correspondência nas categorias definidas, o filtro volta ao valor ao `Default` decidir filtrar a mensagem.

`LogCategories` requer a seguinte declaração:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

O exemplo a seguir constrói um filtro que, por defeito, filtra todos os registos ao `Warning` nível. As `Function` `results` categorias e categorias (equivalente à `Host.Results` versão 2.*x*) são filtrados ao `Error` nível. O filtro compara a categoria atual a todos os níveis registados no `LogCategories` caso e escolhe a correspondência mais longa. Isto significa que o `Debug` nível registado para `Host.Triggers` fósforos `Host.Triggers.Queue` ou `Host.Triggers.Blob` . Isto permite-lhe controlar categorias mais amplas sem precisar de adicionar cada uma delas.

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

Na versão 2. *x* do SDK, usa-se a `LogCategoryFilter` classe para controlar a filtragem. A `LogCategoryFilter` propriedade tem um valor inicial de , o que significa que `Default` `Information` quaisquer mensagens no `Information` , , ou `Warning` `Error` `Critical` níveis são registados, mas quaisquer mensagens nos `Debug` níveis ou `Trace` níveis são filtradas.

Como `LogCategories` na versão 3.*x*, a `CategoryLevels` propriedade permite especificar níveis de registo para categorias específicas para que possa afinar a saída de registo. Se não for encontrada correspondência dentro do `CategoryLevels` dicionário, o filtro volta ao valor ao `Default` decidir se filtra a mensagem.

O exemplo a seguir constrói um filtro que por predefinição filtra todos os registos ao `Warning` nível. As  `Function` `Host.Results` categorias e categorias são filtradas ao `Error` nível. `LogCategoryFilter`O compara a categoria atual a todos os `CategoryLevels` inscritos e escolhe o jogo mais longo. Assim, o `Debug` nível registado `Host.Triggers` para irá corresponder `Host.Triggers.Queue` ou `Host.Triggers.Blob` . Isto permite-lhe controlar categorias mais amplas sem precisar de adicionar cada uma delas.

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

### <a name="custom-telemetry-for-application-insights"></a>Telemetria personalizada para Insights de Aplicações

O processo de implementação de telemetria personalizada para Insights de [Aplicação](../azure-monitor/app/app-insights-overview.md) depende da versão SDK. Para aprender a configurar insights de aplicações, consulte [Adicionar Insights de Aplicação registando](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versão 3. *x*

Porque a versão 3. *x* do WebJobs SDK conta com o hospedeiro genérico .NET Core, uma fábrica de telemetria personalizada já não é fornecida. Mas pode adicionar telemetria personalizada ao oleoduto usando a injeção de dependência. Os exemplos desta secção requerem as seguintes `using` declarações:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

A seguinte implementação personalizada [`ITelemetryInitializer`] permite adicionar a sua própria ao [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) padrão [`TelemetryConfiguration`] .

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Ligue [`ConfigureServices`] para o construtor para adicionar o seu costume ao [`ITelemetryInitializer`] oleoduto.

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

Quando o [`TelemetryConfiguration`] é construído, todos os tipos [`ITelemetryInitializer`] registados de estão incluídos. Para saber mais, consulte [a App Insights API para eventos e métricas personalizados.](../azure-monitor/app/api-custom-events-metrics.md)

Na versão 3. *x*, já não é preciso descarregar o [`TelemetryClient`] quando o hospedeiro parar. O sistema de injeção de dependência .NET Core elimina automaticamente o `ApplicationInsightsLoggerProvider` registado, que elimina o [`TelemetryClient`] .

#### <a name="version-2x"></a>Versão 2. *x*

Na versão 2. *x*, criado [`TelemetryClient`] internamente pelo fornecedor application insights para as utilizações webJobs SDK [`ServerTelemetryChannel`](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/.publicApi/Microsoft.AI.ServerTelemetryChannel.dll) . Quando o ponto final do Application Insights não está disponível ou a estrangular os pedidos de entrada, este canal [guarda pedidos no sistema de ficheiros da aplicação web e reenvia-os mais tarde.](https://apmtips.com/posts/2015-09-03-more-telemetry-channels/)

O [`TelemetryClient`] é criado por uma classe que `ITelemetryClientFactory` implementa. Por padrão, este é o [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/) .

Se quiser modificar qualquer parte do pipeline Application Insights, pode fornecer o seu próprio `ITelemetryClientFactory` , e o anfitrião utilizará a sua classe para construir um [`TelemetryClient`] . Por exemplo, este código substitui `DefaultTelemetryClientFactory` para modificar uma propriedade `ServerTelemetryChannel` de:

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

O `SamplingPercentageEstimatorSettings` objeto configura a [amostragem adaptativa.](../azure-monitor/app/sampling.md) Isto significa que em certos cenários de grande volume, o Applications Insights envia um subconjunto selecionado de dados de telemetria para o servidor.

Depois de criar a fábrica de telemetria, passe-a para o fornecedor de registo de Insights de Aplicação:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> Próximos passos

Este artigo forneceu código snippets que mostram como lidar com cenários comuns para trabalhar com o WebJobs SDK. Para obter amostras completas, consulte [amostras azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost)

['Acordo de Execução']: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
['TelemetriaClient']: /dotnet/api/microsoft.applicationinsights.telemetryclient
['ConfigureServices']: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemetryInitializer']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
['TelemetriaConfiguration']: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['Conigução de EmpregoHost']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs