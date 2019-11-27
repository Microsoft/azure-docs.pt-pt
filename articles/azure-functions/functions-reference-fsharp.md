---
title: Referência F# do Azure Functions Developer
description: Entenda como desenvolver Azure Functions usando F# o script.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: cf080b841e5fb3bbf3b36a2629a619f77fe52ddd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226754"
---
# <a name="azure-functions-f-developer-reference"></a>Referência F# do Azure Functions Developer

F#por Azure Functions é uma solução para executar facilmente pequenas partes de código ou "funções" na nuvem. Os dados fluem para F# sua função por meio de argumentos de função. Os nomes de argumentos são especificados em `function.json`e há nomes predefinidos para acessar itens como os tokens de agente de log de função e cancelamento. 

>[!IMPORTANT]
>F#o script (. fsx) só tem suporte da [versão 1. x](functions-versions.md#creating-1x-apps) do tempo de execução de Azure functions. Se você quiser usar F# o com o tempo de execução da versão 2. x, deverá usar um projeto F# de biblioteca de classes pré-compilado (. FS). Você cria, gerencia e publica um F# projeto de biblioteca de classes usando o Visual Studio como faria com um projeto de [ C# biblioteca de classes](functions-dotnet-class-library.md). Para obter mais informações sobre versões do functions, consulte [visão geral de versões do Azure Functions Runtime](functions-versions.md).

Este artigo pressupõe que você já leu a [referência do Azure Functions Developer](functions-reference.md).

## <a name="how-fsx-works"></a>Como o. fsx funciona
Um arquivo de `.fsx` é F# um script. Pode ser pensado como um F# projeto contido em um único arquivo. O arquivo contém o código para seu programa (nesse caso, sua função do Azure) e diretivas para gerenciar dependências.

Quando você usa um `.fsx` para uma função do Azure, os assemblies comumente necessários são incluídos automaticamente para você, permitindo que você se concentre na função em vez do código "clichê".

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas de F# um projeto de script é semelhante ao seguinte:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Há um arquivo [host. JSON](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função tem seu próprio arquivo de código (. fsx) e o arquivo de configuração de associação (Function. JSON).

As extensões de associação necessárias na [versão 2. x](functions-versions.md) do tempo de execução do Functions são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você deve [registrar extensões de associação](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

## <a name="binding-to-arguments"></a>Associação a argumentos
Cada associação dá suporte a algum conjunto de argumentos, conforme detalhado na [referência do desenvolvedor de Azure Functions gatilhos e associações](functions-triggers-bindings.md). Por exemplo, uma das associações de argumento que um gatilho de blob dá suporte é um POCO, que pode ser expresso F# usando um registro. Por exemplo:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Sua F# função do Azure usará um ou mais argumentos. Quando falamos sobre argumentos Azure Functions, nos referimos a argumentos de *entrada* e argumentos de *saída* . Um argumento de entrada é exatamente o que parece: entrada para a F# função do Azure. Um argumento de *saída* é dados mutáveis ou um argumento `byref<>` que serve como uma maneira de passar dados de volta para *fora* de sua função.

No exemplo acima, `blob` é um argumento de entrada e `output` é um argumento de saída. Observe que usamos `byref<>` para `output` (não há necessidade de adicionar a anotação `[<Out>]`). O uso de um tipo de `byref<>` permite que sua função altere o registro ou objeto ao qual o argumento se refere.

Quando um F# registro é usado como um tipo de entrada, a definição de registro deve ser marcada com `[<CLIMutable>]` para permitir que a estrutura de Azure Functions defina os campos adequadamente antes de passar o registro para sua função. Nos bastidores, `[<CLIMutable>]` gera setters para as propriedades do registro. Por exemplo:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Uma F# classe também pode ser usada para argumentos de entrada e saída. Para uma classe, as propriedades geralmente precisam de getters e setters. Por exemplo:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Registo
Para registrar a saída em seus [logs](../app-service/troubleshoot-diagnostic-logs.md) de F#streaming no, sua função deve usar um argumento do tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Para fins de consistência, recomendamos que esse argumento seja nomeado `log`. Por exemplo:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
O fluxo de trabalho `async` pode ser usado, mas o resultado precisa retornar um `Task`. Isso pode ser feito com `Async.StartAsTask`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token de cancelamento
Se sua função precisar lidar com o desligamento normal, você poderá dar a ele um [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argumento. Isso pode ser combinado com `async`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importando namespaces
Os namespaces podem ser abertos da maneira usual:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Os seguintes namespaces são abertos automaticamente:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referenciando assemblies externos
Da mesma forma, as referências de assembly de estrutura podem ser adicionadas com a diretiva `#r "AssemblyName"`.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Os assemblies a seguir são adicionados automaticamente pelo ambiente de hospedagem Azure Functions:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Além disso, os seguintes assemblies são especiais em maiúsculas e podem ser referenciados por SimpleName (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se você precisar fazer referência a um assembly particular, poderá carregar o arquivo do assembly em uma pasta `bin` em relação à sua função e fazer referência a ele usando o nome do arquivo (por exemplo,  `#r "MyAssembly.dll"`). Para obter informações sobre como carregar arquivos para sua pasta de funções, consulte a seção a seguir sobre gerenciamento de pacotes.

## <a name="editor-prelude"></a>Prelúdio do editor
Um editor que dá F# suporte a serviços de compilador não estará ciente dos namespaces e assemblies que Azure Functions inclui automaticamente. Como tal, pode ser útil incluir um prelúdio que ajude o editor a localizar os assemblies que você está usando e para abrir explicitamente os namespaces. Por exemplo:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Quando Azure Functions executa seu código, ele processa a fonte com `COMPILED` definido, portanto, o editor prelúdio será ignorado.

<a name="package"></a>

## <a name="package-management"></a>Gestão de pacotes
Para usar pacotes NuGet em uma F# função, adicione um arquivo de `project.json` à pasta da função no sistema de arquivos do aplicativo de funções. Aqui está um exemplo `project.json` arquivo que adiciona uma referência de pacote NuGet ao `Microsoft.ProjectOxford.Face` versão 1.1.0:

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

Somente o .NET Framework 4,6 tem suporte, portanto, verifique se o arquivo de `project.json` especifica `net46` como mostrado aqui.

Quando você carrega um arquivo de `project.json`, o tempo de execução Obtém os pacotes e adiciona referências automaticamente aos assemblies de pacote. Você não precisa adicionar diretivas de `#r "AssemblyName"`. Basta adicionar as instruções de `open` necessárias ao arquivo de `.fsx`.

Talvez você queira colocar automaticamente os assemblies de referência em seu editor prelúdio para melhorar a interação do editor com F# os serviços de compilação.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Como adicionar um arquivo de `project.json` à sua função do Azure
1. Comece verificando se seu aplicativo de funções está em execução, o que pode ser feito abrindo a função no portal do Azure. Isso também fornece acesso aos logs de streaming em que a saída da instalação do pacote será exibida.
2. Para carregar um arquivo de `project.json`, use um dos métodos descritos em [como atualizar os arquivos do aplicativo de funções](functions-reference.md#fileupdate). Se estiver usando a [implantação contínua para Azure Functions](functions-continuous-deployment.md), você poderá adicionar um arquivo de `project.json` à sua ramificação de preparo para experimentá-lo antes de adicioná-lo à sua ramificação de implantação.
3. Depois que o arquivo de `project.json` for adicionado, você verá uma saída semelhante ao exemplo a seguir no log de streaming da sua função:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variáveis de ambiente
Para obter uma variável de ambiente ou um valor de configuração de aplicativo, use `System.Environment.GetEnvironmentVariable`, por exemplo:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Reutilizando o código. fsx
Você pode usar código de outros arquivos de `.fsx` usando uma diretiva `#load`. Por exemplo:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Os caminhos fornecidos para a diretiva de `#load` são relativos ao local do arquivo de `.fsx`.

* `#load "logger.fsx"` carrega um arquivo localizado na pasta de funções.
* `#load "package\logger.fsx"` carrega um arquivo localizado na pasta `package` na pasta de funções.
* `#load "..\shared\mylogger.fsx"` carrega um arquivo localizado na pasta `shared` no mesmo nível que a pasta de funções, ou seja, diretamente em `wwwroot`.

A diretiva `#load` só funciona com arquivos deF# `.fsx` (script) e não com arquivos `.fs`.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os seguintes recursos:

* [F#Orienta](/dotnet/articles/fsharp/index)
* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Azure Functions gatilhos e associações](functions-triggers-bindings.md)
* [Teste de Azure Functions](functions-test-a-function.md)
* [Dimensionamento de Azure Functions](functions-scale.md)

