---
title: Referência do desenvolvedor do PowerShell para as funções do Azure
description: Aprenda a desenvolver as funções com o PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: 46b1e5c99dd86fed6f87ac3b8f0ff6555187899b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833510"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guia de programadores do PowerShell de funções do Azure

Este artigo fornece detalhes sobre como escrever as funções do Azure com o PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Uma função do PowerShell Azure (função) é representada como um script do PowerShell que é executado quando acionado. Cada script de função tem um relacionados `function.json` ficheiro que define a forma como a função se comporta, por exemplo, como é acionado e seus parâmetros de entrada e saídos. Para obter mais informações, consulte a [Acionadores e o artigo de enlace](functions-triggers-bindings.md). 

Como outros tipos de funções, funções de script do PowerShell tirar nos parâmetros que correspondem aos nomes de todos os enlaces de entrada, definidos no `function.json` ficheiro. A `TriggerMetadata` parâmetro é transmitido também que contém informações adicionais sobre o acionador que iniciou a função.

Este artigo pressupõe que já leu a [referência para programadores do funções do Azure](functions-reference.md). Deverá ter concluído também a [início rápido das funções para o PowerShell](functions-create-first-function-powershell.md) para criar a sua primeira função do PowerShell.

## <a name="folder-structure"></a>estrutura de pastas

A estrutura de pasta necessária para um projeto do PowerShell é semelhante ao seguinte. Este padrão pode ser alterado. Para obter mais informações, consulte a [scriptFile](#configure-function-scriptfile) secção abaixo.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

Na raiz do projeto, há um partilhada [ `host.json` ](functions-host-json.md) ficheiro que pode ser utilizado para configurar a aplicação de função. Cada função tem uma pasta com o seu próprio arquivo de código (. ps1) e o ficheiro de configuração de vinculação (`function.json`). O nome do diretório pai do arquivo Function sempre é o nome da sua função.

Determinados associações exigem a presença de um `extensions.csproj` ficheiro. Enlace de extensões, necessárias na [versão 2.x](functions-versions.md) de runtime das funções, são definidos no `extensions.csproj` arquivo, com os ficheiros de biblioteca real no `bin` pasta. Ao desenvolver localmente, deve [registar as extensões de vinculação](functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). Ao desenvolver funções no portal do Azure, este registo é feito para.

Em aplicações de funções do PowerShell, pode, opcionalmente, ter uma `profile.ps1` que é executada quando uma aplicação de funções começa a ser executada (caso contrário, sabe como uma  *[arranque a frio](#cold-start)*. Para obter mais informações, consulte [perfil do PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definir um script do PowerShell como uma função

Por predefinição, o runtime das funções procura em sua função na `run.ps1`, onde `run.ps1` compartilha o mesmo diretório principal de seu correspondente `function.json`.

O script é passado um número de argumentos em execução. Para lidar com esses parâmetros, adicione um `param` bloco na parte superior do seu script, como no exemplo seguinte:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parâmetro de TriggerMetadata

O `TriggerMetadata` parâmetro é utilizado para fornecer informações adicionais sobre o acionador. Os metadados adicionais variam de enlace para o enlace, mas todos eles contenham um `sys` propriedade que contém os seguintes dados:

```powershell
$TriggerMetadata.sys
```

| Propriedade   | Descrição                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Quando, em UTC, a função foi acionada        | DateTime |
| NomeMétodo | O nome da função que foi acionada     | string   |
| RandGuid   | um guid exclusivo para esta execução da função | string   |

Cada tipo de Acionador tem um conjunto diferente de metadados. Por exemplo, o `$TriggerMetadata` para `QueueTrigger` contém o `InsertionTime`, `Id`, `DequeueCount`, entre outras coisas. Para obter mais informações nos metadados do acionador de fila, vá para o [documentação oficial para acionadores de fila](functions-bindings-storage-queue.md#trigger---message-metadata). Verifique a documentação sobre o [acionadores](functions-triggers-bindings.md) que está a trabalhar para ver o que vem de dentro os metadados de Acionador.

## <a name="bindings"></a>Enlaces

No PowerShell, [enlaces](functions-triggers-bindings.md) são configuradas e definidos em Function uma função. Funções de interagirem com as ligações de diversas formas.

### <a name="reading-trigger-and-input-data"></a>Acionador de leitura e dados de entrada

Acionador e enlaces de entrada são de leitura como parâmetros passados para a sua função. Enlaces de entrada tem um `direction` definido como `in` na Function. O `name` propriedade definida no `function.json` é o nome do parâmetro, no `param` bloco. Uma vez que o PowerShell utiliza parâmetros nomeados para o enlace, não importa a ordem dos parâmetros. No entanto, é recomendável seguir a ordem dos enlaces definidos no `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Escrita de dados de saída

Em funções, um enlace de saída tem um `direction` definido como `out` na Function. Pode escrever um enlace de saída ao utilizar o `Push-OutputBinding` cmdlet, que está disponível para o runtime das funções. Em todos os casos, o `name` propriedade da ligação, conforme definido na `function.json` corresponde da `Name` parâmetro do `Push-OutputBinding` cmdlet.

O seguinte mostra como chamar `Push-OutputBinding` no seu script de função:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Também é possível passar um valor para uma ligação específica através do pipeline.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` se comporta de forma diferente, consoante o valor especificado para `-Name`:

* Quando o nome especificado não pode ser resolvido para um enlace de saída válida, em seguida, ocorrerá um erro.

* Quando o enlace de saída aceita uma coleção de valores, pode chamar `Push-OutputBinding` repetidamente para enviar por push vários valores.

* Quando o enlace de saída só aceita um valor de singleton, chamando `Push-OutputBinding` uma segunda vez gera um erro.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` Sintaxe

Seguem-se parâmetros válidos para chamar `Push-OutputBinding`:

| Name | Type | Posição | Descrição |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | O nome da ligação de saída que pretende definir. |
| **`-Value`** | Object | 2 | O valor de enlace de saída que pretende definir, que é aceite a partir do pipeline ByValue. |
| **`-Clobber`** | SwitchParameter | com o nome | (Opcional) Quando especificado, força o valor a ser definido para um enlace de saída especificado. | 

Também são suportados os seguintes parâmetros comuns: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Para obter mais informações, consulte [Parâmetroscomuns sobre](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Exemplo de push OutputBinding: Respostas HTTP

Um acionador HTTP retorna uma resposta com um enlace de saída com o nome `response`. No exemplo a seguir, o enlace de saída de `response` tem o valor de "saída #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Uma vez que a saída é a HTTP, que aceita um valor de singleton apenas, ocorrerá um erro quando `Push-OutputBinding` denomina-se uma segunda vez.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Para saídas que apenas aceitam os valores de singleton, pode usar o `-Clobber` parâmetro para substituir o valor antigo em vez de tentar adicionar a uma coleção. O exemplo a seguir pressupõe que já adicionou um valor. Ao utilizar `-Clobber`, a resposta do exemplo seguinte substitui o valor existente para retornar um valor de "saída #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Exemplo de push OutputBinding: Enlace de saída da fila

`Push-OutputBinding` é utilizado para enviar dados para ligações de saída, por exemplo, um [enlace de saída do armazenamento de filas do Azure](functions-bindings-storage-queue.md#output). No exemplo seguinte, a mensagem escrita para a fila tem um valor de "saída #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

O enlace de saída para uma fila de armazenamento aceita vários valores de saída. Neste caso, chamar o exemplo a seguir após o primeiro grava na fila uma lista com dois itens: "saída #1" e "saída #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

O exemplo seguinte, quando chamado após os dois anteriores, adiciona dois valores mais para a coleção de saída:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Quando gravado na fila, a mensagem contém esses quatro valores: "#1 de saída", "saída #2", "#3 de saída" e "saída #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` cmdlet

Pode utilizar o `Get-OutputBinding` cmdlet para obter os valores atualmente definidos para as suas ligações de saída. Este cmdlet obtém uma tabela de hash que contém os nomes das ligações de saída com seus respectivos valores. 

Segue-se um exemplo de uso `Get-OutputBinding` para devolver valores de ligação atual:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` também contém um parâmetro chamado `-Name`, que podem ser utilizadas para filtrar o enlace retornado, como no exemplo seguinte:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Carateres universais (*) são suportados no `Get-OutputBinding`.

## <a name="logging"></a>Registo

Iniciar sessão PowerShell funções funciona como o registo do PowerShell regular. Pode utilizar os cmdlets de registo para escrever cada fluxo de saída. Cada cmdlet é mapeado para um nível de registo utilizado pelas funções.

| Funções de nível de registo | Cmdlet de registo |
| ------------- | -------------- |
| Erro | **`Write-Error`** |
| Aviso | **`Write-Warning`**  | 
| Informações | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informações | Escreve _informações_ registo a nível. |
| Depuração | **`Write-Debug`** |
| Rastreio | **`Write-Progress`** <br /> **`Write-Verbose`** |

Para além destes cmdlets, todos os elementos escritos para o pipeline é redirecionado para o `Information` log nível e exibido com a formatação do PowerShell de predefinição.

> [!IMPORTANT]
> Utilizar o `Write-Verbose` ou `Write-Debug` cmdlets não é suficiente para ver verboso e o registo de nível de depuração. Também tem de configurar o limiar de nível de registo, que declara que nível de registos que realmente me Preocupo. Para obter mais informações, consulte [configurar o nível de registo de aplicação de função](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configurar o nível de registo de aplicação de função

As funções permite que defina o nível de limiar para tornar mais fácil para as funções de forma escreve os registos de controlo. Para definir o limiar para todos os rastreios escritos na consola, utilize o `logging.logLevel.default` propriedade no [ `host.json` ficheiro][referência de host. json]. Esta definição aplica-se a todas as funções na sua aplicação de função.

O exemplo seguinte define o limiar para ativar o registo verboso para todas as funções, mas define o limiar para ativar o registo de depuração para uma função chamada `MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Para obter mais informações, consulte [referência de Host. JSON].

### <a name="viewing-the-logs"></a>Ver os registos

Se a sua aplicação Function App está em execução no Azure, pode utilizar o Application Insights para monitorizá-lo. Leia [monitorizar as funções do Azure](functions-monitoring.md) para saber mais sobre ver e consultar os registos da função.

Se estiver a executar a aplicação de funções localmente para o desenvolvimento, regista o sistema de ficheiros predefinido. Para ver os registos na consola, defina o `AZURE_FUNCTIONS_ENVIRONMENT` variável de ambiente para `Development` antes de iniciar a aplicação de funções.

## <a name="triggers-and-bindings-types"></a>Acionadores e tipos de associação

Um número de acionadores e enlaces estão disponíveis para utilizar com a sua aplicação de função. A lista completa de acionadores e enlaces [podem ser encontradas aqui](functions-triggers-bindings.md#supported-bindings).

Todos os acionadores e enlaces são representados no código, como alguns tipos de dados reais:

* Tabela de hash
* string
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Os primeiros cinco tipos nesta lista são tipos padrão do .NET. As duas últimas são utilizadas apenas pelo [HttpTrigger acionador](#http-triggers-and-bindings).

Cada parâmetro de enlace em suas funções tem de ser um dos seguintes tipos.

### <a name="http-triggers-and-bindings"></a>HTTP acionadores e enlaces

HTTP e acionadores de webhook e HTTP de saída ligações usam os objetos request e response para representar a mensagem de HTTP.

#### <a name="request-object"></a>Objeto de solicitação

O objeto de solicitação que é passado para o script é do tipo `HttpRequestContext`, que tem as seguintes propriedades:

| Propriedade  | Descrição                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Um objeto que contém o corpo do pedido. `Body` é serializado para o melhor tipo com base nos dados. Por exemplo, se os dados JSON, ele é passado como uma tabela de hash. Se os dados são uma cadeia de caracteres, ele é passado como uma cadeia de caracteres. | objeto |
| **`Headers`** | Um dicionário que contém os cabeçalhos de pedido.                | Dictionary < string, string ><sup>*</sup> |
| **`Method`** | O método HTTP do pedido.                                | string                    |
| **`Params`**  | Um objeto que contém os parâmetros de encaminhamento do pedido. | Dictionary < string, string ><sup>*</sup> |
| **`Query`** | Um objeto que contém os parâmetros de consulta.                  | Dictionary < string, string ><sup>*</sup> |
| **`Url`** | O URL do pedido.                                        | string                    |

<sup>*</sup> Todos os `Dictionary<string,string>` chaves diferenciam maiúsculas de minúsculas.

#### <a name="response-object"></a>Objeto de resposta

O objeto de resposta que deve enviar de volta é do tipo `HttpResponseContext`, que tem as seguintes propriedades:

| Propriedade      | Descrição                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Um objeto que contém o corpo da resposta.           | objeto                    |
| **`ContentType`** | Uma mão curta para definir o tipo de conteúdo para a resposta. | string                    |
| **`Headers`** | Um objeto que contém os cabeçalhos de resposta.               | Dicionário ou tabela de hash   |
| **`StatusCode`**  | O código de estado HTTP da resposta.                       | cadeia de caracteres ou int             |

#### <a name="accessing-the-request-and-response"></a>Aceder a solicitação e resposta

Ao trabalhar com acionadores HTTP, pode acessar o pedido HTTP da mesma forma que faria com qualquer outra ligação de entrada. Está a ser o `param` bloco.

Utilize um `HttpResponseContext` objeto para retornar uma resposta, como mostrado a seguir:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

O resultado de invocar essa função seria:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Conversão de tipo para acionadores e enlaces

Para determinados ligações, como o enlace de blob, pode especificar o tipo do parâmetro.

Por exemplo, tiver dados de armazenamento de BLOBs fornecido como uma cadeia de caracteres, adicione o seguinte tipo convertido no meu `param` bloco:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Perfil do PowerShell

No PowerShell, existe o conceito de um perfil do PowerShell. Se não estiver familiarizado com perfis de PowerShell, veja [sobre os perfis](/powershell/module/microsoft.powershell.core/about/about_profiles).

Nas funções do PowerShell, o script de perfil é executado quando a aplicação de funções é iniciado. Aplicações de função começam quando implantado pela primeira vez e depois a ser inativo ([arranque a frio](#cold-start)).

Quando cria uma aplicação de funções através de ferramentas, como o Visual Studio Code e ferramentas de núcleo de funções do Azure, um padrão `profile.ps1` é criada por si. O perfil predefinido é mantido [no repositório do GitHub de ferramentas de núcleo](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contém:

* Autenticação de MSI automática para o Azure.
* A capacidade de ativar o Azure PowerShell `AzureRM` aliases de PowerShell se desejar.

## <a name="powershell-version"></a>Versão do PowerShell

A tabela seguinte mostra a versão do PowerShell utilizada por cada versão principal do runtime das funções:

| Versão de funções | Versão do PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (bloqueados pelo tempo de execução) |
| 2.x               | O PowerShell Core 6                              |

Pode ver a versão atual imprimindo `$PSVersionTable` de qualquer função.

## <a name="dependency-management"></a>Gestão de dependências

Funções do PowerShell suportam a gestão de módulos do Azure pelo serviço. Modificando o Host. JSON e definir a propriedade de managedDependency ativado como true, o ficheiro de requirements.psd1 será processado. Os módulos do Azure mais recente serão automaticamente transferidos e disponibilizados para a função.

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

Tirar partido do seu próprio módulos ou módulos do [galeria do PowerShell](https://powershellgallery.com) é um pouco diferente de como o faria normalmente.

Quando instala o módulo no seu computador local, ele está presente em uma das pastas globalmente disponíveis no seu `$env:PSModulePath`. Uma vez que a função é executada no Azure, não terá acesso para os módulos instalados no seu computador. Isto requer que o `$env:PSModulePath` para uma função do PowerShell a aplicação é diferente do `$env:PSModulePath` num script do PowerShell regular.

Em funções, `PSModulePath` contém dois caminhos:

* A `Modules` pasta que existe na raiz da sua aplicação de funções.
* Um caminho para um `Modules` pasta que se encontrem dentro da função de trabalho de linguagem do PowerShell.

### <a name="function-app-level-modules-folder"></a>Nível de aplicação de função `Modules` pasta

Para utilizar módulos personalizados ou módulos do PowerShell a partir da galeria do PowerShell, pode colocar módulos as suas funções que dependem de um `Modules` pasta. Nessa pasta, os módulos estão automaticamente disponíveis para o runtime das funções. Qualquer função na aplicação de função pode utilizar estes módulos.

Para tirar partido desta funcionalidade, crie um `Modules` pasta na raiz da sua aplicação de funções. Guarde os módulos que pretende utilizar nas suas funções nesta localização.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Utilizar `Save-Module` para guardar todos os módulos utilizam as suas funções ou copiar os seus próprios módulos personalizados para o `Modules` pasta. Com uma pasta de módulos, a aplicação de função deve ter a seguinte estrutura de pastas:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

Quando começa a sua aplicação function app, a função de trabalho de linguagem do PowerShell adiciona isso `Modules` pasta para o `$env:PSModulePath` para que pode contar módulo autoloading tal como faria num script do PowerShell regular.

### <a name="language-worker-level-modules-folder"></a>Nível de função de trabalho de idioma `Modules` pasta

Vários módulos são frequentemente utilizados pela função de trabalho do PowerShell idioma. Estes módulos são definidos na última posição de `PSModulePath`. 

A lista atual dos módulos é o seguinte:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): módulo utilizado para trabalhar com arquivos, como `.zip`, `.nupkg`entre outros.
* **ThreadJob**: Uma implementação baseada em thread do trabalho de PowerShell APIs.

A versão mais recente destes módulos é utilizada por funções. Para utilizar uma versão específica desses módulos, pode colocar a versão específica `Modules` pasta da sua aplicação de função.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [as definições da aplicação](functions-app-settings.md), por exemplo, a ligação de serviço são expostos cadeias de caracteres, como variáveis de ambiente durante a execução. Pode acessar essas configurações usando `$env:NAME_OF_ENV_VAR`, conforme mostrado no exemplo a seguir:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as definições da aplicação são lidos a partir da [Settings](functions-run-local.md#local-settings-file) arquivo de projeto.

## <a name="concurrency"></a>Simultaneidade

Por predefinição, o tempo de execução do PowerShell de funções só pode processar uma invocação de uma função de cada vez. No entanto, este nível de simultaneidade pode não ser suficiente nas seguintes situações:

* Quando está tentando processar um grande número de invocações ao mesmo tempo.
* Quando tem funções que invocam outras funções na mesma aplicação de função.

Pode alterar este comportamento ao definir a seguinte variável de ambiente para um valor inteiro:

```
PSWorkerInProcConcurrencyUpperBound
```

Definir esta variável de ambiente no [as definições da aplicação](functions-app-settings.md) da sua aplicação de função.

### <a name="considerations-for-using-concurrency"></a>Considerações sobre a utilização de simultaneidade

O PowerShell é um _single threaded_ linguagem de script por predefinição. No entanto, simultaneidade pode ser adicionada com vários espaços de execução do PowerShell no mesmo processo. Esta funcionalidade é como funciona o tempo de execução do PowerShell de funções do Azure.

Existem algumas desvantagens nessa abordagem.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>Só é tão bom quanto a máquina está em execução em simultaneidade

Se a sua aplicação function app está em execução [plano do App Service](functions-scale.md#app-service-plan) que suporta apenas um único núcleo, em seguida, simultaneidade não ajudará muito. Isso ocorre porque existem não existem núcleos adicionais para ajudar a equilibrar a carga. Neste caso, o desempenho pode variar quando tem um núcleo de alternância de contexto entre espaços de execução.

O [plano de consumo](functions-scale.md#consumption-plan) é executado usando apenas um núcleo, de forma que não é possível aproveitar a simultaneidade. Se pretender tirar partido de simultaneidade, em vez disso, implemente as suas funções para uma aplicação de funções em execução num plano de serviço de aplicações dedicado com núcleos suficientes.

#### <a name="azure-powershell-state"></a>Estado do Azure PowerShell

O Azure PowerShell utiliza alguns _ao nível do processo_ contextos e o estado para ajudar a poupar da digitação em excesso. No entanto, se ativar a simultaneidade na sua aplicação de função e invocar ações que alteram o estado, pode acabar com condições de corrida. Estas condições de corrida são difíceis de depurar, pois a invocação de uma conta com um determinado Estado e a outra invocação alterou o estado.

Há grande valor em simultaneidade com o Azure PowerShell, uma vez que algumas operações podem demorar uma quantidade considerável de tempo. No entanto, deve continuar com cuidado. Se suspeitar que está a experienciar uma condição de corrida, defina a simultaneidade para `1` e tente novamente o pedido.

## <a name="configure-function-scriptfile"></a>Configurar a função `scriptFile`

Por predefinição, uma função do PowerShell é executada a partir `run.ps1`, um ficheiro que partilha o mesmo diretório principal de seu correspondente `function.json`.

O `scriptFile` propriedade o `function.json` pode ser usado para obter uma estrutura de pastas é semelhante ao seguinte exemplo:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Neste caso, o `function.json` para `myFunction` inclui um `scriptFile` propriedade que referencia o ficheiro com a função exportada para executar.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Utilizar módulos do PowerShell ao configurar um ponto de entrada

Este artigo mostrou funções do PowerShell predefinida `run.ps1` gerado pelos modelos de ficheiro de script.
No entanto, também pode incluir as suas funções nos módulos do PowerShell. Pode referenciar o seu código de função específica no módulo ao utilizar o `scriptFile` e `entryPoint` campos a Function "ficheiro de configuração.

Neste caso, `entryPoint` é o nome de uma função ou o cmdlet no módulo do PowerShell referenciado no `scriptFile`.

Considere a seguinte estrutura de pastas:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Onde `PSFunction.psm1` contém:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Neste exemplo, a configuração para `myFunction` inclui um `scriptFile` propriedade que faz referência `PSFunction.psm1`, que é um módulo do PowerShell na pasta de outro.  O `entryPoint` referências de propriedade a `Invoke-PSTestFunc` função, que é o ponto de entrada no módulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Com esta configuração, o `Invoke-PSTestFunc` é executado exatamente como um `run.ps1` seria.

## <a name="considerations-for-powershell-functions"></a>Considerações para funções do PowerShell

Ao trabalhar com funções do PowerShell, lembre-se de que as considerações nas seções a seguir.

### <a name="cold-start"></a>Arranque a frio

Ao desenvolver funções do Azure no [modelo de alojamento sem servidor](functions-scale.md#consumption-plan), cold começa é uma realidade. *Arranque a frio* refere-se ao período de tempo ele leva para a sua aplicação de função iniciar a execução para processar um pedido. Arranque a frio ocorre mais frequentemente o consumo de plano porque a sua aplicação function app é encerrada durante os períodos de inatividade.

### <a name="bundle-modules-instead-of-using-install-module"></a>Módulos de pacote em vez de usar `Install-Module`

O script é executado em cada invocação. Evite utilizar `Install-Module` no seu script. Em alternativa utilize `Save-Module` antes de publicar para que sua função não tem a perder tempo a transferir o módulo. Se arranques a frio estejam a afetar as suas funções, considere implementar a sua aplicação de função para um [plano do App Service](functions-scale.md#app-service-plan) definida como *sempre ativo* ou uma [plano Premium](functions-scale.md#premium-plan-public-preview).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Acionadores de funções do Azure e enlaces](functions-triggers-bindings.md)

[referência de Host. JSON]: functions-host-json.md
