---
title: Referência do C# desenvolvedor de scripts funções azure
description: Entenda como desenvolver funções C# Azure usando script.
author: craigshoemaker
ms.topic: reference
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 76af1f51c83e9554a51e6c17266fac739e6bd6b1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198365"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Referência do C# desenvolvedor de funções azure (.csx)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Este artigo é uma introdução ao desenvolvimento C# de Funções Azure utilizando o script *(.csx).*

As Funções C# Azure C# suportam e programam linguagens. Se procura orientação sobre [ C# a utilização de um projeto](functions-develop-vs.md)de biblioteca de classe Visual Studio, consulte [ C# ](functions-dotnet-class-library.md)a referência do desenvolvedor.

Este artigo assume que já leu o guia de desenvolvedores de [Funções Azure.](functions-reference.md)

## <a name="how-csx-works"></a>Como funciona .csx

A C# experiência do script para funções Azure baseia-se no [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Os dados C# fluem para a sua função através de argumentos de método. Os nomes de argumentos são especificados num ficheiro `function.json`, e existem nomes predefinidos para aceder a coisas como o logger de funções e fichas de cancelamento.

O formato *.csx* permite-lhe escrever menos "placa C# de caldeira" e concentrar-se na escrita apenas uma função. Em vez de embrulhar tudo num espaço de nome e classe, apenas defina um método `Run`. Inclua quaisquer referências de montagem e espaços de nome no início do ficheiro, como de costume.

Os ficheiros *.csx* de uma aplicação de função são compilados quando uma instância é inicializada. Este passo de compilação significa que C# coisas como o C# arranque a frio podem demorar mais tempo para as funções do script em comparação com as bibliotecas de classes. Este passo de compilação é também por isso que C# as C# funções de script são editáveis no portal Azure, enquanto as bibliotecas de classes não são.

## <a name="folder-structure"></a>Estrutura de pasta

A estrutura da C# pasta para um projeto de script parece ser a seguinte:

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

Há um ficheiro [host.json](functions-host-json.md) partilhado que pode ser usado para configurar a aplicação de função. Cada função tem o seu próprio ficheiro de código (.csx) e ficheiro de configuração de ligação (função.json).

As extensões de encadernação exigidas na [versão 2.x e versões posteriores](functions-versions.md) do tempo de execução das Funções são definidas no ficheiro `extensions.csproj`, com os ficheiros da biblioteca reais na pasta `bin`. Ao desenvolver-se localmente, deve [registar extensões vinculativas](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal Azure, este registo é feito para si.

## <a name="binding-to-arguments"></a>Vinculativo aos argumentos

Os dados de entrada ou C# saída estão ligados a um parâmetro de função de script através da propriedade `name` no ficheiro de configuração *function.json.* O exemplo seguinte mostra um ficheiro *function.json* e ficheiro *run.csx* para uma função ativada pela fila. O parâmetro que recebe dados da mensagem de fila chama-se `myQueueItem` porque é esse o valor da propriedade `name`.

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

A declaração `#r` é explicada [mais tarde neste artigo.](#referencing-external-assemblies)

## <a name="supported-types-for-bindings"></a>Tipos suportados para encadernações

Cada encadernação tem os seus próprios tipos suportados; por exemplo, um gatilho de bolha pode ser usado com um parâmetro de corda, um parâmetro POCO, um parâmetro `CloudBlockBlob`, ou qualquer um de vários outros tipos suportados. O [artigo de referência vinculativo para encadernações blob](functions-bindings-storage-blob-trigger.md#usage) lista todos os tipos de parâmetros suportados para os gatilhos de bolhas. Para mais informações, consulte [Gatilhos e encadernações](functions-triggers-bindings.md) e os [docs de referência vinculativos para cada tipo de encadernação](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Referenciar aulas personalizadas

Se precisar de utilizar uma classe personalizada de Objeto CLR (POCO) personalizado, pode incluir a definição de classe dentro do mesmo ficheiro ou colocá-la num ficheiro separado.

O exemplo que se segue mostra um exemplo *run.csx* que inclui uma definição de classe POCO.

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

## <a name="reusing-csx-code"></a>Reutilizar o código .csx

Pode utilizar classes e métodos definidos noutros *ficheiros .csx* no ficheiro *run.csx.* Para isso, use `#load` diretivas no seu ficheiro *run.csx.* No exemplo seguinte, uma rotina de exploração madeireira chamada `MyLogger` é partilhada em *myLogger.csx* e carregada em *run.csx* usando a diretiva `#load`:

Exemplo *run.csx:*

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

Usar um ficheiro *.csx* partilhado é um padrão comum quando se pretende escrever fortemente os dados passados entre funções utilizando um objeto POCO. No exemplo simplificado seguinte, um gatilho http e o gatilho da fila partilham um objeto POCO chamado `Order` para escrever fortemente os dados da encomenda:

Exemplo *run.csx* para o gatilho HTTP:

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

Exemplo *run.csx* para o gatilho da fila:

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

Ordem *exemplo.csx:*

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

Pode utilizar um caminho relativo com a diretiva `#load`:

* `#load "mylogger.csx"` carrega um ficheiro localizado na pasta de funções.
* `#load "loadedfiles\mylogger.csx"` carrega um ficheiro localizado numa pasta na pasta de funções.
* `#load "..\shared\mylogger.csx"` carrega um ficheiro localizado numa pasta ao mesmo nível da pasta de funções, ou seja, diretamente em *wwwroot*.

A diretiva `#load` funciona apenas com ficheiros *.csx,* não com ficheiros *.cs.*

## <a name="binding-to-method-return-value"></a>Vinculação ao valor de retorno do método

Pode utilizar um valor de devolução de método para uma ligação de saída, utilizando o nome `$return` em *função.json*. Por exemplo, consulte [Gatilhos e encadernações](./functions-bindings-return-value.md).

Utilize o valor de devolução apenas se uma execução de função bem sucedida resultar sempre num valor de retorno para passar para a ligação de saída. Caso contrário, utilize `ICollector` ou `IAsyncCollector`, como se pode ver na secção seguinte.

## <a name="writing-multiple-output-values"></a>Escrever vários valores de saída

Para escrever vários valores para uma ligação de saída, ou se uma invocação de função bem sucedida pode não resultar em nada para passar para a ligação de saída, use os [tipos`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [`IAsyncCollector`.](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) Estes tipos são coleções escritas apenas que são escritas para a ligação de saída quando o método completa.

Este exemplo escreve várias mensagens de fila na mesma fila usando `ICollector`:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Registo

Para iniciar sessão nos C#seus registos de streaming, inclua um argumento do tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Recomendamos que o nomeie `log`. Evite utilizar `Console.Write` em funções Azure.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Para obter informações sobre uma nova estrutura de registo supérbio que pode utilizar em vez de `TraceWriter`, consulte [registos de escrita em C# funções](functions-monitoring.md#write-logs-in-c-functions) no artigo **Funções Monitor Azure.**

## <a name="async"></a>Async

Para fazer uma função [assíncrona,](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)use a palavra-chave `async` e devolva um objeto `Task`.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Não se pode usar `out` parâmetros em funções de asincronização. Para encadernações de saída, utilize o valor de devolução da [função](#binding-to-method-return-value) ou um [objeto de coleção.](#writing-multiple-output-values)

## <a name="cancellation-tokens"></a>Fichas de cancelamento

Uma função pode aceitar um parâmetro [CancelamentoToken,](/dotnet/api/system.threading.cancellationtoken) que permite ao sistema operativo notificar o seu código quando a função está prestes a ser terminada. Pode utilizar esta notificação para se certificar de que a função não termina inesperadamente de uma forma que deixe os dados num estado inconsistente.

O exemplo que se segue mostra como verificar se há uma interrupção da função iminente.

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

## <a name="importing-namespaces"></a>Espaços de nomes importando

Se precisar de importar espaços com nomes, pode fazê-lo como de costume, com a cláusula `using`.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Os seguintes espaços de nome são automaticamente importados e, portanto, são facultativos:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Referenciação de conjuntos externos

Para os conjuntos-quadro, adicione referências utilizando a diretiva `#r "AssemblyName"`.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Os seguintes conjuntos são automaticamente adicionados pelo ambiente de hospedagem das Funções Azure:

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

Os seguintes conjuntos podem ser referenciados por nome simples (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Referenciação de conjuntos personalizados

Para fazer referência a um conjunto personalizado, pode utilizar um conjunto *partilhado* ou um conjunto *privado:*

* As assembléias partilhadas são partilhadas em todas as funções dentro de uma aplicação de função. Para fazer referência a um conjunto personalizado, faça o upload do conjunto para uma pasta chamada `bin` na pasta raiz da [sua app de função](functions-reference.md#folder-structure) (wwwroot).

* As assembleias privadas fazem parte do contexto de uma determinada função e suportam o carregamento lateral de diferentes versões. As assembleias privadas devem ser colocadas numa pasta `bin` no diretório de funções. Faça referência aos conjuntos utilizando o nome do ficheiro, como `#r "MyAssembly.dll"`.

Para obter informações sobre como enviar ficheiros para a sua pasta de funções, consulte a secção na [gestão](#using-nuget-packages)do pacote .

### <a name="watched-directories"></a>Diretórios assistidos

O diretório que contém o ficheiro do script de função é automaticamente observado para alterações nas assembléias. Para ter em conta as alterações de montagem noutras direções, adicione-as à lista de `watchDirectories` no [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Usando pacotes NuGet
Para utilizar os pacotes NuGet numa C# função 2.x e posterior, faça upload de um ficheiro *function.proj* para a pasta da função no sistema de ficheiros da aplicação de função. Aqui está um ficheiro *fun.proj* de exemplo que adiciona uma referência a *Microsoft.ProjectOxford.Face* versão *1.1.0*:

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

Para utilizar um feed NuGet personalizado, especifique o feed num ficheiro *Nuget.Config* na raiz da App função. Para mais informações, consulte [configurar o comportamento do NuGet](/nuget/consume-packages/configuring-nuget-behavior).

> [!NOTE]
> Em funções C# 1.x, os pacotes NuGet são referenciados com um ficheiro *project.json* em vez de um ficheiro *function.proj.*

Para funções de 1.x, utilize um ficheiro *project.json.* Aqui está um arquivo de *projeto.json* exemplo:

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

1. Abra a função no portal Azure. O separador de registos mostra a saída de instalação do pacote.
2. Para fazer o upload de um ficheiro *function.proj,* utilize um dos métodos descritos no tópico de referência da [função Como atualizar](functions-reference.md#fileupdate) os ficheiros de aplicações de funções no tópico de referência do desenvolvedor de Funções Azure.
3. Depois de o ficheiro *function.proj* ser carregado, vê a saída como o seguinte exemplo no registo de streaming da sua função:

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

Para obter uma variável ambiental ou um valor de definição de app, use `System.Environment.GetEnvironmentVariable`, como mostra o seguinte exemplo de código:

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

Em C# e noutras línguas .NET, você pode usar um padrão de ligação [imperativo,](https://en.wikipedia.org/wiki/Imperative_programming) em oposição às encadernações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em *função.json*. A ligação imperativa é útil quando os parâmetros de ligação precisam de ser calculados no tempo de execução em vez de tempo de conceção. Com este padrão, pode ligar-se a encadernações de entrada e saída suportadas no seu código de função.

Defina uma vinculação imperativa da seguinte forma:

- **Não** inclua uma entrada em *função.json* para as suas encadernações imperativas desejadas.
- Passe num parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Utilize o C# seguinte padrão para efetuar a encadernação de dados.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` é o atributo .NET que define a sua ligação e `T` é um tipo de entrada ou saída que é suportado por esse tipo de ligação. `T` não pode ser do tipo de parâmetro `out` (como `out JObject`). Por exemplo, a ligação de saída da tabela mobile Apps suporta seis tipos de [saída,](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)mas só pode utilizar [o ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) para `T`.

### <a name="single-attribute-example"></a>Exemplo de atributo único

O seguinte código de exemplo cria uma ligação de saída de [blob de armazenamento](functions-bindings-storage-blob-output.md) com caminho blob que é definido no tempo de execução, em seguida, escreve uma corda para a bolha.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) define a entrada ou ligação de saída da bolha de [armazenamento,](functions-bindings-storage-blob.md) e [textWriter](/dotnet/api/system.io.textwriter) é um tipo de ligação de saída suportado.

### <a name="multiple-attribute-example"></a>Exemplo de atributo múltiplo

O exemplo anterior obtém a definição da aplicação para a cadeia de ligação à conta de armazenamento principal da aplicação de função (que é `AzureWebJobsStorage`). Pode especificar uma definição de aplicação personalizada para usar na conta de Armazenamento, adicionando o [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando o conjunto de atributos para `BindAsync<T>()`. Utilize um parâmetro `Binder`, não `IBinder`.  Por exemplo:

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

A tabela seguinte lista os atributos .NET para cada tipo de encadernação e as embalagens em que são definidos.

> [!div class="mx-codeBreakAll"]
> | Vínculo | Atributo | Adicionar referência |
> |------|------|------|
> | BD do Cosmos | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Hubs de Eventos | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`, ](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Hubs de Notificação | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`, ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs) [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Fila de armazenamento | [`Microsoft.Azure.WebJobs.QueueAttribute`, ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs) [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Blob de armazenamento | [`Microsoft.Azure.WebJobs.BlobAttribute`, ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Mesa de armazenamento | [`Microsoft.Azure.WebJobs.TableAttribute`, ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs) [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre gatilhos e encadernações](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as melhores práticas para funções azure](functions-best-practices.md)
