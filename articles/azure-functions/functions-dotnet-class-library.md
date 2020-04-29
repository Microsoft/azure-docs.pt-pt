---
title: Referência do desenvolvedor funções Azure C#
description: Entenda como desenvolver funções Azure usando C#.
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: cfa53fe2defca768196af595c1d088d41bc60f71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277066"
---
# <a name="azure-functions-c-developer-reference"></a>Referência do desenvolvedor funções Azure C#

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Este artigo é uma introdução ao desenvolvimento de Funções Azure utilizando C# em bibliotecas da classe .NET.

As Funções Azure suportam linguagens de programação de scripts C# e C#. Se procura orientação sobre a utilização de [C# no portal Azure,](functions-create-function-app-portal.md)consulte a referência do [desenvolvedor c# script (.csx).](functions-reference-csharp.md)

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

No Estúdio Visual, o modelo de projeto **Funções Azure** cria um projeto de biblioteca de classe C# que contém os seguintes ficheiros:

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

Numa biblioteca de classes, uma função `FunctionName` é um método estático com atributo a e gatilho, como mostra o seguinte exemplo:

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

O `FunctionName` atributo marca o método como ponto de entrada de função. O nome deve ser único dentro de um projeto, começar `_`com `-`uma letra e conter apenas letras, números, e , até 127 caracteres de comprimento. Os modelos de projeto `Run`criam frequentemente um método chamado, mas o nome do método pode ser qualquer nome de método C# válido.

O atributo do gatilho especifica o tipo de gatilho e liga os dados de entrada a um parâmetro de método. A função de exemplo é desencadeada por uma mensagem de fila, e a mensagem de fila é transmitida para o método no `myQueueItem` parâmetro.

## <a name="method-signature-parameters"></a>Parâmetros de assinatura de método

A assinatura do método pode conter parâmetros diferentes dos utilizados com o atributo do gatilho. Aqui estão alguns dos parâmetros adicionais que pode incluir:

* [Encadernações](functions-triggers-bindings.md) de entrada e saída marcadas como tal, decorando-as com atributos.  
* Um `ILogger` `TraceWriter` ou[(versão 1.x-only](functions-versions.md#creating-1x-apps)) para [abate](#logging).
* Um `CancellationToken` parâmetro para [uma paragem graciosa.](#cancellation-tokens)
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

O código seguinte obtém o nome da fila para monitorizar a partir de `insertionTime` uma definição de app, e obtém o tempo de criação de mensagens de fila no parâmetro.

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

O ficheiro *função.json* `configurationSource` gerado inclui uma propriedade que diz o tempo de execução para usar atributos .NET para ligações, em vez de *configuração function.json.* Segue-se um exemplo:

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

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Funções

A geração de *ficheiros function.json* é executada pelo pacote NuGet [Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

O mesmo pacote é utilizado para ambas as versões 1.x e 2.x do tempo de funcionamento das Funções. O quadro-alvo é o que diferencia um projeto 1.x de um projeto 2.x. Aqui estão as partes relevantes dos *ficheiros .csproj,* mostrando diferentes quadros-alvo e o mesmo `Sdk` pacote:

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

Entre `Sdk` as dependências do pacote estão gatilhos e encadernações. Um projeto de 1.x refere-se a gatilhos e encadernações de 1.x porque esses gatilhos e encadernações visam o Quadro .NET, enquanto 2.x desencadeia e encaderna o alvo .NET Core.

O `Sdk` pacote também depende de [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json), e indiretamente no [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Estas dependências certificam-se de que o seu projeto utiliza as versões dos pacotes que funcionam com a versão de tempo de execução functions que o projeto tem como alvo. Por exemplo, `Newtonsoft.Json` tem a versão 11 para .NET Framework 4.6.1, mas o tempo de funcionamento `Newtonsoft.Json` das funções que visa .NET Framework 4.6.1 é apenas compatível com 9.0.1. Assim, o seu código de função nesse projeto também tem de usar `Newtonsoft.Json` 9.0.1.

O código `Microsoft.NET.Sdk.Functions` fonte para está disponível nas funções gitHub repo [azure\-\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Versão runtime

O Visual Studio utiliza as [Ferramentas Core funções do Azure](functions-run-local.md#install-the-azure-functions-core-tools) para executar projetos de funções. As Ferramentas Core são uma interface de linha de comando para o tempo de funcionamento das funções.

Se instalar as Ferramentas Core utilizando o npm, isso não afeta a versão Core Tools utilizada pelo Visual Studio. Para as funções runtime versão 1.x, visual Studio armazena versões Core Tools em *%USERPROFILE%\AppData\Local\Azure.Functions.Functions.* Para funções 2.x, as Ferramentas Core estão incluídas na extensão de Ferramentas de **Funcionamento Azul e Web Jobs.** Tanto para 1.x como 2.x, pode supor que versão está a ser utilizada na saída da consola quando executa um projeto Functions:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Tipos suportados para encadernações

Cada encadernação tem os seus próprios tipos suportados; por exemplo, um atributo do gatilho de bolha pode ser aplicado a `CloudBlockBlob` um parâmetro de corda, um parâmetro POCO, um parâmetro ou qualquer um de vários outros tipos suportados. O [artigo de referência vinculativo para encadernações blob](functions-bindings-storage-blob-trigger.md#usage) lista todos os tipos de parâmetros suportados. Para mais informações, consulte [Gatilhos e encadernações](functions-triggers-bindings.md) e os [docs de referência vinculativos para cada tipo de encadernação](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Vinculação ao valor de retorno do método

Pode utilizar um valor de devolução de método para uma ligação de saída, aplicando o atributo ao valor de retorno do método. Por exemplo, consulte [Gatilhos e encadernações](./functions-bindings-return-value.md). 

Utilize o valor de devolução apenas se uma execução de função bem sucedida resultar sempre num valor de retorno para passar para a ligação de saída. Caso contrário, `ICollector` `IAsyncCollector`utilize ou, como se pode ver na secção seguinte.

## <a name="writing-multiple-output-values"></a>Escrever vários valores de saída

Para escrever vários valores para uma ligação de saída, ou se uma invocação [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) de função bem sucedida pode não resultar em nada para passar para a ligação de saída, use os ou tipos. Estes tipos são coleções escritas apenas que são escritas para a ligação de saída quando o método completa.

Este exemplo escreve várias mensagens `ICollector`de fila na mesma fila usando:

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

Para registar a saída nos seus registos de streaming em C#, inclua um argumento do tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Recomendamos que o `log`nomeie, como no seguinte exemplo:  

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

Evite `Console.Write` utilizar em Funções Azure. Para mais informações, consulte [os registos de Escrita em Funções C#](functions-monitoring.md#write-logs-in-c-functions) no artigo **Funções Monitor Azure.**

## <a name="async"></a>Async

Para fazer uma função [assíncrona,](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)use `async` `Task` a palavra-chave e devolva um objeto.

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

Não se pode `out` usar parâmetros em funções de asincronização. Para encadernações de saída, utilize o valor de devolução da [função](#binding-to-method-return-value) ou um [objeto de coleção.](#writing-multiple-output-values)

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

Para obter uma variável ambiental ou `System.Environment.GetEnvironmentVariable`um valor de definição de app, use, como mostra o seguinte exemplo de código:

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

As definições de aplicativos podem ser lidas a partir de variáveis ambientais tanto quando se desenvolve localmente como quando se está a correr em Azure. Ao desenvolver localmente, as `Values` definições de aplicativos provêm da coleção no ficheiro *local.settings.json.* Em ambos os ambientes, `GetEnvironmentVariable("<app setting name>")` local e Azure, recupera o valor da configuração de aplicações nomeada. Por exemplo, quando estiver a funcionar localmente, "My Site Name" seria devolvido `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`se o seu ficheiro *local.settings.json* contiver .

A propriedade [System.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) é uma API alternativa para obter valores `GetEnvironmentVariable` de definição de aplicações, mas recomendamos que utilize como mostrado aqui.

## <a name="binding-at-runtime"></a>Encadernação no tempo de execução

Em C# e outras línguas .NET, você pode usar um padrão de ligação [imperativo,](https://en.wikipedia.org/wiki/Imperative_programming) em oposição às ligações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em atributos. A ligação imperativa é útil quando os parâmetros de ligação precisam de ser calculados no tempo de execução em vez de tempo de conceção. Com este padrão, pode ligar-se a encadernações de entrada e saída suportadas no seu código de função.

Defina uma vinculação imperativa da seguinte forma:

- **Não** inclua um atributo na assinatura de função para as suas encadernações imperativas desejadas.
- Passe num parâmetro [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) de [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)entrada ou .
- Utilize o seguinte padrão C# para efetuar a encadernação de dados.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`é o atributo .NET que define `T` a sua ligação, e é um tipo de entrada ou saída que é suportado por esse tipo de ligação. `T`não pode `out` ser do tipo `out JObject`parâmetro (como). Por exemplo, a ligação de saída da tabela mobile Apps suporta seis tipos de [saída,](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)mas só pode utilizar [o\<ICollector T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou o [\<IAsyncCollector T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) com uma ligação imperativa.

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

O exemplo anterior obtém a definição da aplicação para a `AzureWebJobsStorage`cadeia de ligação à conta de armazenamento principal da aplicação de função (que é ). Pode especificar uma definição de aplicação personalizada para usar na conta de Armazenamento, adicionando o [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando o array de atributo para `BindAsync<T>()`. Use `Binder` um parâmetro, `IBinder`não.  Por exemplo:

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
