---
title: Referência do C# desenvolvedor de Funções Azure
description: Entenda como desenvolver funções C#Azure utilizando .
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: cfa53fe2defca768196af595c1d088d41bc60f71
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356985"
---
# <a name="azure-functions-c-developer-reference"></a>Referência do C# desenvolvedor de Funções Azure

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Este artigo é uma introdução ao desenvolvimento C# de Funções Azure utilizando bibliotecas da classe .NET.

As Funções C# Azure C# suportam e programam linguagens. Se procura orientação sobre [ C# a utilização no portal Azure,](functions-create-function-app-portal.md)consulte [ C# a referência do desenvolvedor script (.csx).](functions-reference-csharp.md)

Este artigo assume que já leu os seguintes artigos:

* [Guia de desenvolvedores de funções azure](functions-reference.md)
* [Ferramentas Azure Functions Visual Studio 2019](functions-develop-vs.md)

## <a name="supported-versions"></a>Versões suportadas

As versões do tempo de funcionamento das Funções funcionam com versões específicas de .NET. O quadro seguinte mostra o nível mais elevado de .NET Core e .NET Framework e .NET Core que podem ser usados com uma versão específica de Funções no seu projeto. 

| Funções versão de tempo de execução | Versão Max .NET |
| ---- | ---- |
| Funções 3.x | .NET Core 3.1 |
| Funções 2.x | .NET Core 2.2 |
| Funções 1.x | .NET Framework 4.6 |

Para saber mais, consulte as versões de execução do Funcionamento do [Azure Functions](functions-versions.md)

## <a name="functions-class-library-project"></a>Projeto de biblioteca de classes de funções

No Estúdio Visual, o modelo de C# projeto **Funções Azure** cria um projeto de biblioteca de classes que contém os seguintes ficheiros:

* [host.json](functions-host-json.md) - armazena configurações de configuração que afetam todas as funções do projeto ao executar localmente ou em Azure.
* [local.settings.json](functions-run-local.md#local-settings-file) - armazena as definições de aplicativos e as cordas de ligação que são usadas quando estão a funcionar localmente. Este ficheiro contém segredos e não é publicado na sua aplicação de funções no Azure. Em vez disso, adicione as definições da [aplicação à sua aplicação de função](functions-develop-vs.md#function-app-settings).

Ao construir o projeto, uma estrutura de pasta que se parece com o seguinte exemplo é gerada no diretório de saída de construção:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Este diretório é o que é implantado na sua app de funções em Azure. As extensões de encadernação exigidas na [versão 2.x](functions-versions.md) do tempo de funcionamento das Funções são [adicionadas ao projeto como pacotes NuGet](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> O processo de construção cria um ficheiro *function.json* para cada função. Este ficheiro *função.json* não deve ser editado diretamente. Não pode alterar a configuração de ligação ou desativar a função editando este ficheiro. Para aprender a desativar uma função, consulte [como desativar as funções](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Métodos reconhecidos como funções

Numa biblioteca de classes, uma função é um método estático com um `FunctionName` e um atributo de gatilho, como mostra o seguinte exemplo:

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

O atributo `FunctionName` marca o método como ponto de entrada de função. O nome deve ser único dentro de um projeto, começar com uma letra e conter apenas letras, números, `_`, e `-`- até 127 caracteres de comprimento. Os modelos de projeto criam frequentemente um método C# chamado `Run`, mas o nome do método pode ser qualquer nome de método válido.

O atributo do gatilho especifica o tipo de gatilho e liga os dados de entrada a um parâmetro de método. A função de exemplo é desencadeada por uma mensagem de fila, e a mensagem de fila é transmitida ao método no parâmetro `myQueueItem`.

## <a name="method-signature-parameters"></a>Parâmetros de assinatura de método

A assinatura do método pode conter parâmetros diferentes dos utilizados com o atributo do gatilho. Aqui estão alguns dos parâmetros adicionais que pode incluir:

* [Encadernações](functions-triggers-bindings.md) de entrada e saída marcadas como tal, decorando-as com atributos.  
* Um parâmetro `ILogger` ou `TraceWriter`[(versão 1.x-only](functions-versions.md#creating-1x-apps)) para [a exploração madeireira](#logging).
* Um parâmetro `CancellationToken` para [uma paragem graciosa.](#cancellation-tokens)
* [Parâmetros de expressões de ligação](./functions-bindings-expressions-patterns.md) para obter metadados de gatilho.

A ordem dos parâmetros na assinatura da função não importa. Por exemplo, pode colocar parâmetros de gatilho antes ou depois de outras ligações, e pode colocar o parâmetro do madeireiro antes ou depois do gatilho ou dos parâmetros de ligação.

### <a name="output-binding-example"></a>Exemplo de encadernação de saída

O exemplo seguinte modifica o anterior adicionando uma ligação de fila de saída. A função escreve a mensagem de fila que desencadeia a função para uma nova mensagem de fila numa fila diferente.

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

Os artigos de referência de ligação (filas de[armazenamento,](functions-bindings-storage-queue.md)por exemplo) explicam quais os tipos de parâmetros que pode utilizar com atributos de ligação de gatilho, entrada ou saída.

### <a name="binding-expressions-example"></a>Exemplo de expressões vinculativas

O código seguinte obtém o nome da fila para monitorizar a partir de uma definição de app, e obtém o tempo de criação de mensagens de fila no parâmetro `insertionTime`.

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

## <a name="autogenerated-functionjson"></a>Função autogerada.json

O processo de construção cria um ficheiro *function.json* numa pasta de função na pasta de construção. Como notado anteriormente, este ficheiro não deve ser editado diretamente. Não pode alterar a configuração de ligação ou desativar a função editando este ficheiro. 

O objetivo deste ficheiro é fornecer informações ao controlador de escala para utilizar para [decisões](functions-scale.md#how-the-consumption-and-premium-plans-work)de escala no plano de consumo . Por esta razão, o ficheiro apenas tem informações de gatilho, não de ligações de entrada ou de saída.

O ficheiro *função.json* gerado inclui uma propriedade `configurationSource` que diz o tempo de funcionamento para usar atributos .NET para ligações, em vez de *configuração function.json.* Segue-se um exemplo:

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

A geração de *ficheiros function.json* é executada pelo pacote NuGet [Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

O mesmo pacote é utilizado para ambas as versões 1.x e 2.x do tempo de funcionamento das Funções. O quadro-alvo é o que diferencia um projeto 1.x de um projeto 2.x. Aqui estão as partes relevantes dos *ficheiros .csproj,* mostrando diferentes quadros-alvo e o mesmo pacote `Sdk`:

**Funções 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Funções 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Entre os `Sdk` dependências de pacotes estão gatilhos e encadernações. Um projeto de 1.x refere-se a gatilhos e encadernações de 1.x porque esses gatilhos e encadernações visam o Quadro .NET, enquanto 2.x desencadeia e encaderna o alvo .NET Core.

O pacote `Sdk` também depende da [Newtonsoft.Json,](https://www.nuget.org/packages/Newtonsoft.Json)e indiretamente do [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Estas dependências certificam-se de que o seu projeto utiliza as versões dos pacotes que funcionam com a versão de tempo de execução functions que o projeto tem como alvo. Por exemplo, `Newtonsoft.Json` tem a versão 11 para .NET Framework 4.6.1, mas o tempo de funcionamento das funções que visa .NET Framework 4.6.1 só é compatível com `Newtonsoft.Json` 9.0.1. Assim, o seu código de funcionamento nesse projeto também tem de usar `Newtonsoft.Json` 9.0.1.

O código fonte para `Microsoft.NET.Sdk.Functions` está disponível nas funções de\-de  do GitHub [\-vs\-construir\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Versão runtime

O Visual Studio utiliza as [Ferramentas Core funções do Azure](functions-run-local.md#install-the-azure-functions-core-tools) para executar projetos de funções. As Ferramentas Core são uma interface de linha de comando para o tempo de funcionamento das funções.

Se instalar as Ferramentas Core utilizando o npm, isso não afeta a versão Core Tools utilizada pelo Visual Studio. Para as funções runtime versão 1.x, visual Studio armazena versões Core Tools em *%USERPROFILE%\AppData\Local\Azure.Functions.Functions.* Para funções 2.x, as Ferramentas Core estão incluídas na extensão de Ferramentas de **Funcionamento Azul e Web Jobs.** Tanto para 1.x como 2.x, pode supor que versão está a ser utilizada na saída da consola quando executa um projeto Functions:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Tipos suportados para encadernações

Cada encadernação tem os seus próprios tipos suportados; por exemplo, um atributo do gatilho de bolha pode ser aplicado a um parâmetro de corda, um parâmetro POCO, um parâmetro `CloudBlockBlob` ou qualquer um de vários outros tipos suportados. O [artigo de referência vinculativo para encadernações blob](functions-bindings-storage-blob-trigger.md#usage) lista todos os tipos de parâmetros suportados. Para mais informações, consulte [Gatilhos e encadernações](functions-triggers-bindings.md) e os [docs de referência vinculativos para cada tipo de encadernação](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Vinculação ao valor de retorno do método

Pode utilizar um valor de devolução de método para uma ligação de saída, aplicando o atributo ao valor de retorno do método. Por exemplo, consulte [Gatilhos e encadernações](./functions-bindings-return-value.md). 

Utilize o valor de devolução apenas se uma execução de função bem sucedida resultar sempre num valor de retorno para passar para a ligação de saída. Caso contrário, utilize `ICollector` ou `IAsyncCollector`, como se pode ver na secção seguinte.

## <a name="writing-multiple-output-values"></a>Escrever vários valores de saída

Para escrever vários valores para uma ligação de saída, ou se uma invocação de função bem sucedida pode não resultar em nada para passar para a ligação de saída, use os [tipos`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [`IAsyncCollector`.](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) Estes tipos são coleções escritas apenas que são escritas para a ligação de saída quando o método completa.

Este exemplo escreve várias mensagens de fila na mesma fila usando `ICollector`:

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

Para iniciar sessão nos C#seus registos de streaming, inclua um argumento do tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Recomendamos que o nomeie `log`, como no seguinte exemplo:  

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

Evite utilizar `Console.Write` em funções Azure. Para mais informações, consulte [os registos de escrita em C# funções](functions-monitoring.md#write-logs-in-c-functions) no artigo **Funções Monitor Azure.**

## <a name="async"></a>Async

Para fazer uma função [assíncrona,](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)use a palavra-chave `async` e devolva um objeto `Task`.

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

Não se pode usar `out` parâmetros em funções de asincronização. Para encadernações de saída, utilize o valor de devolução da [função](#binding-to-method-return-value) ou um [objeto de coleção.](#writing-multiple-output-values)

## <a name="cancellation-tokens"></a>Fichas de cancelamento

Uma função pode aceitar um parâmetro [CancelamentoToken,](/dotnet/api/system.threading.cancellationtoken) que permite ao sistema operativo notificar o seu código quando a função está prestes a ser terminada. Pode utilizar esta notificação para se certificar de que a função não termina inesperadamente de uma forma que deixe os dados num estado inconsistente.

O exemplo que se segue mostra como verificar se há uma interrupção da função iminente.

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

Para obter uma variável ambiental ou um valor de definição de app, use `System.Environment.GetEnvironmentVariable`, como mostra o seguinte exemplo de código:

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

As definições de aplicativos podem ser lidas a partir de variáveis ambientais tanto quando se desenvolve localmente como quando se está a correr em Azure. Ao desenvolver localmente, as definições de aplicativos provêm da recolha `Values` no ficheiro *local.settings.json.* Em ambos os ambientes, local e Azure, `GetEnvironmentVariable("<app setting name>")` recupera o valor da configuração de aplicações nomeada. Por exemplo, quando estiver a funcionar localmente, "My Site Name" seria devolvido se o seu ficheiro *local.settings.json* contiver `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

A propriedade [System.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) é uma API alternativa para obter valores de definição de aplicações, mas recomendamos que utilize `GetEnvironmentVariable` como mostrado aqui.

## <a name="binding-at-runtime"></a>Encadernação no tempo de execução

Dentro C# e noutras línguas .NET, você pode usar um padrão de ligação [imperativo,](https://en.wikipedia.org/wiki/Imperative_programming) em oposição às ligações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em atributos. A ligação imperativa é útil quando os parâmetros de ligação precisam de ser calculados no tempo de execução em vez de tempo de conceção. Com este padrão, pode ligar-se a encadernações de entrada e saída suportadas no seu código de função.

Defina uma vinculação imperativa da seguinte forma:

- **Não** inclua um atributo na assinatura de função para as suas encadernações imperativas desejadas.
- Passe num parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Utilize o C# seguinte padrão para efetuar a encadernação de dados.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` é o atributo .NET que define a sua ligação, e `T` é um tipo de entrada ou saída que é suportado por esse tipo de ligação. `T` não pode ser do tipo de parâmetro `out` (como `out JObject`). Por exemplo, a ligação de saída da tabela Mobile Apps suporta seis tipos de [saída,](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)mas só pode utilizar [o ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [o IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) com uma ligação imperativa.

### <a name="single-attribute-example"></a>Exemplo de atributo único

O seguinte código de exemplo cria uma ligação de saída de [blob de armazenamento](functions-bindings-storage-blob-output.md) com caminho blob que é definido no tempo de execução, em seguida, escreve uma corda para a bolha.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) define a entrada ou ligação de saída da bolha de [armazenamento,](functions-bindings-storage-blob.md) e [textWriter](/dotnet/api/system.io.textwriter) é um tipo de ligação de saída suportado.

### <a name="multiple-attribute-example"></a>Exemplo de atributo múltiplo

O exemplo anterior obtém a definição da aplicação para a cadeia de ligação à conta de armazenamento principal da aplicação de função (que é `AzureWebJobsStorage`). Pode especificar uma definição de aplicação personalizada para usar na conta de Armazenamento, adicionando o [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando o conjunto de atributos para `BindAsync<T>()`. Utilize um parâmetro `Binder`, não `IBinder`.  Por exemplo:

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
> [Saiba mais sobre gatilhos e encadernações](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as melhores práticas para funções azure](functions-best-practices.md)
