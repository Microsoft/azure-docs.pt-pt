---
title: Referência C# do Azure Functions Developer
description: Entenda como desenvolver Azure Functions usando C#o.
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: f412e5ea358fe7b97476802f432616c37b05dbd9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975487"
---
# <a name="azure-functions-c-developer-reference"></a>Referência C# do Azure Functions Developer

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Este artigo é uma introdução ao desenvolvimento de Azure Functions usando C# as bibliotecas de classes .net.

O Azure Functions C# suporta C# e linguagens de programação de script. Se você estiver procurando orientação sobre como [usar C# o portal do Azure](functions-create-function-app-portal.md), consulte [ C# referência do desenvolvedor de script (. CSX)](functions-reference-csharp.md).

Este artigo pressupõe que você já leu os seguintes artigos:

* [Guia de desenvolvedores do Azure Functions](functions-reference.md)
* [Ferramentas do Azure Functions Visual Studio 2019](functions-develop-vs.md)

## <a name="supported-versions"></a>Versões suportadas

As versões do tempo de execução do Functions funcionam com versões específicas do .NET. A tabela a seguir mostra o nível mais alto de .NET Core e .NET Framework e .NET Core que podem ser usados com uma versão específica de funções em seu projeto. 

| Versão de tempo de execução do Functions | Versão máxima do .NET |
| ---- | ---- |
| Funções 3. x | .NET Core 3,1 |
| Funções 2.x | .NET Core 2.2 |
| Funções 1.x | .NET Framework 4.6 |

Para saber mais, consulte [visão geral das versões do Azure Functions Runtime](functions-versions.md)

## <a name="functions-class-library-project"></a>Projeto de biblioteca de classes de funções

No Visual Studio, o modelo de projeto **Azure Functions** cria C# um projeto de biblioteca de classes que contém os seguintes arquivos:

* [host. JSON](functions-host-json.md) – armazena as definições de configuração que afetam todas as funções no projeto quando executadas localmente ou no Azure.
* [local. Settings. JSON](functions-run-local.md#local-settings-file) – armazena as configurações do aplicativo e as cadeias de conexão que são usadas durante a execução local. Este arquivo contém segredos e não é publicado em seu aplicativo de funções no Azure. Em vez disso, [adicione configurações de aplicativo ao seu aplicativo de funções](functions-develop-vs.md#function-app-settings).

Quando você cria o projeto, uma estrutura de pastas parecida com o exemplo a seguir é gerada no diretório de saída da compilação:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Esse diretório é o que é implantado em seu aplicativo de funções no Azure. As extensões de associação necessárias na [versão 2. x](functions-versions.md) do tempo de execução do Functions são [adicionadas ao projeto como pacotes NuGet](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> O processo de compilação cria um arquivo *Function. JSON* para cada função. Este arquivo *Function. JSON* não deve ser editado diretamente. Você não pode alterar a configuração de associação ou desabilitar a função editando esse arquivo. Para saber como desabilitar uma função, consulte [como desabilitar funções](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Métodos reconhecidos como funções

Em uma biblioteca de classes, uma função é um método estático com um `FunctionName` e um atributo de gatilho, conforme mostrado no exemplo a seguir:

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

O atributo `FunctionName` marca o método como um ponto de entrada de função. O nome deve ser exclusivo em um projeto, começar com uma letra e conter apenas letras, números, `_`e `-`, até 127 caracteres de comprimento. Modelos de projeto geralmente criam um método chamado `Run`, mas o nome do método pode ser C# qualquer nome de método válido.

O atributo Trigger especifica o tipo de gatilho e associa os dados de entrada a um parâmetro de método. A função de exemplo é disparada por uma mensagem da fila e a mensagem da fila é passada para o método no parâmetro `myQueueItem`.

## <a name="method-signature-parameters"></a>Parâmetros de assinatura do método

A assinatura do método pode conter parâmetros diferentes daquele usado com o atributo Trigger. Aqui estão alguns dos parâmetros adicionais que você pode incluir:

* [Associações de entrada e saída](functions-triggers-bindings.md) marcadas como tal, decorando-as com atributos.  
* Um parâmetro `ILogger` ou `TraceWriter` ([versão 1. x somente](functions-versions.md#creating-1x-apps)) para [registro em log](#logging).
* Um parâmetro `CancellationToken` para [desligamento normal](#cancellation-tokens).
* Parâmetros de [expressões de associação](./functions-bindings-expressions-patterns.md) para obter metadados do gatilho.

A ordem dos parâmetros na assinatura da função não importa. Por exemplo, você pode colocar parâmetros de gatilho antes ou depois de outras associações, e pode colocar o parâmetro do agente antes ou depois dos parâmetros de gatilho ou de associação.

### <a name="output-binding-example"></a>Exemplo de associação de saída

O exemplo a seguir modifica o anterior, adicionando uma associação de fila de saída. A função grava a mensagem da fila que dispara a função para uma nova mensagem da fila em uma fila diferente.

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

Os artigos de referência de associação ([filas de armazenamento](functions-bindings-storage-queue.md), por exemplo) explicam quais tipos de parâmetro você pode usar com atributos de associação de gatilho, entrada ou saída.

### <a name="binding-expressions-example"></a>Exemplo de expressões de associação

O código a seguir obtém o nome da fila a ser monitorada de uma configuração de aplicativo e obtém a hora de criação da mensagem da fila no parâmetro `insertionTime`.

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

## <a name="autogenerated-functionjson"></a>Function. JSON gerado automaticamente

O processo de compilação cria um arquivo *Function. JSON* em uma pasta de funções na pasta Build. Conforme observado anteriormente, esse arquivo não deve ser editado diretamente. Você não pode alterar a configuração de associação ou desabilitar a função editando esse arquivo. 

A finalidade desse arquivo é fornecer informações para o controlador de escala a ser usado para as [decisões de dimensionamento no plano de consumo](functions-scale.md#how-the-consumption-and-premium-plans-work). Por esse motivo, o arquivo tem apenas informações de gatilho, não associações de entrada ou saída.

O arquivo *Function. JSON* gerado inclui uma propriedade `configurationSource` que informa o tempo de execução para usar atributos .net para associações, em vez de configuração de *Function. JSON* . Segue-se um exemplo:

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

A geração de arquivo *Function. JSON* é executada pelo pacote NuGet [Microsoft\.NET\.SDK\.funções](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

O mesmo pacote é usado para a versão 1. x e 2. x do tempo de execução do functions. A estrutura de destino é o que diferencia um projeto 1. x de um projeto 2. x. Aqui estão as partes relevantes de arquivos *. csproj* , mostrando estruturas de destino diferentes e o mesmo pacote de `Sdk`:

**Funções 1. x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Funções 2. x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Entre as dependências do pacote `Sdk` são gatilhos e associações. Um projeto 1. x refere-se a gatilhos e associações de 1. x porque esses gatilhos e associações se destinam ao .NET Framework, enquanto os gatilhos de 2. x e associações são direcionadas ao .NET Core.

O pacote de `Sdk` também depende de [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json)e indiretamente em [WindowsAzure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Essas dependências garantem que seu projeto Use as versões desses pacotes que funcionam com a versão de tempo de execução do Functions que o projeto tem como destino. Por exemplo, `Newtonsoft.Json` tem a versão 11 para .NET Framework 4.6.1, mas o tempo de execução do Functions que tem como alvo .NET Framework 4.6.1 é compatível apenas com `Newtonsoft.Json` 9.0.1. Portanto, o código de função nesse projeto também precisa usar `Newtonsoft.Json` 9.0.1.

O código-fonte para `Microsoft.NET.Sdk.Functions` está disponível no repositório GitHub [funções do azure\-\-\-\-SDK](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Versão de tempo de execução

O Visual Studio usa o [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) para executar projetos do functions. As ferramentas principais são uma interface de linha de comando para o tempo de execução do functions.

Se você instalar as ferramentas principais usando o NPM, isso não afetará a versão das ferramentas principais usada pelo Visual Studio. Para o Functions Runtime versão 1. x, o Visual Studio armazena versões de ferramentas principais no *%USERPROFILE%\AppData\Local\Azure.Functions.CLI* e usa a versão mais recente armazenada lá. Para o Functions 2. x, as ferramentas principais estão incluídas no Azure Functions e na extensão de **ferramentas de trabalhos da Web** . Para 1. x e 2. x, você pode ver qual versão está sendo usada na saída do console ao executar um projeto do Functions:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Tipos com suporte para associações

Cada associação tem seus próprios tipos com suporte; por exemplo, um atributo de gatilho de blob pode ser aplicado a um parâmetro de cadeia de caracteres, um parâmetro POCO, um parâmetro de `CloudBlockBlob` ou qualquer um dos vários outros tipos com suporte. O [artigo de referência de associação para associações de blob](functions-bindings-storage-blob.md#trigger---usage) lista todos os tipos de parâmetro com suporte. Para obter mais informações, consulte [gatilhos e associações](functions-triggers-bindings.md) e os [documentos de referência de associação para cada tipo de associação](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Associação ao valor de retorno do método

Você pode usar um valor de retorno de método para uma associação de saída, aplicando o atributo ao valor de retorno do método. Para obter exemplos, consulte [gatilhos e associações](./functions-bindings-return-value.md). 

Use o valor de retorno somente se uma execução de função bem-sucedida sempre resultar em um valor de retorno para passar para a associação de saída. Caso contrário, use `ICollector` ou `IAsyncCollector`, conforme mostrado na seção a seguir.

## <a name="writing-multiple-output-values"></a>Gravando vários valores de saída

Para gravar vários valores em uma associação de saída ou se uma invocação de função bem-sucedida pode não resultar em nada para passar para a associação de saída, use os tipos [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) . Esses tipos são coleções somente gravação que são gravadas na associação de saída quando o método é concluído.

Este exemplo grava várias mensagens da fila na mesma fila usando `ICollector`:

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

Para registrar a saída em seus logs de C#streaming no, inclua um argumento do tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). É recomendável que você o nomeie `log`, como no exemplo a seguir:  

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

Evite usar `Console.Write` em Azure Functions. Para obter mais informações, consulte [gravar logs C# em funções](functions-monitoring.md#write-logs-in-c-functions) no artigo **Azure Functions do monitor** .

## <a name="async"></a>Async

Para tornar uma função [assíncrona](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), use a palavra-chave `async` e retorne um objeto `Task`.

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

Você não pode usar parâmetros de `out` em funções assíncronas. Para associações de saída, use o [valor de retorno da função](#binding-to-method-return-value) ou um [objeto do coletor](#writing-multiple-output-values) em vez disso.

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Uma função pode aceitar um parâmetro [CancellationToken](/dotnet/api/system.threading.cancellationtoken) , que permite que o sistema operacional Notifique seu código quando a função está prestes a ser encerrada. Você pode usar essa notificação para garantir que a função não seja encerrada inesperadamente de forma a deixar dados em um estado inconsistente.

O exemplo a seguir mostra como verificar a terminação de função iminente.

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

Para obter uma variável de ambiente ou um valor de configuração de aplicativo, use `System.Environment.GetEnvironmentVariable`, conforme mostrado no exemplo de código a seguir:

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

As configurações do aplicativo podem ser lidas de variáveis de ambiente ao desenvolver localmente e ao executar no Azure. Ao desenvolver localmente, as configurações do aplicativo são provenientes da coleção de `Values` no arquivo *local. Settings. JSON* . Em ambos os ambientes, local e Azure, `GetEnvironmentVariable("<app setting name>")` recupera o valor da configuração de aplicativo nomeado. Por exemplo, quando você estiver executando localmente, o "nome do meu site" será retornado se o arquivo *local. Settings. JSON* contiver `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

A propriedade [System. Configuration. ConfigurationManager. AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) é uma API alternativa para obter valores de configuração de aplicativo, mas recomendamos que você use `GetEnvironmentVariable` como mostrado aqui.

## <a name="binding-at-runtime"></a>Associação em tempo de execução

No C# e em outras linguagens .net, você pode usar um padrão de associação [imperativo](https://en.wikipedia.org/wiki/Imperative_programming) , em oposição às associações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em atributos. A associação imperativa é útil quando parâmetros de associação precisam ser computados em tempo de execução em vez de tempo de design. Com esse padrão, você pode associar a associações de entrada e saída com suporte imediatamente no seu código de função.

Defina uma associação imperativa da seguinte maneira:

- **Não** inclua um atributo na assinatura da função para suas associações imperativas desejadas.
- Passe um parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Use o padrão C# a seguir para executar a vinculação de dados.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` é o atributo .NET que define sua associação e `T` é um tipo de entrada ou saída com suporte nesse tipo de associação. `T` não pode ser um tipo de parâmetro `out` (como `out JObject`). Por exemplo, a associação de saída de tabela de aplicativos móveis dá suporte a [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas você só pode usar [ICollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) com associação imperativa.

### <a name="single-attribute-example"></a>Exemplo de atributo único

O código de exemplo a seguir cria uma [Associação de saída de blob de armazenamento](functions-bindings-storage-blob.md#output) com o caminho de blob definido em tempo de execução e, em seguida, grava uma cadeia de caracteres no BLOB.

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

[Blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) define a associação de entrada ou saída de [blob de armazenamento](functions-bindings-storage-blob.md) , e [TextWriter](/dotnet/api/system.io.textwriter) é um tipo de associação de saída com suporte.

### <a name="multiple-attribute-example"></a>Exemplo de atributo múltiplo

O exemplo anterior Obtém a configuração do aplicativo para a cadeia de conexão da conta de armazenamento principal do aplicativo de funções (que é `AzureWebJobsStorage`). Você pode especificar uma configuração de aplicativo personalizada a ser usada para a conta de armazenamento adicionando o [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando a matriz de atributos para `BindAsync<T>()`. Use um parâmetro `Binder`, não `IBinder`.  Por exemplo:

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre gatilhos e associações](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as práticas recomendadas para Azure Functions](functions-best-practices.md)
