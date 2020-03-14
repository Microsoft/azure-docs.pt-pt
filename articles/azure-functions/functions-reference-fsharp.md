---
title: Referência do F# desenvolvedor de Funções Azure
description: Entenda como desenvolver funções F# Azure usando script.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276767"
---
# <a name="azure-functions-f-developer-reference"></a>Referência do F# desenvolvedor de funções Azure

F#para funções Azure é uma solução para executar facilmente pequenos pedaços de código, ou "funções", na nuvem. Os dados F# fluem para a sua função através de argumentos de função. Os nomes de argumentos são especificados em `function.json`, e existem nomes predefinidos para aceder a coisas como o logger de funções e fichas de cancelamento. 

>[!IMPORTANT]
>F#script (.fsx) é suportado apenas pela [versão 1.x](functions-versions.md#creating-1x-apps) do tempo de execução das Funções Azure. Se quiser utilizar F# com versões 2.x e versões posteriores do F# tempo de execução, deve utilizar um projeto de biblioteca de classes pré-compilado (.fs). Crias, geres e F# publicas um projeto de biblioteca de classes usando o Visual Studio como serias um [ C# projeto](functions-dotnet-class-library.md)de biblioteca de classes. Para obter mais informações sobre as versões Functions, consulte as versões de execução do Funcionamento do [Azure Functions](functions-versions.md).

Este artigo assume que já leu a referência do desenvolvedor de [Funções Azure.](functions-reference.md)

## <a name="how-fsx-works"></a>Como funciona .fsx
Um ficheiro `.fsx` F# é um guião. Pode ser considerado como F# um projeto que está contido num único ficheiro. O ficheiro contém tanto o código para o seu programa (neste caso, a sua Função Azure) como as diretivas para gerir dependências.

Quando utiliza uma `.fsx` para uma Função Azure, os conjuntos normalmente necessários são automaticamente incluídos para si, permitindo-lhe concentrar-se na função em vez do código "boilerplate".

## <a name="folder-structure"></a>Estrutura de pasta

A estrutura da F# pasta para um projeto de script parece ser a seguinte:

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

Há um ficheiro [host.json](functions-host-json.md) partilhado que pode ser usado para configurar a aplicação de função. Cada função tem o seu próprio ficheiro de código (.fsx) e ficheiro de configuração de ligação (função.json).

As extensões de encadernação exigidas na [versão 2.x e versões posteriores](functions-versions.md) do tempo de execução das Funções são definidas no ficheiro `extensions.csproj`, com os ficheiros da biblioteca reais na pasta `bin`. Ao desenvolver-se localmente, deve [registar extensões vinculativas](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal Azure, este registo é feito para si.

## <a name="binding-to-arguments"></a>Vinculativo aos argumentos
Cada encadernação suporta alguns conjuntos de argumentos, conforme detalhado nas [Funções Azure, dispara e encaderna](functions-triggers-bindings.md)a referência do desenvolvedor. Por exemplo, um dos argumentos que une um gatilho de bolha é F# um POCO, que pode ser expresso usando um registo. Por exemplo:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

A F# sua Função Azure terá um ou mais argumentos. Quando falamos de argumentos da Azure Functions, referimo-nos a argumentos de *entrada* e argumentos de *saída.* Um argumento de entrada é exatamente o F# que parece: entrada na sua Função Azure. Um argumento *de saída* é um dado mutável ou um argumento `byref<>` que serve como uma forma de passar os dados de volta para *fora* da sua função.

No exemplo acima, `blob` é um argumento de entrada, e `output` é um argumento de saída. Note que usámos `byref<>` para `output` (não há necessidade de adicionar o `[<Out>]` anotação). A utilização de um tipo `byref<>` permite que a sua função altere a que registo ou objeto do argumento se refere.

Quando F# um registo é utilizado como um tipo de entrada, a definição de registo deve ser marcada com `[<CLIMutable>]` de modo a permitir que a estrutura das Funções Azure estabeleça os campos adequadamente antes de passar o registo para a sua função. Sob o capot, `[<CLIMutable>]` gera setters para as propriedades recorde. Por exemplo:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Uma F# aula também pode ser usada para argumentos dentro e fora. Para uma aula, as propriedades geralmente precisam de getters e setters. Por exemplo:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Registo
Para iniciar sessão nos F#seus [registos de streaming,](../app-service/troubleshoot-diagnostic-logs.md) a sua função deve ter um argumento de tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Para obter coerência, recomendamos que este argumento seja denominado `log`. Por exemplo:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
O fluxo de trabalho `async` pode ser utilizado, mas o resultado tem de devolver uma `Task`. Isto pode ser feito com `Async.StartAsTask`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Ficha de cancelamento
Se a sua função precisar de lidar com o encerramento graciosamente, pode dar-lhe um [argumento`CancellationToken`.](/dotnet/api/system.threading.cancellationtoken) Isto pode ser combinado com `async`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Espaços de nomes importando
Espaços de nome podem ser abertos da forma habitual:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Os seguintes espaços de nome são automaticamente abertos:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referenciação de Assembleias Externas
Do mesmo modo, podem ser acrescentadas referências à assembleia-quadro com a diretiva `#r "AssemblyName"`.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Os seguintes conjuntos são automaticamente adicionados pelo ambiente de hospedagem das Funções Azure:

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

Além disso, os seguintes conjuntos são remetos especiais e podem ser referenciados por um nome simples (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se necessitar de fazer referência a um conjunto privado, pode enviar o ficheiro de montagem para uma pasta `bin` relativa à sua função e referenciar utilizando o nome do ficheiro (por exemplo.  `#r "MyAssembly.dll"`). Para obter informações sobre como enviar ficheiros para a sua pasta de funções, consulte a seguinte secção na gestão do pacote.

## <a name="editor-prelude"></a>Prelúdio editor
Um editor que F# apoie os Serviços compiladores não estará ciente dos espaços e conjuntos de nomes que o Azure Functions inclui automaticamente. Como tal, pode ser útil incluir um prelúdio que ajude o editor a encontrar as assembléias que está a usar e a abrir explicitamente espaços de nomes. Por exemplo:

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

Quando a Azure Functions executa o seu código, processa a fonte com `COMPILED` definida, pelo que o prelúdio do editor será ignorado.

<a name="package"></a>

## <a name="package-management"></a>Gestão de pacotes
Para utilizar os pacotes F# NuGet numa função, adicione um ficheiro `project.json` à pasta da função no sistema de ficheiros da aplicação de função. Aqui está um exemplo `project.json` ficheiro que adiciona uma referência de pacote NuGet à versão 1.1.0 `Microsoft.ProjectOxford.Face`:

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

Apenas o .NET Framework 4.6 é suportado, por isso certifique-se de que o seu ficheiro `project.json` especifica `net46` conforme mostrado aqui.

Ao fazer o upload de um ficheiro `project.json`, o tempo de execução recebe os pacotes e adiciona automaticamente referências aos conjuntos de pacotes. Não é preciso acrescentar `#r "AssemblyName"` diretivas. Basta adicionar as declarações `open` necessárias ao seu ficheiro `.fsx`.

Pode querer colocar automaticamente as assembleias de referência no prelúdio do F# seu editor, para melhorar a interação do seu editor com os Serviços compilados.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Como adicionar um ficheiro `project.json` à sua Função Azure
1. Comece por certificar-se de que a sua aplicação de funções está em funcionamento, o que pode fazer abrindo a sua função no portal Azure. Isto também dá acesso aos registos de streaming onde será exibida a saída de instalação do pacote.
2. Para fazer o upload de um ficheiro `project.json`, utilize um dos métodos descritos na [forma de atualizar ficheiros](functions-reference.md#fileupdate)de aplicações de funções . Se estiver a utilizar a [Implantação Contínua para funções Azure,](functions-continuous-deployment.md)pode adicionar um ficheiro `project.json` ao seu ramo de preparação para experimentar o mesmo antes de o adicionar ao seu ramo de implantação.
3. Após a adição do ficheiro `project.json`, verá a saída semelhante ao seguinte exemplo no registo de streaming da sua função:

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
Para obter uma variável ambiental ou um valor de definição de app, use `System.Environment.GetEnvironmentVariable`, por exemplo:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Reutilizar o código .fsx
Pode utilizar código saem de outros ficheiros `.fsx` utilizando uma diretiva `#load`. Por exemplo:

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

Os caminhos que fornecem à diretiva `#load` são relativos à localização do seu ficheiro `.fsx`.

* `#load "logger.fsx"` carrega um ficheiro localizado na pasta de funções.
* `#load "package\logger.fsx"` carrega um ficheiro localizado na pasta `package` na pasta de funções.
* `#load "..\shared\mylogger.fsx"` carrega um ficheiro localizado na pasta `shared` ao mesmo nível da pasta de funções, ou seja, diretamente sob `wwwroot`.

A diretiva `#load` sóF# funciona com ficheiros `.fsx` (script) e não com ficheiros `.fs`.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os seguintes recursos:

* [F#Guia](/dotnet/articles/fsharp/index)
* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Funções Azure desencadeiam e encadernam](functions-triggers-bindings.md)
* [Teste de Funções Azure](functions-test-a-function.md)
* [Escalade funções azure](functions-scale.md)

