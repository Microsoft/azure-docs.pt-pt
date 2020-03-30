---
title: Referência do desenvolvedor funções Azure F#
description: Entenda como desenvolver funções Azure usando o script F#.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276767"
---
# <a name="azure-functions-f-developer-reference"></a>Referência do desenvolvedor das funções Azure F#

F# para Funções Azure é uma solução para executar facilmente pequenos pedaços de código, ou "funções", na nuvem. Os dados fluem para a sua função F# através de argumentos de função. Os nomes dos `function.json`argumentos são especificados em , e existem nomes predefinidos para aceder a coisas como o logger de funções e fichas de cancelamento. 

>[!IMPORTANT]
>O script F# (.fsx) é suportado apenas pela [versão 1.x](functions-versions.md#creating-1x-apps) do tempo de execução das Funções Azure. Se quiser utilizar F# com versões 2.x e versões posteriores do tempo de execução, deve utilizar um projeto de biblioteca de classe F# pré-compilado (.fs). Crias, geres e publicas um projeto de biblioteca de classe F# utilizando o Visual Studio como serias um projeto de [biblioteca de classe C#.](functions-dotnet-class-library.md) Para obter mais informações sobre as versões Functions, consulte as versões de execução do Funcionamento do [Azure Functions](functions-versions.md).

Este artigo assume que já leu a referência do desenvolvedor de [Funções Azure.](functions-reference.md)

## <a name="how-fsx-works"></a>Como funciona .fsx
Um `.fsx` ficheiro é um guião F# Pode ser considerado como um projeto F# que está contido num único ficheiro. O ficheiro contém tanto o código para o seu programa (neste caso, a sua Função Azure) como as diretivas para gerir dependências.

Quando utiliza `.fsx` uma função Azure, os conjuntos normalmente necessários são automaticamente incluídos para si, permitindo-lhe concentrar-se na função em vez do código "boilerplate".

## <a name="folder-structure"></a>Estrutura de pasta

A estrutura da pasta para um projeto de script F# parece ser a seguinte:

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

As extensões de encadernação exigidas na [versão 2.x e versões posteriores](functions-versions.md) do tempo de execução das Funções são definidas no `extensions.csproj` ficheiro, com os ficheiros da biblioteca reais na `bin` pasta. Ao desenvolver-se localmente, deve [registar extensões vinculativas](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal Azure, este registo é feito para si.

## <a name="binding-to-arguments"></a>Vinculativo aos argumentos
Cada encadernação suporta alguns conjuntos de argumentos, conforme detalhado nas [Funções Azure, dispara e encaderna](functions-triggers-bindings.md)a referência do desenvolvedor. Por exemplo, um dos argumentos que une um gatilho de bolha suporta um POCO, que pode ser expresso usando um disco F#. Por exemplo:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

A sua Função F# Azure terá um ou mais argumentos. Quando falamos de argumentos da Azure Functions, referimo-nos a argumentos de *entrada* e argumentos de *saída.* Um argumento de entrada é exatamente o que parece: entrada para a sua Função F# Azure. Um argumento *de saída* é `byref<>` dados mutáveis ou um argumento que serve como uma forma de passar os dados de volta para *fora* da sua função.

No exemplo acima, `blob` é um argumento `output` de entrada, e é um argumento de saída. Note que `byref<>` `output` usamos (não há necessidade `[<Out>]` de adicionar a anotação). A `byref<>` utilização de um tipo permite que a sua função altere a que registo ou objeto do argumento se refere.

Quando um registo F# é utilizado como um tipo de `[<CLIMutable>]` entrada, a definição de registo deve ser marcada para permitir que a estrutura das Funções Azure estabeleça os campos adequadamente antes de passar o registo para a sua função. Sob o `[<CLIMutable>]` capot, gera setters para as propriedades recorde. Por exemplo:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Uma classe F# também pode ser usada para argumentos dentro e fora. Para uma aula, as propriedades geralmente precisam de getters e setters. Por exemplo:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Registo
Para registar a saída nos seus [registos](../app-service/troubleshoot-diagnostic-logs.md) de streaming em F#, a sua função deve ter um argumento de tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Para a consistência, recomendamos `log`que este argumento seja nomeado . Por exemplo:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
O `async` fluxo de trabalho pode ser utilizado, `Task`mas o resultado tem de devolver a . Isto pode ser `Async.StartAsTask`feito com, por exemplo:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Ficha de cancelamento
Se a sua função precisar de lidar [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) com o encerramento graciosamente, pode argumentar. Isto pode ser `async`combinado com, por exemplo:

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
Do mesmo modo, podem ser acrescentadas referências à assembleia-quadro com a `#r "AssemblyName"` diretiva.

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

Além disso, os seguintes conjuntos são remetos especiais e `#r "AssemblyName"`podem ser referenciados por um nome simples (por exemplo):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se necessitar de fazer referência a um conjunto privado, pode enviar o ficheiro de montagem para uma `bin` pasta relativa à sua função e referenciar utilizando o nome do ficheiro (por exemplo.  `#r "MyAssembly.dll"`). Para obter informações sobre como enviar ficheiros para a sua pasta de funções, consulte a seguinte secção na gestão do pacote.

## <a name="editor-prelude"></a>Prelúdio editor
Um editor que apoie os Serviços de Compiladores F# não estará ciente dos espaços e conjuntos de nomes que o Azure Functions inclui automaticamente. Como tal, pode ser útil incluir um prelúdio que ajude o editor a encontrar as assembléias que está a usar e a abrir explicitamente espaços de nomes. Por exemplo:

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

Quando a Azure Functions executa o seu `COMPILED` código, processa a fonte com definido, pelo que o prelúdio do editor será ignorado.

<a name="package"></a>

## <a name="package-management"></a>Gestão de pacotes
Para utilizar os pacotes NuGet numa `project.json` função F#, adicione um ficheiro à pasta da função no sistema de ficheiros da aplicação de função. Aqui está `project.json` um ficheiro de exemplo que `Microsoft.ProjectOxford.Face` adiciona uma referência de pacote NuGet à versão 1.1.0:

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

Apenas o .NET Framework 4.6 é suportado, por isso certifique-se de que o seu `project.json` ficheiro especifica `net46` como mostrado aqui.

Ao fazer `project.json` o upload de um ficheiro, o tempo de execução recebe os pacotes e adiciona automaticamente referências aos conjuntos de pacotes. Não precisas de acrescentar `#r "AssemblyName"` diretivas. Basta adicionar as `open` declarações `.fsx` necessárias ao seu ficheiro.

Pode querer colocar automaticamente as assembleias de referência no prelúdio do seu editor, para melhorar a interação do seu editor com os Serviços De Compile F# Compile.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Como adicionar `project.json` um ficheiro à sua Função Azure
1. Comece por certificar-se de que a sua aplicação de funções está em funcionamento, o que pode fazer abrindo a sua função no portal Azure. Isto também dá acesso aos registos de streaming onde será exibida a saída de instalação do pacote.
2. Para fazer `project.json` o upload de um ficheiro, utilize um dos métodos descritos na [forma de atualizar ficheiros](functions-reference.md#fileupdate)de aplicações de funções . Se estiver a utilizar a [Implantação Contínua para funções Azure,](functions-continuous-deployment.md)pode adicionar um `project.json` ficheiro ao seu ramo de preparação para experimentar o mesmo antes de o adicionar ao seu ramo de implantação.
3. Após `project.json` a adição do ficheiro, verá a saída semelhante ao seguinte exemplo no registo de streaming da sua função:

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
Para obter uma variável ambiental ou `System.Environment.GetEnvironmentVariable`um valor de definição de app, use, por exemplo:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Reutilizar o código .fsx
Pode utilizar código `.fsx` de outros `#load` ficheiros utilizando uma diretiva. Por exemplo:

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

Os caminhos que `#load` a diretiva prevê em `.fsx` relação à localização do seu ficheiro.

* `#load "logger.fsx"`carrega um ficheiro localizado na pasta de funções.
* `#load "package\logger.fsx"`carrega um ficheiro `package` localizado na pasta na pasta de funções.
* `#load "..\shared\mylogger.fsx"`carrega um ficheiro `shared` localizado na pasta ao mesmo nível da pasta `wwwroot`de funções, ou seja, diretamente por baixo .

A `#load` diretiva só `.fsx` funciona com ficheiros (F# script) e não com `.fs` ficheiros.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os seguintes recursos:

* [Guia F#](/dotnet/articles/fsharp/index)
* [Boas Práticas para Funções Azure](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Funções Azure desencadeiam e encadernam](functions-triggers-bindings.md)
* [Teste de Funções Azure](functions-test-a-function.md)
* [Escalade funções azure](functions-scale.md)

