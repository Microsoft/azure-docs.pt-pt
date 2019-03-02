---
title: Referência do programador do Azure funções c# script
description: Aprenda a desenvolver as funções do Azure com o script c#.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 4ef16a932eb50294ff3d1596873295d82f8d894e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241482"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Referência do programador do Azure funções c# script (. csx)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Este artigo é uma introdução ao desenvolvimento das funções do Azure com o script c# (*. csx*).

As funções do Azure suporta c# e c# script linguagens de programação. Se estiver à procura de documentação de orientação [usando a linguagem c# num projeto de biblioteca de classes do Visual Studio](functions-develop-vs.md), consulte [referência para programadores sobre c#](functions-dotnet-class-library.md).

Este artigo pressupõe que já leu a [guia de programadores do funções do Azure](functions-reference.md).

## <a name="how-csx-works"></a>Como funciona. csx

A experiência de script c# das funções do Azure baseia-se sobre o [SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Os dados fluem para sua função de c# por meio de argumentos de método. Argumento nomes estão especificados num `function.json` de ficheiros e estão predefinidas nomes para aceder a coisas como os tokens de agente de log e cancelamento de função.

O *. csx* formato permite-lhe escrever menos "automático" e se concentre em escrever apenas uma função c#. Em vez de encapsular tudo num espaço de nomes e uma classe, só tem de definir um `Run` método. Inclua quaisquer referências a assembly e espaços de nomes no início do ficheiro como habitualmente.

Uma aplicação de funções *. csx* arquivos são compilados Quando uma instância é inicializada. Este passo de compilação significa coisas como a frio pode demorar mais tempo para funções de script do c# em comparação comparadas bibliotecas de classes do c#. Este passo de compilação também é por que funções de script do c# são editáveis no portal do Azure, embora não sejam bibliotecas de classes do c#.

## <a name="folder-structure"></a>estrutura de pastas

A estrutura de pastas para um projeto de script do c# é semelhante ao seguinte:

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

Existe um partilhada [Host. JSON](functions-host-json.md) ficheiro que pode ser utilizado para configurar a aplicação de função. Cada função tem seu próprio arquivo de código (. csx) e o ficheiro de configuração de vinculação (Function).

As extensões de vinculação necessárias [versão 2.x](functions-versions.md) runtime das funções definidas no `extensions.csproj` arquivo, com os ficheiros de biblioteca real no `bin` pasta. Ao desenvolver localmente, deve [registar as extensões de vinculação](./functions-bindings-register.md#local-development-azure-functions-core-tools). Ao desenvolver funções no portal do Azure, este registo é feito para.

## <a name="binding-to-arguments"></a>Vinculando a argumentos

Dados de entrada ou de saída estão vinculados a um c# script parâmetro da função através do `name` propriedade no *Function* ficheiro de configuração. A exemplo a seguir mostra um *Function* ficheiro e *csx* ficheiro para uma função acionada por fila. O parâmetro que recebe dados da mensagem de fila com o nome `myQueueItem` uma vez que é o valor da `name` propriedade.

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

O `#r` instrução é explicada [mais adiante neste artigo](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Tipos com suporte para enlaces

Cada vinculação tem seus próprios tipos com suporte; Por exemplo, um acionador de blob pode ser utilizado com um parâmetro de cadeia de caracteres, um parâmetro POCO, um `CloudBlockBlob` parâmetro ou qualquer um dos vários outros tipos de suporte. O [artigo de referência de ligação para enlaces de BLOBs](functions-bindings-storage-blob.md#trigger---usage) tipos de parâmetro de lista de todas as suportada para acionadores de Blobs. Para obter mais informações, consulte [Acionadores e enlaces](functions-triggers-bindings.md) e o [documentos de referência de ligação para cada tipo de enlace](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Referência de classes personalizadas

Se precisar de utilizar uma classe de Plain Old CLR Object (POCO) personalizada, pode incluir a definição de classe dentro do mesmo arquivo ou colocá-lo num arquivo separado.

A exemplo a seguir mostra um *csx* exemplo que inclui uma definição de classe POCO.

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

Uma classe POCO tem de ter um getter e setter definido para cada propriedade.

## <a name="reusing-csx-code"></a>Reutilização de código. csx

Pode utilizar classes e métodos definidos em outras *. csx* arquivos no seu *csx* ficheiro. Para tal, utilize `#load` diretivas no seu *csx* ficheiro. No exemplo seguinte, o nome de uma rotina de registo `MyLogger` partilhados no *myLogger.csx* e carregado no *csx* usando o `#load` diretiva:

Exemplo *csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemplo *mylogger.csx*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Usando um partilhada *. csx* ficheiro é um padrão comum quando se deseja vivamente digitar os dados passados entre as funções por usando um objeto POCO. No exemplo a seguir simplificado, um acionador HTTP e o acionador de fila partilham um objeto POCO chamado `Order` vivamente digitar os dados do pedido:

Exemplo *csx* para o acionador HTTP:

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

Exemplo *csx* para o acionador de fila:

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

Exemplo *order.csx*:

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

Pode utilizar um caminho relativo com o `#load` diretiva:

* `#load "mylogger.csx"` carrega um arquivo localizado na pasta de função.
* `#load "loadedfiles\mylogger.csx"` carrega um arquivo localizado numa pasta na pasta de função.
* `#load "..\shared\mylogger.csx"` carrega um arquivo localizado numa pasta no mesmo nível, como a pasta de função, ou seja, diretamente sob *wwwroot*.

O `#load` diretiva funciona apenas com *. csx* ficheiros, não com *. cs* ficheiros.

## <a name="binding-to-method-return-value"></a>Ligação para o valor de retorno do método

Pode utilizar um valor de retorno do método para um enlace de saída, ao utilizar o nome `$return` no *Function*. Para obter exemplos, consulte [Acionadores e enlaces](./functions-bindings-return-value.md).

Utilize o valor de retorno apenas se uma execução de função efetuada com êxito sempre resulta num valor de retorno para passar para o enlace de saída. Caso contrário, utilize `ICollector` ou `IAsyncCollector`, conforme mostrado na secção seguinte.

## <a name="writing-multiple-output-values"></a>Escrita de vários valores de saída

Para gravar vários valores para um enlace de saída, ou se uma invocação de função efetuada com êxito não pode resultar em nada para passar para o enlace de saída, utilize o [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) tipos. Esses tipos são coleções somente gravação, que são escritas para o enlace de saída quando o método for concluído.

Neste exemplo escreve vários de fila de mensagens em fila o mesmo por meio `ICollector`:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Registo

Para iniciar sessão a saída para os registos de transmissão em fluxo em C#, incluem um argumento do tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Recomendamos que atribua um nome `log`. Evite utilizar `Console.Write` nas funções do Azure.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Para obter informações sobre uma arquitetura de registo mais recente que pode utilizar em vez de `TraceWriter`, consulte [registos de escrita no c# funções](functions-monitoring.md#write-logs-in-c-functions) no **as funções do Azure de Monitor** artigo.

## <a name="async"></a>Async

Para tornar uma função [assíncrona](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), utilize o `async` palavra-chave e retornar um `Task` objeto.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Não é possível utilizar `out` parâmetros nas funções assíncronas. Para ligações de saída, utilize o [função de valor de retorno](#binding-to-method-return-value) ou uma [objeto recoletor](#writing-multiple-output-values) em vez disso.

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Uma função pode aceitar uma [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) parâmetro, que permite que o sistema operativo notificar o seu código quando a função está prestes a ser terminada. Pode utilizar esta notificação para se certificar de que a função não terminar inesperadamente de uma forma que mantém os dados num estado inconsistente.

O exemplo seguinte mostra como verificar a existência de terminação de função iminente.

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

## <a name="importing-namespaces"></a>Importação de espaços de nomes

Se precisar de importar os espaços de nomes, pode fazer por isso, normal, com o `using` cláusula.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Os espaços de nomes seguintes são automaticamente importados e, portanto, são opcionais:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Referência a assemblies externos

Para framework assemblies, adicione referências ao utilizar o `#r "AssemblyName"` diretiva.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

As assemblagens seguintes são adicionadas automaticamente por funções do Azure, ambiente de alojamento:

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

## <a name="referencing-custom-assemblies"></a>Referência a assemblies personalizados

Para referenciar um assembly personalizado, pode usar um *partilhada* assembly ou um *privada* assembly:

* Assemblies compartilhados são partilhados entre todas as funções dentro de uma aplicação de funções. Para referenciar um assembly personalizado, carregar o assembly para uma pasta denominada `bin` no seu [pasta de raiz de aplicação de função](functions-reference.md#folder-structure) (wwwroot).

* Assemblies privadas fazem parte do contexto de uma determinada função e suportam sideloading de versões diferentes. Assemblies privadas devem ser carregados num `bin` pasta no diretório de função. Referenciar os assemblies com o nome de ficheiro, tal como `#r "MyAssembly.dll"`.

Para obter informações sobre como carregar ficheiros para a pasta de função, consulte a secção sobre [gestão de pacotes](#using-nuget-packages).

### <a name="watched-directories"></a>Diretórios assistidos a

O diretório que contém o ficheiro de script de função é automaticamente assistiu para que as alterações aos assemblies. Para ver as alterações de assembly em outros diretórios, adicioná-los para o `watchDirectories` listar, num [Host. JSON](functions-host-json.md).

## <a name="using-nuget-packages"></a>Utilizar pacotes de NuGet
Usar pacotes NuGet num 2.x C# função, carregar um *function.proj* ficheiro para de pasta a função no sistema de ficheiros da aplicação de função. Eis um exemplo *function.proj* arquivo que adiciona uma referência ao *Microsoft.ProjectOxford.Face* versão *1.1.0*:

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

Para utilizar um feed de NuGet personalizado, especifique o feed num *Nuget.Config* ficheiro na raiz da aplicação de funções. Para obter mais informações, consulte [comportamento de configurar o NuGet](/nuget/consume-packages/configuring-nuget-behavior). 

> [!NOTE]
> No 1.x C# funções, pacotes de NuGet são referenciadas com um *Project* de ficheiros em vez de uma *function.proj* ficheiro.

Para funções de 1.x, utilize um *Project* de ficheiros em vez disso. Eis um exemplo *Project* ficheiro: 

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

### <a name="using-a-functionproj-file"></a>Através de um ficheiro de function.proj

1. Abra a função no portal do Azure. Separador registos apresenta o resultado de instalação do pacote.
2. Para carregar um *function.proj* de ficheiros, utilize um dos métodos descritos os [como atualizar os ficheiros de aplicação de função](functions-reference.md#fileupdate) no tópico de referência do programador das funções do Azure.
3. Depois do *function.proj* ficheiro é carregado, verá a saída semelhante ao seguinte exemplo na sua função da transmissão em fluxo o registo:

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

Para obter uma variável de ambiente ou uma aplicação de definir o valor, utilize `System.Environment.GetEnvironmentVariable`, conforme mostrado no exemplo de código a seguir:

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

## <a name="binding-at-runtime"></a>Ligação em tempo de execução

No c# e de outras linguagens .NET, pode usar uma [imperativo](https://en.wikipedia.org/wiki/Imperative_programming) padrão, de ligação, em vez do [ *declarativa* ](https://en.wikipedia.org/wiki/Declarative_programming) ligações em *Function*. Ligação de imperativa é útil quando os parâmetros de ligação tem de ser computado em tempo de tempo de execução, em vez de design. Com esse padrão, é possível vincular à suportada de entrada e saída enlaces no momento no código da função.

Defina um imperativo enlace da seguinte forma:

- **Isso não é possível** incluir uma entrada no *Function* para suas vinculações imperativas pretendidas.
- Passe um parâmetro de entrada [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Utilize o seguinte em C# padrão para efetuar a ligação de dados.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` é o atributo de .NET que define a sua ligação e `T` é um tipo de entrada ou de saída que é suportado por esse tipo de enlace. `T` não pode ser uma `out` tipo de parâmetro (como `out JObject`). Por exemplo, a tabela de aplicações móveis saída suporta vinculação [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas só pode utilizar [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)para `T`.

### <a name="single-attribute-example"></a>Exemplo de atributo único

O código de exemplo seguinte cria um [enlace de saída do blob de armazenamento](functions-bindings-storage-blob.md#output) com BLOBs caminho definido em tempo de execução, em seguida, escreve uma cadeia de caracteres para o blob.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) define o [blob de armazenamento](functions-bindings-storage-blob.md) entrada ou saída de ligação, e [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) é um tipo de enlace de saída suportados.

### <a name="multiple-attribute-example"></a>Exemplo de atributo vários

O exemplo anterior obtém a definição de aplicação para a cadeia de ligação de conta da aplicação de função principal armazenamento (que é `AzureWebJobsStorage`). Pode especificar uma definição de aplicação personalizada para utilizar para a conta de armazenamento ao adicionar o [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando a matriz de atributo na `BindAsync<T>()`. Utilize um `Binder` parâmetro, não `IBinder`.  Por exemplo:

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

A tabela seguinte lista os atributos de .NET para cada tipo de enlace e os pacotes que são definidos.

> [!div class="mx-codeBreakAll"]
| Vínculo | Atributo | Adicionar referência |
|------|------|------|
| BD do Cosmos | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
| Hubs de Eventos | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Hubs de Notificação | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Fila de armazenamento | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Blob de armazenamento | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Tabela de armazenamento | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os acionadores e enlaces](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as melhores práticas para as funções do Azure](functions-best-practices.md)
