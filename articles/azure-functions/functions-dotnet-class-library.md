---
title: Referência do programador Azure Functions C#
description: Entenda como desenvolver funções Azure usando C#.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: 23b0961c369c21f50d9a873678a1c910385e6a91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206206"
---
# <a name="azure-functions-c-developer-reference"></a>Referência do programador Azure Functions C#

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Este artigo é uma introdução ao desenvolvimento de Funções Azure utilizando C# em bibliotecas de classe .NET.

A Azure Functions suporta linguagens de programação de scripts C# e C#. Se estiver à procura de orientação sobre [a utilização de C# no portal Azure,](functions-create-function-app-portal.md)consulte a [referência do programador do script C# (.csx).](functions-reference-csharp.md)

Este artigo assume que já leu os seguintes artigos:

* [Guia de desenvolvedores de funções Azure](functions-reference.md)
* [Ferramentas Azure Functions Visual Studio 2019](functions-develop-vs.md)

## <a name="supported-versions"></a>Versões suportadas

As versões do tempo de execução das Funções funcionam com versões específicas de .NET. A tabela a seguir mostra o nível mais alto de .NET Core e .NET Framework e .NET Core que podem ser utilizados com uma versão específica de Funções no seu projeto. 

| Versão de tempo de execução de funções | Versão Max .NET |
| ---- | ---- |
| Funções 3.x | .NET Core 3.1 |
| Funções 2.x | .NET Core 2.2 |
| Funções 1.x | .Net Quadro 4.7 |

Para saber mais, consulte [as versões de tempo de execução do Azure Functions](functions-versions.md)

## <a name="functions-class-library-project"></a>Projeto de biblioteca de classes de funções

No Visual Studio, o modelo de projeto **Azure Functions** cria um projeto de biblioteca de classe C# que contém os seguintes ficheiros:

* [host.jsligado](functions-host-json.md) - armazena configurações de configuração que afetam todas as funções do projeto quando funciona localmente ou em Azure.
* [local.settings.js-](functions-run-local.md#local-settings-file) armazena as definições de aplicações e as cordas de ligação que são usadas quando são executando localmente. Este ficheiro contém segredos e não foi publicado na sua aplicação de função em Azure. Em vez disso, [adicione as definições de aplicação à sua aplicação de função](functions-develop-vs.md#function-app-settings).

Quando se constrói o projeto, uma estrutura de pasta que se parece com o seguinte exemplo é gerada no diretório de saída de construção:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Este diretório é o que é implementado na sua aplicação de função em Azure. As extensões de encadernação necessárias na [versão 2.x](functions-versions.md) do tempo de execução das Funções são [adicionadas ao projeto como pacotes NuGet](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> O processo de construção cria uma *function.jsno* ficheiro para cada função. Esta *function.jsem* ficheiro não deve ser editada diretamente. Não é possível alterar a configuração de encadernação ou desativar a função editando este ficheiro. Para aprender a desativar uma função, consulte [Como desativar as funções](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Métodos reconhecidos como funções

Numa biblioteca de classes, uma função é um método estático com um `FunctionName` atributo de gatilho e um gatilho, como mostra o seguinte exemplo:

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

O `FunctionName` atributo marca o método como ponto de entrada de função. O nome deve ser único dentro de um projeto, começar com uma letra e conter apenas letras, números `_` e `-` até 127 caracteres de comprimento. Os modelos de projeto muitas vezes criam um método chamado `Run` , mas o nome do método pode ser qualquer nome de método C# válido.

O atributo gatilho especifica o tipo de gatilho e liga os dados de entrada a um parâmetro do método. A função exemplo é desencadeada por uma mensagem de fila, e a mensagem de fila é passada para o método no `myQueueItem` parâmetro.

## <a name="method-signature-parameters"></a>Parâmetros de assinatura do método

A assinatura do método pode conter parâmetros diferentes dos utilizados com o atributo do gatilho. Aqui estão alguns dos parâmetros adicionais que pode incluir:

* [Entradas e encadernações de saída marcadas](functions-triggers-bindings.md) como tal, decorando-as com atributos.  
* Um `ILogger` `TraceWriter` parâmetro ou[(versão 1.x-only)](functions-versions.md#creating-1x-apps)para [o registo.](#logging)
* Um `CancellationToken` parâmetro para uma [paragem graciosa](#cancellation-tokens).
* [A ligação apresenta parâmetros](./functions-bindings-expressions-patterns.md) para obter metadados desencadeadores.

A ordem dos parâmetros na assinatura da função não importa. Por exemplo, pode colocar parâmetros de gatilho antes ou depois de outras encadernações, e pode colocar o parâmetro do madeireiros antes ou depois do gatilho ou parâmetros de ligação.

### <a name="output-binding-example"></a>Exemplo vinculativo de saída

O exemplo a seguir modifica o anterior adicionando uma encadernação de fila de saída. A função escreve a mensagem de fila que desencadeia a função para uma nova mensagem de fila numa fila diferente.

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

Os artigos de referência de encadernação[(filas de armazenamento,](functions-bindings-storage-queue.md)por exemplo) explicam quais os tipos de parâmetros que pode utilizar com atributos de detonação, entrada ou ligação de saída.

### <a name="binding-expressions-example"></a>Exemplo de expressões vinculativas

O código seguinte obtém o nome da fila para monitorizar a partir de uma configuração de aplicação, e obtém o tempo de criação de mensagem de fila no `insertionTime` parâmetro.

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

## <a name="autogenerated-functionjson"></a>function.jsautogerados em

O processo de construção cria uma *function.jsno* ficheiro numa pasta de função na pasta de construção. Como já foi notado anteriormente, este ficheiro não deve ser editado diretamente. Não é possível alterar a configuração de encadernação ou desativar a função editando este ficheiro. 

O objetivo deste ficheiro é fornecer informações ao controlador de escala para utilizar para [as decisões de escalonamento do plano de consumo.](functions-scale.md#how-the-consumption-and-premium-plans-work) Por esta razão, o ficheiro tem apenas informações de gatilho, não entradas ou encadernações de saída.

O *function.js* gerado no ficheiro inclui uma `configurationSource` propriedade que diz ao tempo de execução para usar atributos .NET para encadernações, em vez * defunction.jsna* configuração. Eis um exemplo:

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

A *function.jsna* geração de ficheiros é executada pelo pacote NuGet Microsoft NET [ \. \. Sdk \. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

O mesmo pacote é utilizado tanto para as versões 1.x como para 2.x do tempo de execução das Funções. O quadro-alvo é o que diferencia um projeto de 1.x de um projeto 2.x. Aqui estão as partes relevantes dos *ficheiros .csproj,* mostrando diferentes quadros-alvo e o mesmo `Sdk` pacote:

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

Entre as dependências dos `Sdk` pacotes estão gatilhos e encadernações. Um projeto de 1.x refere-se a 1.x gatilhos e encadernações porque esses gatilhos e encadernações visam o Quadro .NET, enquanto 2.x dispara e liga o alvo .NET Core.

O `Sdk` pacote também depende de [Newtonsoft.Js,](https://www.nuget.org/packages/Newtonsoft.Json)e indiretamente no [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Estas dependências asseguram que o seu projeto utiliza as versões dos pacotes que funcionam com a versão de tempo de execução de Funções que o projeto visa. Por exemplo, `Newtonsoft.Json` tem a versão 11 para .NET Framework 4.6.1, mas o tempo de funcionamento das Funções que visa .NET Framework 4.6.1 só é compatível com `Newtonsoft.Json` 9.0.1. Assim, o seu código de função nesse projeto também tem de usar `Newtonsoft.Json` o 9.0.1.

O código-fonte `Microsoft.NET.Sdk.Functions` para está disponível nas funções gitHub repo [azure \- vs build \- \- \- sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Versão runtime

O Visual Studio utiliza as [Ferramentas Centrais Azure Functions](functions-run-local.md#install-the-azure-functions-core-tools) para executar projetos de funções. As Ferramentas Centrais são uma interface de linha de comando para o tempo de execução das funções.

Se instalar as Ferramentas Core utilizando npm, isso não afeta a versão Core Tools utilizada pelo Visual Studio. Para a versão runtime 1.x das Funções, o Visual Studio armazena as versões Core Tools em *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* e utiliza a versão mais recente armazenada lá. Para as funções 2.x, as Ferramentas Core estão incluídas na extensão **Azure Functions e Web Jobs Tools.** Para 1.x e 2.x, pode ver que versão está a ser utilizada na saída da consola quando executar um projeto De funções:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

Pode compilar a sua aplicação de função como [binários ReadyToRun](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images). ReadyToRun é uma forma de compilação antecipada que pode melhorar o desempenho do arranque para ajudar a reduzir o impacto do [arranque a frio](functions-scale.md#cold-start) ao executar um plano de [consumo.](functions-scale.md#consumption-plan)

O ReadyToRun está disponível em .NET 3.0 e requer [a versão 3.0 do tempo de funcionamento das Funções Azure](functions-versions.md).

Para compilar o seu projeto como ReadyToRun, atualize o seu ficheiro de projeto adicionando os `<PublishReadyToRun>` elementos e `<RuntimeIdentifier>` elementos. Segue-se a configuração para publicação numa aplicação de função Windows 32 bit.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> O ReadyToRun não suporta atualmente a compilação cruzada. Tem de construir a sua aplicação na mesma plataforma que o alvo de implementação. Além disso, preste atenção à "mordida" que está configurada na sua aplicação de função. Por exemplo, se a sua aplicação de função no Azure for o Windows 64-bit, tem de compilar a sua aplicação no Windows `win-x64` com o identificador de [tempo de execução](/dotnet/core/rid-catalog).

Também pode construir a sua aplicação com ReadyToRun a partir da linha de comando. Para mais informações, consulte a `-p:PublishReadyToRun=true` opção em [`dotnet publish`](/dotnet/core/tools/dotnet-publish) .

## <a name="supported-types-for-bindings"></a>Tipos suportados para encadernações

Cada encadernação tem os seus próprios tipos suportados; por exemplo, um atributo de gatilho blob pode ser aplicado a um parâmetro de corda, um parâmetro POCO, um `CloudBlockBlob` parâmetro, ou qualquer um de vários outros tipos suportados. O [artigo de referência vinculativo para encadernações blob](functions-bindings-storage-blob-trigger.md#usage) lista todos os tipos de parâmetros suportados. Para obter mais informações, consulte [Gatilhos e encadernações](functions-triggers-bindings.md) e os [documentos de referência vinculativos para cada tipo de encadernação](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Ligação ao valor de retorno do método

Pode utilizar um valor de retorno de método para uma ligação de saída, aplicando o atributo ao valor de retorno do método. Por exemplo, consulte [Gatilhos e encadernações](./functions-bindings-return-value.md). 

Utilize o valor de devolução apenas se uma execução de função bem sucedida resultar sempre num valor de retorno para passar para a ligação de saída. Caso contrário, utilize `ICollector` `IAsyncCollector` ou, como indicado na secção seguinte.

## <a name="writing-multiple-output-values"></a>Escrever vários valores de saída

Para escrever vários valores para uma ligação de saída, ou se uma invocação de função bem sucedida pode não resultar em nada para passar para a ligação de saída, use os [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) ou tipos. Estes tipos são coleções apenas de escrita que são escritas para a ligação de saída quando o método termina.

Este exemplo escreve várias mensagens de fila na mesma fila `ICollector` utilizando:

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

Para iniciar a sua saída nos seus registos de streaming em C#, inclua um argumento do tipo [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). Recomendamos que lhe dê um `log` nome, como no seguinte exemplo:  

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

Evite utilizar `Console.Write` em Funções Azure. Para obter mais informações, consulte [as funções de Gravação em C#](functions-monitoring.md#write-logs-in-c-functions) no artigo **'Funções Azure'** do Monitor.

## <a name="async"></a>Async

Para fazer uma função [assíncrona,](/dotnet/csharp/programming-guide/concepts/async/)use a `async` palavra-chave e devolva um `Task` objeto.

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

Não pode usar `out` parâmetros em funções de async. Para encadernações de saída, utilize o valor de retorno da [função](#binding-to-method-return-value) ou um [objeto de coletor.](#writing-multiple-output-values)

## <a name="cancellation-tokens"></a>Fichas de cancelamento

Uma função pode aceitar um parâmetro [CancelamentoToken,](/dotnet/api/system.threading.cancellationtoken) que permite ao sistema operativo notificar o seu código quando a função está prestes a ser terminada. Pode utilizar esta notificação para se certificar de que a função não termina inesperadamente de forma a deixar os dados num estado inconsistente.

O exemplo a seguir mostra como verificar a interrupção da função iminente.

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

Para obter uma variável ambiental ou um valor de definição de aplicação, `System.Environment.GetEnvironmentVariable` utilize, como mostra o seguinte exemplo de código:

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

As definições de aplicativos podem ser lidas a partir de variáveis ambientais, tanto quando se desenvolve localmente como quando está a correr em Azure. Ao desenvolver-se localmente, as configurações das aplicações provêm da `Values` coleção nolocal.settings.js* em* arquivo. Em ambos os ambientes, local e Azure, `GetEnvironmentVariable("<app setting name>")` recupera o valor da configuração da aplicação nomeada. Por exemplo, quando estiver a correr localmente, o "Meu Nome do Site" seria devolvido se o seu *local.settings.jsno* ficheiro contiver `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` .

A [ propriedadeSystem.Configuration.ConfigurationManager.AppSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) é uma API alternativa para obter valores de definição de aplicativos, mas recomendamos que use `GetEnvironmentVariable` como mostrado aqui.

## <a name="binding-at-runtime"></a>Encadernação no tempo de execução

Em C# e em outras línguas .NET, pode utilizar um padrão de ligação [imperativo,](https://en.wikipedia.org/wiki/Imperative_programming) em oposição às encadernações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em atributos. A ligação imperativa é útil quando os parâmetros de ligação precisam de ser calculados em tempo de execução em vez de tempo de conceção. Com este padrão, pode ligar-se a entradas suportadas e ligações de saída no seu código de função.

Defina uma ligação imperativa da seguinte forma:

- **Não** inclua um atributo na assinatura de função para as suas ligações imperativas desejadas.
- Passe num parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- Utilize o seguinte padrão C# para executar a ligação de dados.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` é o atributo .NET que define a sua ligação, e `T` é um tipo de entrada ou saída que é suportado por esse tipo de ligação. `T` não pode ser um `out` tipo de parâmetro `out JObject` (como). Por exemplo, a ligação de saída da tabela mobile Apps suporta [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas só pode utilizar [iCollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) com obrigação de ligação.

### <a name="single-attribute-example"></a>Exemplo de atributo único

O seguinte código de exemplo cria uma [ligação de saída de bolha](functions-bindings-storage-blob-output.md) de armazenamento com o caminho blob definido no tempo de execução, em seguida, escreve uma corda para a bolha.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) define a entrada [blob de armazenamento](functions-bindings-storage-blob.md) ou a ligação de saída, e [o TextWriter](/dotnet/api/system.io.textwriter) é um tipo de ligação de saída suportado.

### <a name="multiple-attribute-example"></a>Exemplo de atributo múltiplo

O exemplo anterior obtém a definição da aplicação para a principal cadeia de ligação da conta de armazenamento da aplicação de função (que `AzureWebJobsStorage` é). Pode especificar uma definição de aplicação personalizada para utilizar para a conta de Armazenamento adicionando o [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando o conjunto de atributos em `BindAsync<T>()` . Use um `Binder` parâmetro, `IBinder` não.  Por exemplo:

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
> [Saiba mais sobre as melhores práticas para funções Azure](functions-best-practices.md)
