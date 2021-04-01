---
title: Referência do desenvolvedor de scripts Azure Functions C#
description: Entenda como desenvolver funções Azure usando o script C#.
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 48614640660da6d85face5ea416d267fa9f59515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92164844"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Referência do programador Azure Functions C# (.csx)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Este artigo é uma introdução ao desenvolvimento de Funções Azure utilizando o script C#*(.csx*).

A Azure Functions suporta linguagens de programação de scripts C# e C#. Se procura orientação sobre [a utilização de C# num projeto de biblioteca de classe Visual Studio](functions-develop-vs.md), consulte a referência do desenvolvedor [C#](functions-dotnet-class-library.md).

Este artigo pressupõe que já leu o guia de desenvolvedores de [Funções Azure.](functions-reference.md)

## <a name="how-csx-works"></a>Como funciona .csx

A experiência de script C# para Azure Functions baseia-se no [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Os dados fluem para a sua função C# através de argumentos de método. Os nomes dos argumentos são especificados num `function.json` ficheiro, e existem nomes predefinidos para aceder a coisas como o madeiriro de funções e fichas de cancelamento.

O formato *.csx* permite-lhe escrever menos "caldeira" e concentrar-se em escrever apenas uma função C#. Em vez de embrulhar tudo num espaço de nome e classe, basta definir um `Run` método. Inclua quaisquer referências de montagem e espaços de nome no início do ficheiro, como de costume.

Os *ficheiros .csx* de uma aplicação de função são compilados quando uma instância é inicializada. Este passo de compilação significa que coisas como o arranque a frio podem demorar mais tempo para as funções de script C# em comparação com as bibliotecas de classe C#. Este passo de compilação é também o motivo pelo qual as funções de script C# são editáveis no portal Azure, enquanto as bibliotecas de classe C# não são.

## <a name="folder-structure"></a>Estrutura de pasta

A estrutura da pasta para um projeto de script C# parece o seguinte:

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

Há umhost.jspartilhado [ no](functions-host-json.md) ficheiro que pode ser usado para configurar a aplicação de função. Cada função tem o seu próprio ficheiro de código (.csx) e ficheiro de configuração de ligação (function.js).

As extensões de ligação necessárias na [versão 2.x e versões posteriores](functions-versions.md) do tempo de execução das Funções são definidas no `extensions.csproj` ficheiro, com os ficheiros reais da biblioteca na `bin` pasta. Ao desenvolver-se localmente, deve [registar extensões vinculativas](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal Azure, este registo é feito para si.

## <a name="binding-to-arguments"></a>Vinculativo aos argumentos

Os dados de entrada ou saída estão ligados a um parâmetro de função de script C# através da `name` propriedade nofunction.js *no* ficheiro de configuração. O exemplo a seguir mostra uma *function.jsno* ficheiro e no ficheiro *run.csx* para uma função desencadeada pela fila. O parâmetro que recebe dados da mensagem de fila é nomeado `myQueueItem` porque é o valor da `name` propriedade.

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

A `#r` declaração é explicada mais tarde neste [artigo.](#referencing-external-assemblies)

## <a name="supported-types-for-bindings"></a>Tipos suportados para encadernações

Cada encadernação tem os seus próprios tipos suportados; por exemplo, um gatilho blob pode ser usado com um parâmetro de corda, um parâmetro POCO, um `CloudBlockBlob` parâmetro, ou qualquer um de vários outros tipos suportados. O [artigo de referência vinculativo para encadernações blob](functions-bindings-storage-blob-trigger.md#usage) lista todos os tipos de parâmetros suportados para os gatilhos blob. Para obter mais informações, consulte [Gatilhos e encadernações](functions-triggers-bindings.md) e os [documentos de referência vinculativos para cada tipo de encadernação](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Referenciação de aulas personalizadas

Se precisar de utilizar uma classe personalizada de Objeto CLR (POCO) simples, pode incluir a definição de classe dentro do mesmo ficheiro ou colocá-la num ficheiro separado.

O exemplo a seguir mostra um exemplo *run.csx* que inclui uma definição de classe POCO.

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

Uma classe POCO deve ter um getter e setter definidos para cada propriedade.

## <a name="reusing-csx-code"></a>Reutilização do código .csx

Pode utilizar aulas e métodos definidos em *outros ficheiros .csx* no seu ficheiro *run.csx.* Para isso, use `#load` as diretivas no seu ficheiro *run.csx.* No exemplo seguinte, uma rotina de registo registadora `MyLogger` é partilhada no *myLogger.csx* e carregada em *run.csx* utilizando a `#load` diretiva:

Exemplo *run.csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemplo *mylogger.csx:*

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

A utilização de um ficheiro *.csx* partilhado é um padrão comum quando se pretende escrever fortemente os dados passados entre funções utilizando um objeto POCO. No seguinte exemplo simplificado, um gatilho HTTP e um gatilho de fila partilham um objeto POCO nomeado `Order` para digitar fortemente os dados da encomenda:

Exemplo *executado.csx* para disparador HTTP:

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

Exemplo *run.csx* para gatilho de fila:

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

Exemplo *ordem.csx*:

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

Pode utilizar um caminho relativo com a `#load` diretiva:

* `#load "mylogger.csx"` carrega um ficheiro localizado na pasta de funções.
* `#load "loadedfiles\mylogger.csx"` carrega um ficheiro localizado numa pasta na pasta de funções.
* `#load "..\shared\mylogger.csx"` carrega um ficheiro situado numa pasta ao mesmo nível da pasta de funções, ou seja, diretamente em *wwwroot*.

A `#load` diretiva funciona apenas com *ficheiros .csx,* não com *ficheiros .cs.*

## <a name="binding-to-method-return-value"></a>Ligação ao valor de retorno do método

Pode utilizar um valor de retorno de método para uma ligação de saída, utilizando o nome `$return` em *function.jsem*. Por exemplo, consulte [Gatilhos e encadernações](./functions-bindings-return-value.md).

Utilize o valor de devolução apenas se uma execução de função bem sucedida resultar sempre num valor de retorno para passar para a ligação de saída. Caso contrário, utilize `ICollector` `IAsyncCollector` ou, como indicado na secção seguinte.

## <a name="writing-multiple-output-values"></a>Escrever vários valores de saída

Para escrever vários valores para uma ligação de saída, ou se uma invocação de função bem sucedida pode não resultar em nada para passar para a ligação de saída, use os [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) ou tipos. Estes tipos são coleções apenas de escrita que são escritas para a ligação de saída quando o método termina.

Este exemplo escreve várias mensagens de fila na mesma fila `ICollector` utilizando:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Registo

Para iniciar a sua saída nos seus registos de streaming em C#, inclua um argumento do tipo [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). Recomendamos que lhe dê um `log` nome. Evite utilizar `Console.Write` em Funções Azure.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Para obter informações sobre uma nova estrutura de registo que pode utilizar em vez de `TraceWriter` , consulte a documentação [ILogger](functions-dotnet-class-library.md#ilogger) no guia de desenvolvedores da biblioteca da classe .NET.

### <a name="custom-metrics-logging"></a>Registo de métricas personalizadas

Pode utilizar o `LogMetric` método de extensão `ILogger` para criar métricas personalizadas em Application Insights. Aqui está uma chamada de método de amostra:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Este código é uma alternativa à chamada `TrackMetric` utilizando a API de Insights de Aplicação para .NET.

## <a name="async"></a>Async

Para fazer uma função [assíncrona,](/dotnet/csharp/programming-guide/concepts/async/)use a `async` palavra-chave e devolva um `Task` objeto.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Não pode usar `out` parâmetros em funções de async. Para encadernações de saída, utilize o valor de retorno da [função](#binding-to-method-return-value) ou um [objeto de coletor.](#writing-multiple-output-values)

## <a name="cancellation-tokens"></a>Fichas de cancelamento

Uma função pode aceitar um parâmetro [CancelamentoToken,](/dotnet/api/system.threading.cancellationtoken) que permite ao sistema operativo notificar o seu código quando a função está prestes a ser terminada. Pode utilizar esta notificação para se certificar de que a função não termina inesperadamente de forma a deixar os dados num estado inconsistente.

O exemplo a seguir mostra como verificar a interrupção da função iminente.

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

## <a name="importing-namespaces"></a>Espaços de identificação importados

Se precisar de importar espaços de nome, pode fazê-lo como de costume, com a `using` cláusula.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Os seguintes espaços de nome são automaticamente importados e, por conseguinte, são opcionais:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Referenciação de conjuntos externos

Para os conjuntos-quadro, adicione referências utilizando a `#r "AssemblyName"` diretiva.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Os seguintes conjuntos são automaticamente adicionados pelo ambiente de hospedagem Azure Functions:

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

Os seguintes conjuntos podem ser referenciados por nome simples (por `#r "AssemblyName"` exemplo:

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Referenciação de conjuntos personalizados

Para fazer referência a um conjunto personalizado, pode utilizar uma assembleia *partilhada* ou uma assembleia *privada:*

* Os conjuntos partilhados são partilhados em todas as funções dentro de uma aplicação de função. Para fazer referência a um conjunto personalizado, faça o upload do conjunto para uma pasta nomeada `bin` na pasta raiz da sua [aplicação de funções](functions-reference.md#folder-structure) (wwwroot).

* As assembleias privadas fazem parte do contexto de uma determinada função e suportam o carregamento lateral de diferentes versões. As assembleias privadas devem ser carregadas numa `bin` pasta no diretório de funções. Faça referência aos conjuntos utilizando o nome do ficheiro, tal como `#r "MyAssembly.dll"` .

Para obter informações sobre como enviar ficheiros para a sua pasta de funções, consulte a secção sobre [a gestão do pacote](#using-nuget-packages).

### <a name="watched-directories"></a>Diretórios assistidos

O diretório que contém o ficheiro de script de função é automaticamente observado para alterações nos conjuntos. Para observar as alterações de montagem em outros diretórios, adicione-os à `watchDirectories` lista emhost.js[ em](functions-host-json.md).

## <a name="using-nuget-packages"></a>Usando pacotes NuGet
Para utilizar pacotes NuGet numa função 2.x e mais tarde C#, carrece um ficheiro *function.proj* para a pasta da função no sistema de ficheiros da aplicação de funções. Aqui está um ficheiro *de exemplo.proj* que adiciona uma referência à versão *1.1.0* do *Microsoft.ProjectOxford.Face* :

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

Para utilizar um feed NuGet personalizado, especifique o feed num ficheiro *Nuget.Config* na raiz da App de Função. Para obter mais informações, consulte [o comportamento do NuGet configurado.](/nuget/consume-packages/configuring-nuget-behavior)

> [!NOTE]
> Nas funções 1.x C#, as embalagens NuGet são referenciadas com um *project.jsno* ficheiro em vez de um ficheiro *function.proj.*

Para funções de 1.x, utilize um *project.jsno* ficheiro. Aqui está um exemplo *project.jsno* arquivo:

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

### <a name="using-a-functionproj-file"></a>Usando um ficheiro function.proj

1. Abra a função no portal Azure. O separador de registos exibe a saída de instalação do pacote.
2. Para carregar um ficheiro *function.proj,* utilize um dos métodos descritos no [como atualizar ficheiros de aplicações](functions-reference.md#fileupdate) de função no tópico de referência do programador Azure Functions.
3. Depois de o ficheiro *função.proj* ser carregado, vê a saída como o seguinte exemplo no registo de streaming da sua função:

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

Para obter uma variável ambiental ou um valor de definição de aplicação, `System.Environment.GetEnvironmentVariable` utilize, como mostra o seguinte exemplo de código:

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

## <a name="binding-at-runtime"></a>Encadernação no tempo de execução

Em C# e em outras línguas .NET, pode utilizar um padrão de ligação [imperativo,](https://en.wikipedia.org/wiki/Imperative_programming) ao contrário das encadernações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em *function.jsem*. A ligação imperativa é útil quando os parâmetros de ligação precisam de ser calculados em tempo de execução em vez de tempo de conceção. Com este padrão, pode ligar-se a entradas suportadas e ligações de saída no seu código de função.

Defina uma ligação imperativa da seguinte forma:

- **Não** inclua uma entrada em *function.js* para as suas obrigações imperativas desejadas.
- Passe num parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- Utilize o seguinte padrão C# para executar a ligação de dados.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` é o atributo .NET que define a sua ligação e `T` é um tipo de entrada ou saída que é suportado por esse tipo de encadernação. `T` não pode ser um `out` tipo de parâmetro `out JObject` (como). Por exemplo, a ligação de saída da tabela mobile Apps suporta [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas só pode utilizar o [ICollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou para [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) `T` .

### <a name="single-attribute-example"></a>Exemplo de atributo único

O seguinte código de exemplo cria uma [ligação de saída de bolha](functions-bindings-storage-blob-output.md) de armazenamento com o caminho blob definido no tempo de execução, em seguida, escreve uma corda para a bolha.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) define a entrada [blob de armazenamento](functions-bindings-storage-blob.md) ou a ligação de saída, e [o TextWriter](/dotnet/api/system.io.textwriter) é um tipo de ligação de saída suportado.

### <a name="multiple-attribute-example"></a>Exemplo de atributo múltiplo

O exemplo anterior obtém a definição da aplicação para a principal cadeia de ligação da conta de armazenamento da aplicação de função (que `AzureWebJobsStorage` é). Pode especificar uma definição de aplicação personalizada para utilizar para a conta de Armazenamento adicionando o [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando o conjunto de atributos em `BindAsync<T>()` . Use um `Binder` parâmetro, `IBinder` não.  Por exemplo:

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

A tabela que se segue lista os atributos .NET para cada tipo de encadernação e os pacotes em que são definidos.

> [!div class="mx-codeBreakAll"]
> | Enlace | Atributo | Adicionar referência |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Hubs de Eventos | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAccountTests.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Aplicações Móveis | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Hubs de Notificação | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAttributeTests.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAccountTests.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Fila de armazenamento | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Blob de armazenamento | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Mesa de armazenamento | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre gatilhos e encadernações](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as melhores práticas para funções Azure](functions-best-practices.md)
