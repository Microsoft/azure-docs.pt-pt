---
title: Referência do Azure funções c# para programadores
description: Aprenda a desenvolver as funções do Azure com c#.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/12/2018
ms.author: glenga
ms.openlocfilehash: b54585805a2d94d1b41c1ec39aea911ed05b3ca7
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193327"
---
# <a name="azure-functions-c-developer-reference"></a>Referência do Azure funções c# para programadores

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Este artigo é uma introdução ao desenvolvimento das funções do Azure com o c# nas bibliotecas de classes do .NET.

As funções do Azure suporta c# e c# script linguagens de programação. Se estiver à procura de documentação de orientação [usando c# no portal do Azure](functions-create-function-app-portal.md), consulte [referência do desenvolvedor do c# script (. csx)](functions-reference-csharp.md).

Este artigo pressupõe que já leu os artigos seguintes:

* [Guia para programadores do funções do Azure](functions-reference.md)
* [Ferramentas do Visual Studio 2017 das funções do Azure](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Projeto de biblioteca de classes de funções

No Visual Studio, o **as funções do Azure** modelo de projeto cria um c# classe projeto de biblioteca que contém os seguintes ficheiros:

* [Host. JSON](functions-host-json.md) -armazena as definições de configuração que afetam todas as funções no projeto ao executar localmente ou no Azure.
* [Settings](functions-run-local.md#local-settings-file) -armazena as definições da aplicação e as cadeias de ligação que são utilizadas ao executar localmente. Este ficheiro contém segredos e não é publicado na sua aplicação de função no Azure. Em vez disso, tem [adicionar as definições da aplicação à sua aplicação de função](functions-develop-vs.md#function-app-settings).

Quando compila o projeto, uma estrutura de pastas que se assemelha ao seguinte é gerado na compilação diretório de saída:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Este diretório é o que é implementado para a aplicação de funções no Azure. As extensões de vinculação necessárias [versão 2.x](functions-versions.md) as funções de tempo de execução são [adicionada ao projeto como pacotes de NuGet](./functions-bindings-register.md#c-class-library-with-visual-studio-2017).

> [!IMPORTANT]
> O processo de compilação cria um *Function* ficheiro para cada função. Isso *Function* ficheiro não deve ser editada diretamente. Não é possível alterar a configuração de ligação ou desativar a função ao editar este ficheiro. Para saber como desativar uma função, veja [como desativar funções](disable-function.md#functions-2x---c-class-libraries).

## <a name="methods-recognized-as-functions"></a>Métodos reconhecidos como funções

Numa biblioteca de classe, uma função é um método estático com um `FunctionName` e um atributo de Acionador, conforme mostrado no exemplo a seguir:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

O `FunctionName` atributo marca o método como um ponto de entrada da função. O nome tem de ser exclusivo dentro de um projeto, começar com uma letra e conter apenas letras, números `_` e `-`, até 127 carateres de comprimento. Modelos de projeto, muitas vezes, criam um método chamado `Run`, mas o nome do método pode ser qualquer nome de método do c# válido.

O atributo de Acionador Especifica o tipo de Acionador e vincula os dados de entrada para um parâmetro de método. A função de exemplo é acionada por uma mensagem de fila e a mensagem de fila é passada para o método no `myQueueItem` parâmetro.

## <a name="method-signature-parameters"></a>Parâmetros de assinatura do método

A assinatura do método pode conter parâmetros que não seja usado com o atributo de Acionador. Aqui estão alguns dos parâmetros adicionais que pode incluir:

* [Entrada e saída enlaces](functions-triggers-bindings.md) marcados como tal decorando-los com atributos.  
* Uma `ILogger` ou `TraceWriter` ([versão 1.x só](functions-versions.md#creating-1x-apps)) parâmetro [registo](#logging).
* R `CancellationToken` parâmetro [desligamento não](#cancellation-tokens).
* [Expressões de enlace](./functions-bindings-expressions-patterns.md) acionam de parâmetros para obter metadados.

A ordem dos parâmetros na assinatura de função não é relevante. Por exemplo, pode colocar os parâmetros de Acionador antes ou depois de outras associações, e pode colocar o parâmetro de agente de log antes ou depois de parâmetros de Acionador ou enlace.

### <a name="output-binding-example"></a>Exemplo de enlace de saída

O exemplo seguinte modifica o um anterior ao adicionar um enlace de fila de saída. A função escreve a mensagem de fila que aciona a função para uma nova mensagem de fila numa fila diferente.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Os artigos de referência de ligação ([filas de armazenamento](functions-bindings-storage-queue.md), por exemplo) explicar quais tipos de parâmetro que pode utilizar com o acionador, entrada ou atributos de enlace de saída.

### <a name="binding-expressions-example"></a>Exemplo de expressões de enlace

O código seguinte obtém o nome da fila para monitorizar a partir de uma definição de aplicação, e ele obtém a hora de criação de mensagem de fila a `insertionTime` parâmetro.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Function geradas automaticamente

O processo de compilação cria um *Function* arquivo numa pasta de função na pasta de compilação. Conforme observado anteriormente, este ficheiro não deve ser editada diretamente. Não é possível alterar a configuração de ligação ou desativar a função ao editar este ficheiro. 

O objetivo deste ficheiro é fornecer informações para o controlador de dimensionamento para utilizar para [dimensionamento decisões sobre o plano de consumo](functions-scale.md#how-the-consumption-plan-works). Por esse motivo, o ficheiro tem apenas informações de Acionador, não de entrada ou ligações de saída.

O gerado *Function* ficheiro inclui um `configurationSource` propriedade que indica o tempo de execução a utilização de atributos de .NET para associações, em vez *Function* configuração. Segue-se um exemplo:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

O *Function* geração de ficheiros é efetuada pelo pacote NuGet [Microsoft\.NET\.Sdk\.funções](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

O mesmo pacote é utilizado para as versões 1.x e 2.x do runtime das funções. A estrutura de destino é o que diferencia um projeto de 1.x de um projeto de 2.x. Aqui estão as partes relevantes *. csproj* unidades de destino em ficheiros, que mostra diferentes estruturas e o mesmo `Sdk` pacote:

**As funções 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**As funções 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Entre o `Sdk` as dependências de pacote são acionadores e enlaces. Um projeto de 1.x refere-se para 1.x acionadores e enlaces porque aquelas têm como destino o .NET Framework, embora 2.x acionadores e enlaces .NET Core de destino.

O `Sdk` pacote também depende [newtonsoft](https://www.nuget.org/packages/Newtonsoft.Json)e, no indiretamente [windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Estas dependências, certifique-se de que o seu projeto usa as versões desses pacotes que funcionam com a versão de runtime das funções que os destinos de projeto. Por exemplo, `Newtonsoft.Json` tem a versão 11 para o .NET Framework 4.6.1, mas só é compatível com o runtime das funções que tenha como destino o .NET Framework 4.6.1 `Newtonsoft.Json` 9.0.1. Para que o código de função desse projeto também tem de utilizar `Newtonsoft.Json` 9.0.1.

O código-fonte `Microsoft.NET.Sdk.Functions` está disponível no repositório do GitHub [azure\-funções\-vs\-criar\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Versão de runtime

O Visual Studio utiliza o [ferramentas de núcleo de funções do Azure](functions-run-local.md#install-the-azure-functions-core-tools) para executar projetos de funções. As ferramentas de núcleo é uma interface de linha de comandos para o runtime das funções.

Se instalar as ferramentas de núcleo com npm, que não afeta a versão de ferramentas de núcleo utilizada pelo Visual Studio. Para a versão de runtime das funções 1.x, Visual Studio armazena as versões de ferramentas de núcleo na *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* e utiliza a versão mais recente armazenada aqui. Para funções 2.x, as ferramentas de núcleo estão incluídas no **funções do Azure e ferramentas do Web Jobs** extensão. Para 1.x e 2.x, pode ver qual a versão está a ser utilizada na saída da consola, quando executar um projeto de funções:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Tipos com suporte para enlaces

Cada vinculação tem seus próprios tipos com suporte; Por exemplo, um atributo de Acionador de blob pode ser aplicado a um parâmetro de cadeia de caracteres, um parâmetro POCO, um `CloudBlockBlob` parâmetro ou qualquer um dos vários outros tipos de suporte. O [artigo de referência de ligação para enlaces de BLOBs](functions-bindings-storage-blob.md#trigger---usage) tipos de parâmetro de lista de todas as suportado. Para obter mais informações, consulte [Acionadores e enlaces](functions-triggers-bindings.md) e o [documentos de referência de ligação para cada tipo de enlace](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Ligação para o valor de retorno do método

Pode utilizar um valor de retorno do método para um enlace de saída, ao aplicar o atributo para o valor de retorno do método. Para obter exemplos, consulte [Acionadores e enlaces](./functions-bindings-return-value.md). 

Utilize o valor de retorno apenas se uma execução de função efetuada com êxito sempre resulta num valor de retorno para passar para o enlace de saída. Caso contrário, utilize `ICollector` ou `IAsyncCollector`, conforme mostrado na secção seguinte.

## <a name="writing-multiple-output-values"></a>Escrita de vários valores de saída

Para gravar vários valores para um enlace de saída, ou se uma invocação de função efetuada com êxito não pode resultar em nada para passar para o enlace de saída, utilize o [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) tipos. Esses tipos são coleções somente gravação, que são escritas para o enlace de saída quando o método for concluído.

Neste exemplo escreve vários de fila de mensagens em fila o mesmo por meio `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Registo

Para iniciar sessão a saída para os registos de transmissão em fluxo em C#, incluem um argumento do tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Recomendamos que atribua um nome `log`, como no exemplo a seguir:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Evite utilizar `Console.Write` nas funções do Azure. Para obter mais informações, consulte [registos de escrita no C# funções](functions-monitoring.md#write-logs-in-c-functions) no **as funções do Monitor do Azure** artigo.

## <a name="async"></a>Async

Para tornar uma função [assíncrona](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), utilize o `async` palavra-chave e retornar um `Task` objeto.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Não é possível utilizar `out` parâmetros nas funções assíncronas. Para ligações de saída, utilize o [função de valor de retorno](#binding-to-method-return-value) ou uma [objeto recoletor](#writing-multiple-output-values) em vez disso.

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Uma função pode aceitar uma [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) parâmetro, que permite que o sistema operativo notificar o seu código quando a função está prestes a ser terminada. Pode utilizar esta notificação para se certificar de que a função não terminar inesperadamente de uma forma que mantém os dados num estado inconsistente.

O exemplo seguinte mostra como verificar a existência de terminação de função iminente.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
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
}
```

## <a name="environment-variables"></a>Variáveis de ambiente

Para obter uma variável de ambiente ou uma aplicação de definir o valor, utilize `System.Environment.GetEnvironmentVariable`, conforme mostrado no exemplo de código a seguir:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
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
}
```

Definições da aplicação podem ser lidos de variáveis de ambiente ao desenvolver localmente e quando em execução no Azure. Ao desenvolver localmente, definições de aplicações provenientes da `Values` coleção na *Settings* ficheiro. Nos dois ambientes locais e do Azure, `GetEnvironmentVariable("<app setting name>")` recupera o valor da definição de aplicação nomeada. Por exemplo, quando estiver a executar localmente, "Meu Site Name" seria devolvido se a sua *Settings* arquivo contém `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

O [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) propriedade é uma API alternativa para obter os valores de definição de aplicação, mas recomendamos que utilize `GetEnvironmentVariable` conforme mostrado aqui.

## <a name="binding-at-runtime"></a>Ligação em tempo de execução

Em c# e de outras linguagens .NET, pode usar uma [imperativo](https://en.wikipedia.org/wiki/Imperative_programming) padrão, de ligação, em vez do [ *declarativa* ](https://en.wikipedia.org/wiki/Declarative_programming) enlaces de atributos. Ligação de imperativa é útil quando os parâmetros de ligação tem de ser computado em tempo de tempo de execução, em vez de design. Com esse padrão, é possível vincular à suportada de entrada e saída enlaces no momento no código da função.

Defina um imperativo enlace da seguinte forma:

- **Isso não é possível** incluem um atributo na assinatura de função para suas vinculações imperativas pretendidas.
- Passe um parâmetro de entrada [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Utilize o seguinte em C# padrão para efetuar a ligação de dados.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` é o atributo de .NET que define a sua ligação, e `T` é um tipo de entrada ou de saída que é suportado por esse tipo de enlace. `T` não pode ser uma `out` tipo de parâmetro (como `out JObject`). Por exemplo, a tabela de aplicações móveis saída suporta vinculação [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas só pode utilizar [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)com a vinculação imperativa.

### <a name="single-attribute-example"></a>Exemplo de atributo único

O código de exemplo seguinte cria um [enlace de saída do blob de armazenamento](functions-bindings-storage-blob.md#output) com BLOBs caminho definido em tempo de execução, em seguida, escreve uma cadeia de caracteres para o blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) define o [blob de armazenamento](functions-bindings-storage-blob.md) entrada ou saída de ligação, e [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) é um tipo de enlace de saída suportados.

### <a name="multiple-attribute-example"></a>Exemplo de atributo vários

O exemplo anterior obtém a definição de aplicação para a cadeia de ligação de conta da aplicação de função principal armazenamento (que é `AzureWebJobsStorage`). Pode especificar uma definição de aplicação personalizada para utilizar para a conta de armazenamento ao adicionar o [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando a matriz de atributo na `BindAsync<T>()`. Utilize um `Binder` parâmetro, não `IBinder`.  Por exemplo:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Acionadores e enlaces 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os acionadores e enlaces](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as melhores práticas para as funções do Azure](functions-best-practices.md)
