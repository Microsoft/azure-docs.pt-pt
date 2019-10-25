---
title: Referência C# do desenvolvedor de Azure Functions script
description: Entenda como desenvolver Azure Functions usando C# o script.
author: craigshoemaker
manager: gwallace
keywords: funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.topic: reference
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 8bbfef9d9873669120f792bce3e50e457791d4b0
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72787205"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Referência C# do desenvolvedor do Azure Functions script (. CSX)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Este artigo é uma introdução ao desenvolvimento de Azure Functions usando C# script ( *. CSX*).

O Azure Functions C# suporta C# e linguagens de programação de script. Se você estiver procurando orientação sobre como [usar C# em um projeto de biblioteca de classes do Visual Studio](functions-develop-vs.md), consulte [ C# referência do desenvolvedor](functions-dotnet-class-library.md).

Este artigo pressupõe que você já tenha lido o [Guia do Azure Functions desenvolvedores](functions-reference.md).

## <a name="how-csx-works"></a>Como o. CSX funciona

A C# experiência de script para Azure Functions é baseada no [SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Os dados fluem para C# sua função por meio de argumentos de método. Os nomes de argumento são especificados em um arquivo `function.json`, e há nomes predefinidos para acessar coisas como os tokens de agente de log de função e cancelamento.

O formato *. CSX* permite escrever menos "clichê" e se concentrar em escrever apenas uma C# função. Em vez de encapsular tudo em um namespace e classe, basta definir um método `Run`. Inclua quaisquer referências de assembly e namespaces no início do arquivo como de costume.

Os arquivos *. CSX* de um aplicativo de funções são compilados quando uma instância é inicializada. Essa etapa de compilação significa coisas como a inicialização a frio pode C# levar mais tempo para C# funções de script em comparação com bibliotecas de classes. Essa etapa de compilação também é C# por que as funções de script são editáveis C# no portal do Azure, enquanto as bibliotecas de classes não são.

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas de C# um projeto de script é semelhante ao seguinte:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Há um arquivo [host. JSON](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função tem seu próprio arquivo de código (. CSX) e o arquivo de configuração de associação (Function. JSON).

As extensões de associação necessárias na [versão 2. x](functions-versions.md) do tempo de execução do Functions são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você deve [registrar extensões de associação](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

## <a name="binding-to-arguments"></a>Associação a argumentos

Os dados de entrada ou de saída são C# associados a um parâmetro de função de script por meio da propriedade `name` no arquivo de configuração *Function. JSON* . O exemplo a seguir mostra um arquivo *Function. JSON* e executa o arquivo *. CSX* para uma função disparada por fila. O parâmetro que recebe dados da mensagem da fila é denominado `myQueueItem` porque esse é o valor da propriedade `name`.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

A instrução `#r` é explicada [posteriormente neste artigo](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Tipos com suporte para associações

Cada associação tem seus próprios tipos com suporte; por exemplo, um gatilho de blob pode ser usado com um parâmetro de cadeia de caracteres, um parâmetro POCO, um parâmetro `CloudBlockBlob` ou qualquer um dos vários outros tipos com suporte. O [artigo de referência de associação para associações de blob](functions-bindings-storage-blob.md#trigger---usage) lista todos os tipos de parâmetro com suporte para gatilhos de BLOB. Para obter mais informações, consulte [gatilhos e associações](functions-triggers-bindings.md) e os [documentos de referência de associação para cada tipo de associação](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Referenciando classes personalizadas

Se você precisar usar uma classe POCO (objeto CLR antigo) personalizada, poderá incluir a definição de classe dentro do mesmo arquivo ou colocá-la em um arquivo separado.

O exemplo a seguir mostra um exemplo *Run. CSX* que inclui uma definição de classe poco.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Uma classe POCO deve ter um getter e setter definido para cada propriedade.

## <a name="reusing-csx-code"></a>Reutilizando o código. CSX

Você pode usar classes e métodos definidos em outros arquivos *. CSX* em seu arquivo *Run. CSX* . Para fazer isso, use as diretivas `#load` no arquivo *Run. CSX* . No exemplo a seguir, uma rotina de registro em log chamada `MyLogger` é compartilhada em *MyLogger. CSX* e carregada em *Run. CSX* usando a diretiva `#load`:

Exemplo de *Run. CSX*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemplo de *MyLogger. CSX*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Usar um arquivo *. CSX* compartilhado é um padrão comum quando você deseja fortemente digitar os dados passados entre as funções usando um objeto poco. No exemplo simplificado a seguir, um gatilho HTTP e um gatilho de fila compartilham um objeto POCO chamado `Order` para digitar fortemente os dados do pedido:

Exemplo *Run. CSX* para gatilho http:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Exemplo de *Run. CSX* para o gatilho de fila:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Exemplo de *Order. CSX*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +
                  "\n\tcustAddress : " + custAddress +
                  "\n\tcustEmail : " + custEmail +
                  "\n\tcartId : " + cartId + "\n}";
    }
}
```

Você pode usar um caminho relativo com a diretiva `#load`:

* `#load "mylogger.csx"` carrega um arquivo localizado na pasta de funções.
* `#load "loadedfiles\mylogger.csx"` carrega um arquivo localizado em uma pasta na pasta de funções.
* `#load "..\shared\mylogger.csx"` carrega um arquivo localizado em uma pasta no mesmo nível que a pasta de função, ou seja, diretamente sob *wwwroot*.

A diretiva `#load` funciona apenas com arquivos *. CSX* , e não com arquivos *. cs* .

## <a name="binding-to-method-return-value"></a>Associação ao valor de retorno do método

Você pode usar um valor de retorno de método para uma associação de saída, usando o nome `$return` em *Function. JSON*. Para obter exemplos, consulte [gatilhos e associações](./functions-bindings-return-value.md).

Use o valor de retorno somente se uma execução de função bem-sucedida sempre resultar em um valor de retorno para passar para a associação de saída. Caso contrário, use `ICollector` ou `IAsyncCollector`, conforme mostrado na seção a seguir.

## <a name="writing-multiple-output-values"></a>Gravando vários valores de saída

Para gravar vários valores em uma associação de saída ou se uma invocação de função bem-sucedida pode não resultar em nada para passar para a associação de saída, use os tipos [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) . Esses tipos são coleções somente gravação que são gravadas na associação de saída quando o método é concluído.

Este exemplo grava várias mensagens da fila na mesma fila usando `ICollector`:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Registo

Para registrar a saída em seus logs de C#streaming no, inclua um argumento do tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Recomendamos que você o nomeie `log`. Evite usar `Console.Write` em Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Para obter informações sobre uma estrutura de log mais recente que você pode usar em vez de `TraceWriter`, consulte [gravar logs em C# funções](functions-monitoring.md#write-logs-in-c-functions) no artigo **monitorar Azure Functions** .

## <a name="async"></a>Async

Para tornar uma função [assíncrona](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), use a palavra-chave `async` e retorne um objeto `Task`.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Você não pode usar parâmetros `out` em funções assíncronas. Para associações de saída, use o [valor de retorno da função](#binding-to-method-return-value) ou um [objeto do coletor](#writing-multiple-output-values) em vez disso.

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Uma função pode aceitar um parâmetro [CancellationToken](/dotnet/api/system.threading.cancellationtoken) , que permite que o sistema operacional Notifique seu código quando a função está prestes a ser encerrada. Você pode usar essa notificação para garantir que a função não seja encerrada inesperadamente de forma a deixar dados em um estado inconsistente.

O exemplo a seguir mostra como verificar a terminação de função iminente.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>Importando namespaces

Se você precisar importar namespaces, poderá fazer isso como de costume, com a cláusula `using`.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Os namespaces a seguir são importados automaticamente e, portanto, são opcionais:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Referenciando assemblies externos

Para assemblies do Framework, adicione referências usando a diretiva `#r "AssemblyName"`.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Os assemblies a seguir são adicionados automaticamente pelo ambiente de hospedagem Azure Functions:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Os assemblies a seguir podem ser referenciados por nome simples (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Referenciando assemblies personalizados

Para fazer referência a um assembly personalizado, você pode usar um assembly *compartilhado* ou um assembly *privado* :

* Os assemblies compartilhados são compartilhados entre todas as funções em um aplicativo de funções. Para fazer referência a um assembly personalizado, carregue o assembly em uma pasta chamada `bin` em sua [pasta raiz do aplicativo de funções](functions-reference.md#folder-structure) (wwwroot).

* Os assemblies privados fazem parte de um contexto de função fornecido e dão suporte ao carregamento lateral de diferentes versões. Os assemblies privados devem ser carregados em uma pasta `bin` no diretório de funções. Referencie os assemblies usando o nome do arquivo, como `#r "MyAssembly.dll"`.

Para obter informações sobre como carregar arquivos para sua pasta de funções, consulte a seção sobre [Gerenciamento de pacotes](#using-nuget-packages).

### <a name="watched-directories"></a>Diretórios observados

O diretório que contém o arquivo de script de função é automaticamente observado para alterações em assemblies. Para observar as alterações de assembly em outros diretórios, adicione-as à lista `watchDirectories` em [host. JSON](functions-host-json.md).

## <a name="using-nuget-packages"></a>Usando pacotes NuGet
Para usar os pacotes NuGet em uma função 2 C# . x, carregue um arquivo *Function. proj* para a pasta da função no sistema de arquivos do aplicativo de funções. Aqui está um arquivo *Function. proj* de exemplo que adiciona uma referência a *Microsoft. ProjectOxford. face* versão *1.1.0*:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Para usar um feed do NuGet personalizado, especifique o feed em um arquivo *NuGet. config* na raiz do aplicativo de funções. Para obter mais informações, consulte [Configurando o comportamento do NuGet](/nuget/consume-packages/configuring-nuget-behavior).

> [!NOTE]
> Em funções 1. C# x, os pacotes NuGet são referenciados com um arquivo *Project. JSON* em vez de um arquivo *Function. proj* .

Para funções 1. x, use um arquivo *Project. JSON* em vez disso. Aqui está um exemplo de arquivo *Project. JSON* :

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Usando um arquivo function. proj

1. Abra a função no portal do Azure. A guia logs exibe a saída de instalação do pacote.
2. Para carregar um arquivo *Function. proj* , use um dos métodos descritos em [como atualizar os arquivos do aplicativo de funções](functions-reference.md#fileupdate) no tópico Azure Functions referência do desenvolvedor.
3. Depois que o arquivo *Function. proj* for carregado, você verá uma saída semelhante ao exemplo a seguir no log de streaming da função:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Variáveis de ambiente

Para obter uma variável de ambiente ou um valor de configuração de aplicativo, use `System.Environment.GetEnvironmentVariable`, conforme mostrado no exemplo de código a seguir:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a>

## <a name="binding-at-runtime"></a>Associação em tempo de execução

No C# e em outras linguagens .net, você pode usar um padrão de associação [imperativo](https://en.wikipedia.org/wiki/Imperative_programming) , em oposição às associações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em *Function. JSON*. A associação imperativa é útil quando parâmetros de associação precisam ser computados em tempo de execução em vez de tempo de design. Com esse padrão, você pode associar a associações de entrada e saída com suporte imediatamente no seu código de função.

Defina uma associação imperativa da seguinte maneira:

- **Não** inclua uma entrada em *Function. JSON* para suas associações imperativas desejadas.
- Passe um parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Use o padrão C# a seguir para executar a vinculação de dados.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` é o atributo .NET que define sua associação e `T` é um tipo de entrada ou saída com suporte nesse tipo de associação. `T` não pode ser um tipo de parâmetro `out` (como `out JObject`). Por exemplo, a associação de saída de tabela de aplicativos móveis dá suporte a [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas você só pode usar [ICollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) para `T`.

### <a name="single-attribute-example"></a>Exemplo de atributo único

O código de exemplo a seguir cria uma [Associação de saída de blob de armazenamento](functions-bindings-storage-blob.md#output) com o caminho de blob definido em tempo de execução e, em seguida, grava uma cadeia de caracteres no BLOB.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[Blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) define a associação de entrada ou saída de [blob de armazenamento](functions-bindings-storage-blob.md) , e [TextWriter](/dotnet/api/system.io.textwriter) é um tipo de associação de saída com suporte.

### <a name="multiple-attribute-example"></a>Exemplo de atributo múltiplo

O exemplo anterior Obtém a configuração do aplicativo para a cadeia de conexão da conta de armazenamento principal do aplicativo de funções (que é `AzureWebJobsStorage`). Você pode especificar uma configuração de aplicativo personalizada a ser usada para a conta de armazenamento adicionando o [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando a matriz de atributos para `BindAsync<T>()`. Use um parâmetro `Binder`, não `IBinder`.  Por exemplo:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

A tabela a seguir lista os atributos do .NET para cada tipo de associação e os pacotes nos quais eles são definidos.

> [!div class="mx-codeBreakAll"]
> | Vinculação | Atributo | Adicionar referência |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Hubs de Eventos | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Aplicações Móveis | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Hubs de Notificação | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Fila de armazenamento | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Blob de armazenamento | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tabela de armazenamento | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre gatilhos e associações](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as práticas recomendadas para Azure Functions](functions-best-practices.md)
