---
title: Referência do programador Azure Functions F#
description: Entenda como desenvolver funções Azure usando o script F#.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: f9b7b92fd21e12f1d86c5d5878e48c6ec6b0e748
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87088024"
---
# <a name="azure-functions-f-developer-reference"></a>Referência do programador Azure Functions F#

F# para Funções Azure é uma solução para executar facilmente pequenas peças de código, ou "funções", na nuvem. Os dados fluem para a sua função F# através de argumentos de função. Os nomes dos argumentos são especificados em `function.json` , e existem nomes predefinidos para aceder a coisas como o madeir de funções e fichas de cancelamento. 

>[!IMPORTANT]
>F# O script (.fsx) só é suportado pela [versão 1.x](functions-versions.md#creating-1x-apps) do tempo de funcionamento das Funções Azure. Se quiser utilizar F# com versões 2.x e posteriores do tempo de execução, deve utilizar um projeto de biblioteca de classe F# pré-compensado (.fs). Você cria, gere e publica um projeto de biblioteca de classes F# usando o Visual Studio como você faria um [projeto de biblioteca de classe C](functions-dotnet-class-library.md)# . Para obter mais informações sobre as versões Funções, consulte [as versões de tempo de execução do Azure Functions](functions-versions.md).

Este artigo pressupõe que já leu a referência do [programador Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Como funciona .fsx
Um `.fsx` ficheiro é um guião F. Pode ser considerado como um projeto F# que está contido num único ficheiro. O ficheiro contém tanto o código para o seu programa (neste caso, a sua Função Azure) como as diretivas para a gestão das dependências.

Quando utiliza uma `.fsx` função Azure, os conjuntos normalmente necessários são automaticamente incluídos para si, permitindo-lhe concentrar-se na função em vez de código "caldeira".

## <a name="folder-structure"></a>Estrutura de pasta

A estrutura da pasta para um projeto de script F# parece o seguinte:

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

Há umhost.jspartilhado [ no](functions-host-json.md) ficheiro que pode ser usado para configurar a aplicação de função. Cada função tem o seu próprio ficheiro de código (.fsx) e ficheiro de configuração de ligação (function.js).

As extensões de ligação necessárias na [versão 2.x e versões posteriores](functions-versions.md) do tempo de execução das Funções são definidas no `extensions.csproj` ficheiro, com os ficheiros reais da biblioteca na `bin` pasta. Ao desenvolver-se localmente, deve [registar extensões vinculativas](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal Azure, este registo é feito para si.

## <a name="binding-to-arguments"></a>Vinculativo aos argumentos
Cada encadernação suporta alguns conjuntos de argumentos, conforme detalhado no [Azure Functions triggers e bindings developer reference](functions-triggers-bindings.md). Por exemplo, um dos suportes de um gatilho blob é um POCO, que pode ser expresso usando um registo F#. Por exemplo:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

A sua Função F# Azure terá um ou mais argumentos. Quando falamos de argumentos do Azure Functions, referimo-nos a argumentos *de entrada* e argumentos *de saída.* Um argumento de entrada é exatamente o que parece: inserir na sua Função F# Azure. Um argumento *de saída* é dado mutável ou um argumento que serve como uma forma de passar `byref<>` os dados de volta para *fora* da sua função.

No exemplo acima, `blob` é um argumento de entrada, e é um argumento de `output` saída. Note que usamos `byref<>` para `output` (não há necessidade de adicionar a `[<Out>]` anotação). A utilização de um `byref<>` tipo permite que a sua função altere o registo ou objeto a que o argumento se refere.

Quando um registo F' é utilizado como tipo de entrada, a definição de registo deve ser marcada `[<CLIMutable>]` para permitir que a estrutura de Funções Azure desapropriar os campos adequadamente antes de passar o registo para a sua função. Sob o capot, `[<CLIMutable>]` gera setters para as propriedades dos registos. Por exemplo:

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
Para iniciar a sua saída nos [seus registos de streaming](../app-service/troubleshoot-diagnostic-logs.md) em F#, a sua função deve ter um argumento do tipo [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). Para obter consistência, recomendamos que este argumento seja `log` nomeado. Por exemplo:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
O `async` fluxo de trabalho pode ser utilizado, mas o resultado precisa de devolver um `Task` . Isto pode ser feito `Async.StartAsTask` com, por exemplo:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token de cancelamento
Se a sua função precisar de lidar com o encerramento graciosamente, pode [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argumentar. Isto pode ser combinado `async` com, por exemplo:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Espaços de identificação importados
Os espaços de nome podem ser abertos da forma habitual:

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

## <a name="referencing-external-assemblies"></a>Referenciação de Conjuntos Externos
Do mesmo modo, podem ser acrescentadas referências à montagem-quadro com a `#r "AssemblyName"` diretiva.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Os seguintes conjuntos são automaticamente adicionados pelo ambiente de hospedagem Azure Functions:

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

Além disso, os seguintes conjuntos são especiais e podem ser referenciados por nome simples (por `#r "AssemblyName"` exemplo):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se precisar de fazer referência a uma assembleia privada, pode enviar o ficheiro de montagem para uma `bin` pasta relativa à sua função e faz referência ao mesmo utilizando o nome do ficheiro (por exemplo.  `#r "MyAssembly.dll"`). Para obter informações sobre como fazer o upload de ficheiros para a sua pasta de funções, consulte a seguinte secção sobre a gestão do pacote.

## <a name="editor-prelude"></a>Prelúdio do Editor
Um editor que suporte os Serviços de Compiler F# não estará ciente dos espaços e conjuntos de nomes que o Azure Functions inclui automaticamente. Como tal, pode ser útil incluir um prelúdio que ajude o editor a encontrar as assembléias que você está usando, e para abrir explicitamente espaços de nome. Por exemplo:

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

Quando o Azure Functions executa o seu código, processa a fonte com definido, para que `COMPILED` o prelúdio do editor seja ignorado.

<a name="package"></a>

## <a name="package-management"></a>Gestão de pacotes
Para utilizar pacotes NuGet numa função F#, adicione um `project.json` ficheiro à pasta da função no sistema de ficheiros da aplicação de funções. Aqui está um ficheiro de exemplo `project.json` que adiciona uma referência de pacote NuGet à versão `Microsoft.ProjectOxford.Face` 1.1.0:

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

Apenas o Quadro .NET 4.6 é suportado, por isso certifique-se de que o seu `project.json` ficheiro especifica como mostrado `net46` aqui.

Quando faz o upload de um `project.json` ficheiro, o tempo de execução recebe as embalagens e adiciona automaticamente referências aos conjuntos de pacotes. Não precisas de acrescentar `#r "AssemblyName"` diretivas. Basta adicionar as `open` declarações necessárias ao seu `.fsx` ficheiro.

Pode desejar colocar automaticamente conjuntos de referências no prelúdio do seu editor, para melhorar a interação do seu editor com os Serviços de Compile F#.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Como adicionar um `project.json` ficheiro à sua Função Azure
1. Comece por se certificar de que a sua aplicação de função está a funcionar, o que pode fazer abrindo a sua função no portal Azure. Isto também dá acesso aos registos de streaming onde será exibida a saída de instalação do pacote.
2. Para fazer o upload de um `project.json` ficheiro, utilize um dos métodos descritos na [forma de atualizar ficheiros de aplicações de função](functions-reference.md#fileupdate). Se estiver a utilizar [a Implementação Contínua para Funções Azure,](functions-continuous-deployment.md)pode adicionar um `project.json` ficheiro ao seu ramo de preparação para o experimentar antes de o adicionar ao seu ramo de implantação.
3. Após a adição do `project.json` ficheiro, verá uma saída semelhante ao seguinte exemplo no registo de streaming da sua função:

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
Para obter uma variável ambiental ou um valor de definição de aplicação, `System.Environment.GetEnvironmentVariable` use, por exemplo:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Reutilização do código .fsx
Pode utilizar códigos de `.fsx` outros ficheiros utilizando uma `#load` diretiva. Por exemplo:

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

Os caminhos que fornecem a `#load` diretiva são relativos à localização do seu `.fsx` ficheiro.

* `#load "logger.fsx"` carrega um ficheiro localizado na pasta de funções.
* `#load "package\logger.fsx"` carrega um ficheiro localizado na `package` pasta na pasta de funções.
* `#load "..\shared\mylogger.fsx"` carrega um ficheiro localizado na `shared` pasta ao mesmo nível da pasta de função, ou seja, diretamente em `wwwroot` .

A `#load` diretiva só funciona com `.fsx` ficheiros (script F#) e não com `.fs` ficheiros.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, veja os seguintes recursos:

* [Guia do F#](/dotnet/articles/fsharp/index)
* [Boas Práticas para Funções Azure](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Funções Azure dispara e encaderna](functions-triggers-bindings.md)
* [Teste de funções Azure](functions-test-a-function.md)
* [Escala de funções Azure](functions-scale.md)
