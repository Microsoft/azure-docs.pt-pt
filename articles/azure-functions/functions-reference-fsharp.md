---
title: 'Referência do Azure funções F # para programadores | Documentos da Microsoft'
description: 'Aprenda a desenvolver as funções do Azure com o F #.'
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
keywords: 'Azure funções, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, F #'
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: azure-functions
ms.devlang: fsharp
ms.topic: reference
ms.date: 09/09/2016
ms.author: syclebsc
ms.openlocfilehash: 5593f76511f43106d6743a158b051e118ef2a4a6
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125260"
---
# <a name="azure-functions-f-developer-reference"></a>Referência de F # para programadores do funções do Azure

F # para as funções do Azure é uma solução para uma fácil execução pequenas partes de código, ou "funções", na cloud. Os dados fluem para sua função F # por meio de argumentos de função. Argumento nomes estão especificados na `function.json`, e existem nomes predefinidos para o acesso a coisas como os tokens de agente de log e cancelamento de função.

Este artigo pressupõe que já leu a [referência para programadores do funções do Azure](functions-reference.md).

## <a name="how-fsx-works"></a>Como funciona a .fsx
Um `.fsx` arquivo é um script do F #. Ele pode ser considerado como um projeto do F # que está contido num único arquivo. O ficheiro contém tanto o código para o seu programa (no caso, sua função do Azure) e diretivas para gerir as dependências.

Quando utiliza um `.fsx` para uma função do Azure, exigidas normalmente assemblies são incluídos automaticamente para si, permitindo que se concentre no código de função, em vez de "automático".

## <a name="folder-structure"></a>estrutura de pastas

A estrutura de pastas para um projeto de script do F # é semelhante ao seguinte:

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

Existe um ficheiro partilhado [host. JSON] (funções-host-json.md) que pode ser utilizado para configurar a aplicação de funções. Cada função tem seu próprio arquivo de código (.fsx) e o ficheiro de configuração de vinculação (Function).

As extensões de vinculação necessárias [versão 2.x](functions-versions.md) runtime das funções definidas no `extensions.csproj` arquivo, com os ficheiros de biblioteca real no `bin` pasta. Ao desenvolver localmente, deve [registar as extensões de vinculação](functions-triggers-bindings.md#local-development-azure-functions-core-tools). Ao desenvolver funções no portal do Azure, este registo é feito para.

## <a name="binding-to-arguments"></a>Vinculando a argumentos
Cada ligação suporta um conjunto de argumentos, conforme detalhado no [referência de Programador de acionadores e enlaces do funções do Azure](functions-triggers-bindings.md). Por exemplo, uma das ligações de argumento que oferece suporte a um acionador de blob é um POCO, que pode ser expressos por meio de um registo de F #. Por exemplo:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Sua função do Azure do F # levará um ou mais argumentos. Quando falamos sobre argumentos de funções do Azure, nos Referimos *entrada* argumentos e *saída* argumentos. Um argumento de entrada é exatamente o que parece: de entrada para a sua função de Azure do F #. Uma *saída* argumento é mutáveis dados ou uma `byref<>` argumento que funciona como uma forma para passar dados de volta *horizontalmente* da sua função.

No exemplo acima, `blob` é um argumento de entrada, e `output` é um argumento de saída. Observe que utilizamos `byref<>` para `output` (não é necessário para adicionar o `[<Out>]` anotação). Usando um `byref<>` tipo permite que a sua função alterar o registo ou o objeto o argumento refere-se a.

Quando um registo de F # é usado como um tipo de entrada, a definição de registo deve ser marcada com `[<CLIMutable>]` para permitir que a estrutura de funções do Azure definir os campos corretamente antes de transmitir o registo para a sua função. Nos bastidores, `[<CLIMutable>]` gera setters para as propriedades de registo. Por exemplo:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Uma classe do F # também pode ser usada para ambos os argumentos. Para uma classe, propriedades, normalmente, irão precisar de getters e setters. Por exemplo:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Registo
Para iniciar sessão a saída para o seu [registos de transmissão em fluxo](../app-service/web-sites-enable-diagnostic-log.md) em F #, sua função deve demorar um argumento de tipo `TraceWriter`. Para obter consistência, recomendamos que este argumento é denominado `log`. Por exemplo:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
O `async` pode ser utilizado o fluxo de trabalho, mas o resultado tem de devolver um `Task`. Isso pode ser feito com `Async.StartAsTask`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token de cancelamento
Se a sua função precisa lidar graciosamente com encerramento, pode dar um [ `CancellationToken` ](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argumento. Pode ser combinado com `async`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importação de espaços de nomes
Espaços de nomes podem ser abertos como habitualmente:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Os espaços de nomes seguintes são abertos automaticamente:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referência a Assemblies externos
Da mesma forma, as referências de assemblagem do framework podem ser adicionadas com o `#r "AssemblyName"` diretiva.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

As assemblagens seguintes são adicionadas automaticamente por funções do Azure, ambiente de alojamento:

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

Além disso, os assemblies a seguir são especiais Case e pode ser referenciada por simplename (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se precisar de fazer referência a um assembly privado, pode carregar o ficheiro de assemblagem para um `bin` pasta referente à sua função e referência-lo utilizando o ficheiro de nome (por exemplo,  `#r "MyAssembly.dll"`). Para obter informações sobre como carregar ficheiros para a pasta de função, consulte a secção seguinte na gestão de pacotes.

## <a name="editor-prelude"></a>Prelúdio do Editor
Um editor que ofereça suporte a serviços de compilador F # não será conhecimento dos namespaces e assemblies que inclui automaticamente as funções do Azure. Como tal, pode ser útil para incluir um prelúdio que ajuda o editor de encontrar os assemblies que está a utilizar e espaços de nomes de abrir explicitamente. Por exemplo:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Quando as funções do Azure é executado o seu código, processa a origem com `COMPILED` definido, para que o prelúdio de editor será ignorado.

<a name="package"></a>

## <a name="package-management"></a>Gestão de pacotes
Para usar pacotes NuGet numa função F #, adicione um `project.json` ficheiro para de pasta a função no sistema de ficheiros da aplicação de função. Eis um exemplo `project.json` arquivo que adiciona uma referência de pacote NuGet para `Microsoft.ProjectOxford.Face` versão 1.1.0:

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

Apenas o .NET Framework 4.6 é suportada, por isso, certifique-se de que sua `project.json` Especifica o ficheiro `net46` conforme mostrado aqui.

Quando carrega um `project.json` obtém os pacotes de ficheiros, o tempo de execução e adiciona automaticamente referências aos assemblies do pacote. Não precisa de adicionar `#r "AssemblyName"` diretivas. Basta adicionar necessários `open` instruções para sua `.fsx` ficheiro.

Pode ser útil colocar automaticamente referencia assemblies no seu prelúdio de editor, para melhorar a interação de seu editor com os serviços de compilação do F #.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Como adicionar um `project.json` ficheiro para a sua função do Azure
1. Comece por certificar-se de que a aplicação de função está em execução, que pode ser feito ao abrir a sua função no portal do Azure. Isso também permite acesso para os registos de transmissão em fluxo em que será apresentada saída de instalação do pacote.
2. Para carregar um `project.json` de ficheiros, utilize um dos métodos descritos [como atualizar os ficheiros de aplicação de função](functions-reference.md#fileupdate). Se estiver a utilizar [implementação contínua para funções do Azure](functions-continuous-deployment.md), pode adicionar um `project.json` ficheiro para o seu ramo de teste para testá-la antes de adicionar seu ramo de implementação.
3. Depois do `project.json` ficheiro é adicionado, irá ver o resultado semelhante ao seguinte exemplo na sua função da transmissão em fluxo o registo:

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
Para obter uma variável de ambiente ou uma aplicação de definir o valor, utilize `System.Environment.GetEnvironmentVariable`, por exemplo:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Reutilizar os códigos de .fsx
Pode usar código dos outros `.fsx` ficheiros ao utilizar um `#load` diretiva. Por exemplo:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Caminhos fornece para o `#load` diretiva são relativo à localização do seu `.fsx` ficheiro.

* `#load "logger.fsx"` carrega um arquivo localizado na pasta de função.
* `#load "package\logger.fsx"` carrega um arquivo localizado no `package` pasta na pasta de função.
* `#load "..\shared\mylogger.fsx"` carrega um arquivo localizado no `shared` pasta de mesmo nível, como a pasta de função, ou seja, diretamente em `wwwroot`.

O `#load` diretiva só funciona com `.fsx` arquivos (skriptu F #) e não com `.fs` ficheiros.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte os seguintes recursos:

* [Guia de F #](/dotnet/articles/fsharp/index)
* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Acionadores de funções do Azure e enlaces](functions-triggers-bindings.md)
* [Funções do Azure de teste](functions-test-a-function.md)
* [Dimensionamento das funções do Azure](functions-scale.md)

