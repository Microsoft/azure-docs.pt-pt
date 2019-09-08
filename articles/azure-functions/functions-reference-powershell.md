---
title: Referência do desenvolvedor do PowerShell para Azure Functions
description: Entenda como desenvolver funções usando o PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 36d24e798e73ef336324eedadee1ba3fec4c0e1d
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773037"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guia do desenvolvedor do Azure Functions PowerShell

Este artigo fornece detalhes sobre como você escreve Azure Functions usando o PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Uma função do PowerShell do Azure (função) é representada como um script do PowerShell que é executado quando disparado. Cada script de função tem um `function.json` arquivo relacionado que define como a função se comporta, como como ele é disparado e seus parâmetros de entrada e saída. Para saber mais, confira o [artigo gatilhos e Associação](functions-triggers-bindings.md). 

Assim como outros tipos de funções, as funções de script do PowerShell assumem parâmetros que correspondem aos nomes de todas as associações de `function.json` entrada definidas no arquivo. Também `TriggerMetadata` é passado um parâmetro que contém informações adicionais sobre o gatilho que iniciou a função.

Este artigo pressupõe que você já tenha lido o [Azure Functions referência do desenvolvedor](functions-reference.md). Você também deve ter concluído o guia de [início rápido do Functions para o PowerShell](functions-create-first-function-powershell.md) para criar sua primeira função do PowerShell.

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas necessária para um projeto do PowerShell é semelhante ao seguinte. Esse padrão pode ser alterado. Para obter mais informações, consulte a seção [scriptfile](#configure-function-scriptfile) abaixo.

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

Na raiz do projeto, há um arquivo compartilhado [`host.json`](functions-host-json.md) que pode ser usado para configurar o aplicativo de funções. Cada função tem uma pasta com seu próprio arquivo de código (. ps1) e arquivo de configuração`function.json`de associação (). O nome do diretório pai do arquivo function. JSON é sempre o nome da sua função.

Determinadas associações exigem a presença de um `extensions.csproj` arquivo. As extensões de associação, necessárias na [versão 2. x](functions-versions.md) do tempo de execução do Functions `extensions.csproj` , são definidas no arquivo, com os arquivos `bin` de biblioteca reais na pasta. Ao desenvolver localmente, você deve [registrar extensões de associação](functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

Em aplicativos de funções do PowerShell, você pode opcionalmente `profile.ps1` ter um que é executado quando um aplicativo de funções começa a ser executado (caso contrário, saiba como um *[início frio](#cold-start)* . Para obter mais informações, consulte [perfil do PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definindo um script do PowerShell como uma função

Por padrão, o tempo de execução do Functions procura `run.ps1`sua função `run.ps1` no, onde compartilha o mesmo diretório pai `function.json`que o correspondente.

Seu script é passado por vários argumentos na execução. Para lidar com esses parâmetros, adicione `param` um bloco à parte superior do seu script, como no exemplo a seguir:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parâmetro TriggerMetadata

O `TriggerMetadata` parâmetro é usado para fornecer informações adicionais sobre o gatilho. Os metadados adicionais variam de associação à associação, mas todos contêm uma `sys` propriedade que contém os seguintes dados:

```powershell
$TriggerMetadata.sys
```

| Propriedade   | Descrição                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Quando, em UTC, a função foi disparada        | DateTime |
| NomeMétodo | O nome da função que foi disparada     | Cadeia de caracteres   |
| RandGuid   | um GUID exclusivo para esta execução da função | Cadeia de caracteres   |

Cada tipo de gatilho tem um conjunto diferente de metadados. Por exemplo, o `$TriggerMetadata` para `QueueTrigger` contém o `InsertionTime`, `Id`, `DequeueCount`, entre outras coisas. Para obter mais informações sobre os metadados do gatilho de fila, acesse a [documentação oficial para gatilhos de fila](functions-bindings-storage-queue.md#trigger---message-metadata). Verifique a documentação nos [gatilhos](functions-triggers-bindings.md) com os quais você está trabalhando para ver o que acontece nos metadados do gatilho.

## <a name="bindings"></a>Enlaces

No PowerShell, as [associações](functions-triggers-bindings.md) são configuradas e definidas no function. JSON de uma função. As funções interagem com associações de várias maneiras.

### <a name="reading-trigger-and-input-data"></a>Lendo dados de gatilho e de entrada

As associações de entrada e gatilho são lidas como parâmetros passados para sua função. As associações de entrada têm `direction` um definido `in` como in function. JSON. A `name` propriedade definida em `function.json` é o nome do parâmetro, no `param` bloco. Como o PowerShell usa parâmetros nomeados para associação, a ordem dos parâmetros não importa. No entanto, é uma prática recomendada seguir a ordem das associações definidas no `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Gravando dados de saída

No functions, uma associação de saída `direction` tem um `out` definido como no function. JSON. Você pode gravar em uma associação de saída usando o `Push-OutputBinding` cmdlet, que está disponível para o tempo de execução do functions. Em todos os casos, `name` a propriedade da associação, conforme definido `function.json` em, corresponde `Name` `Push-OutputBinding` ao parâmetro do cmdlet.

O seguinte mostra como chamar `Push-OutputBinding` em seu script de função:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Você também pode passar um valor para uma ligação específica por meio do pipeline.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`comporta-se de forma diferente com base no valor `-Name`especificado para:

* Quando o nome especificado não puder ser resolvido para uma associação de saída válida, um erro será gerado.

* Quando a associação de saída aceita uma coleção de valores, você pode `Push-OutputBinding` chamar repetidamente para enviar vários valores por push.

* Quando a associação de saída aceita apenas um valor singleton, `Push-OutputBinding` chamar uma segunda vez gera um erro.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`sintaxe

Veja a seguir os parâmetros válidos para `Push-OutputBinding`chamar:

| Name | Type | Posição | Descrição |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Cadeia | 1 | O nome da Associação de saída que você deseja definir. |
| **`-Value`** | Objeto | 2 | O valor da Associação de saída que você deseja definir, que é aceita do pipeline ByValue. |
| **`-Clobber`** | SwitchParameter | Nomeado | Adicional Quando especificado, força o valor a ser definido para uma associação de saída especificada. | 

Os seguintes parâmetros comuns também têm suporte: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Para obter mais informações, consulte [sobre CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Exemplo de push-OutputBinding: Respostas HTTP

Um gatilho HTTP retorna uma resposta usando uma associação de saída `response`denominada. No exemplo a seguir, a associação de saída `response` de tem o valor de "saída #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Como a saída é para http, que aceita apenas um valor singleton, um erro é gerado quando `Push-OutputBinding` é chamado uma segunda vez.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Para saídas que aceitam apenas valores singleton, você pode usar `-Clobber` o parâmetro para substituir o valor antigo em vez de tentar adicionar a uma coleção. O exemplo a seguir pressupõe que você já adicionou um valor. Usando `-Clobber`, a resposta do exemplo a seguir substitui o valor existente para retornar um valor de "saída #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Exemplo de push-OutputBinding: Associação de saída da fila

`Push-OutputBinding`é usado para enviar dados para associações de saída, como uma [Associação de saída do armazenamento de filas do Azure](functions-bindings-storage-queue.md#output). No exemplo a seguir, a mensagem gravada na fila tem um valor de "saída #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

A associação de saída para uma fila de armazenamento aceita vários valores de saída. Nesse caso, chamar o exemplo a seguir após a primeira gravação na fila em uma lista com dois itens: "output #1" e "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

O exemplo a seguir, quando chamado após os dois anteriores, adiciona mais dois valores à coleção de saída:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Quando gravado na fila, a mensagem contém estes quatro valores: "saída #1", "saída #2", "saída #3" e "#4 de saída".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`cmdlet

Você pode usar o `Get-OutputBinding` cmdlet para recuperar os valores definidos atualmente para suas associações de saída. Esse cmdlet recupera uma tabela de hash que contém os nomes das associações de saída com seus respectivos valores. 

Veja a seguir um exemplo de como `Get-OutputBinding` usar para retornar valores de associação atuais:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`também contém um parâmetro chamado `-Name`, que pode ser usado para filtrar a associação retornada, como no exemplo a seguir:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Há suporte para caracteres curinga (*) `Get-OutputBinding`no.

## <a name="logging"></a>Registo

O registro em log nas funções do PowerShell funciona como log normal do PowerShell. Você pode usar os cmdlets de log para gravar em cada fluxo de saída. Cada cmdlet é mapeado para um nível de log usado pelas funções.

| Nível de log de funções | Cmdlet de registro em log |
| ------------- | -------------- |
| Erro | **`Write-Error`** |
| Aviso | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Grava no log do nível de _informações_ . |
| depurar | **`Write-Debug`** |
| Rastreio | **`Write-Progress`** <br /> **`Write-Verbose`** |

Além desses cmdlets, qualquer coisa gravada no pipeline é redirecionada para o nível `Information` de log e exibida com a formatação padrão do PowerShell.

> [!IMPORTANT]
> Usar os `Write-Verbose` cmdlets ou `Write-Debug` não é suficiente para ver o log detalhado e de nível de depuração. Você também deve configurar o limite de nível de log, que declara o nível de logs que você realmente importa. Para saber mais, confira [Configurar o nível de log do aplicativo de funções](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configurar o nível de log do aplicativo de funções

Azure Functions permite que você defina o nível de limite para facilitar o controle da maneira como as funções são gravadas nos logs. Para definir o limite para todos os rastreamentos gravados no console, `logging.logLevel.default` use a propriedade na referência do [ `host.json` arquivo][referência de host. json]. Essa configuração se aplica a todas as funções em seu aplicativo de funções.

O exemplo a seguir define o limite para habilitar o log detalhado para todas as funções, mas define o limite para habilitar o log de `MyFunction`depuração para uma função chamada:

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

Para obter mais informações, consulte [referência de host. JSON].

### <a name="viewing-the-logs"></a>Exibindo os logs

Se o Aplicativo de funções estiver em execução no Azure, você poderá usar Application Insights para monitorá-lo. Leia [Azure Functions de monitoramento](functions-monitoring.md) para saber mais sobre como exibir e consultar logs de função.

Se você estiver executando o Aplicativo de funções localmente para desenvolvimento, os logs serão padrão para o sistema de arquivos. Para ver os logs no console, defina a `AZURE_FUNCTIONS_ENVIRONMENT` variável de ambiente como `Development` antes de iniciar o aplicativo de funções.

## <a name="triggers-and-bindings-types"></a>Tipos de associações e gatilhos

Há vários gatilhos e associações disponíveis para você usar com seu aplicativo de funções. A lista completa de gatilhos e associações [pode ser encontrada aqui](functions-triggers-bindings.md#supported-bindings).

Todos os gatilhos e associações são representados no código como alguns tipos de dados reais:

* Tabela
* Cadeia de caracteres
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Os cinco primeiros tipos nessa lista são tipos .NET padrão. Os dois últimos são usados somente pelo [gatilho HttpTrigger](#http-triggers-and-bindings).

Cada parâmetro de associação em suas funções deve ser um desses tipos.

### <a name="http-triggers-and-bindings"></a>Gatilhos e associações HTTP

Os gatilhos HTTP e webhook e as associações de saída HTTP usam objetos Request e Response para representar as mensagens HTTP.

#### <a name="request-object"></a>Objeto de solicitação

O objeto de solicitação que é passado para o script é do tipo `HttpRequestContext`, que tem as seguintes propriedades:

| Propriedade  | Descrição                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Um objeto que contém o corpo da solicitação. `Body`é serializado no melhor tipo com base nos dados. Por exemplo, se os dados forem JSON, eles serão passados como uma tabela de hash. Se os dados forem uma cadeia de caracteres, eles serão passados como uma cadeia de caracteres. | object |
| **`Headers`** | Um dicionário que contém os cabeçalhos de solicitação.                | < De cadeia de caracteres de dicionário, Cadeia de caracteres ><sup>*</sup> |
| **`Method`** | O método HTTP da solicitação.                                | Cadeia de caracteres                    |
| **`Params`**  | Um objeto que contém os parâmetros de roteamento da solicitação. | < De cadeia de caracteres de dicionário, Cadeia de caracteres ><sup>*</sup> |
| **`Query`** | Um objeto que contém os parâmetros de consulta.                  | < De cadeia de caracteres de dicionário, Cadeia de caracteres ><sup>*</sup> |
| **`Url`** | A URL da solicitação.                                        | Cadeia de caracteres                    |

<sup>*</sup>Todas `Dictionary<string,string>` as chaves não diferenciam maiúsculas de minúsculas.

#### <a name="response-object"></a>Objeto de resposta

O objeto de resposta que você deve enviar de volta é do `HttpResponseContext`tipo, que tem as seguintes propriedades:

| Propriedade      | Descrição                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Um objeto que contém o corpo da resposta.           | object                    |
| **`ContentType`** | Uma pequena mão para definir o tipo de conteúdo para a resposta. | Cadeia de caracteres                    |
| **`Headers`** | Um objeto que contém os cabeçalhos de resposta.               | Dicionário ou Hashtable   |
| **`StatusCode`**  | O código de status HTTP da resposta.                       | Cadeia de caracteres ou int             |

#### <a name="accessing-the-request-and-response"></a>Acessando a solicitação e a resposta

Ao trabalhar com gatilhos HTTP, você pode acessar a solicitação HTTP da mesma maneira que faria com qualquer outra associação de entrada. Está no `param` bloco.

Use um `HttpResponseContext` objeto para retornar uma resposta, conforme mostrado a seguir:

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

O resultado da invocação dessa função seria:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Conversão de tipo para gatilhos e associações

Para determinadas associações como a associação de BLOB, você pode especificar o tipo do parâmetro.

Por exemplo, para ter dados do armazenamento de BLOBs fornecidos como uma cadeia de caracteres, adicione a seguinte `param` conversão de tipo ao meu bloco:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Perfil do PowerShell

No PowerShell, há o conceito de um perfil do PowerShell. Se você não estiver familiarizado com os perfis do PowerShell, consulte [about Profiles](/powershell/module/microsoft.powershell.core/about/about_profiles).

Em funções do PowerShell, o script de perfil é executado quando o aplicativo de funções é iniciado. Os aplicativos de funções começam quando são implantados pela primeira vez e depois ficam ociosos ([inicialização a frio](#cold-start)).

Quando você cria um aplicativo de funções usando ferramentas, como Visual Studio Code e Azure Functions Core Tools, um padrão `profile.ps1` é criado para você. O perfil padrão é mantido [no repositório GitHub das ferramentas principais](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contém:

* Autenticação automática de MSI para o Azure.
* A capacidade de ativar o Azure PowerShell `AzureRM` aliases do PowerShell, se desejar.

## <a name="powershell-version"></a>Versão do PowerShell

A tabela a seguir mostra a versão do PowerShell usada por cada versão principal do tempo de execução do Functions:

| Versão das funções | Versão do PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5,1 (bloqueado pelo tempo de execução) |
| 2.x               | PowerShell Core 6                              |

Você pode ver a versão atual imprimindo `$PSVersionTable` de qualquer função.

## <a name="dependency-management"></a>Gestão de dependências

As funções do PowerShell dão suporte ao download e gerenciamento de módulos da [Galeria do PowerShell](https://www.powershellgallery.com) pelo serviço. Ao modificar o host. JSON e definir a propriedade managedDependency Enabled como true, o arquivo Requirements. psd1 será processado. Os módulos especificados serão baixados automaticamente e disponibilizados para a função. 

O número máximo de módulos com suporte no momento é 10. A sintaxe com suporte é MajorNumber. * ou versão exata do módulo, conforme mostrado abaixo. O módulo AZ do Azure é incluído por padrão quando um novo aplicativo de funções do PowerShell é criado.

O trabalho de idioma irá pegar todos os módulos atualizados em uma reinicialização.

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
    SqlServer = '21.1.18147'
}
```

Aproveitar seus próprios módulos personalizados é um pouco diferente de como você faria normalmente.

Quando você instala o módulo em seu computador local, ele entra em uma das pastas disponíveis globalmente no seu `$env:PSModulePath`. Como sua função é executada no Azure, você não terá acesso aos módulos instalados em seu computador. Isso requer que o `$env:PSModulePath` para um aplicativo de funções do PowerShell seja `$env:PSModulePath` diferente de em um script do PowerShell regular.

No functions `PSModulePath` , o contém dois caminhos:

* Uma `Modules` pasta que existe na raiz do seu aplicativo de funções.
* Um caminho para uma `Modules` pasta que reside dentro do operador de linguagem do PowerShell.

### <a name="function-app-level-modules-folder"></a>Pasta de nível `Modules` de aplicativo de função

Para usar módulos personalizados, você pode posicionar os módulos nos quais suas funções dependem `Modules` de uma pasta. Nessa pasta, os módulos ficam automaticamente disponíveis para o tempo de execução do functions. Qualquer função no aplicativo de funções pode usar esses módulos. 

> [!NOTE]
> Os módulos especificados no arquivo Requirements. psd1 são baixados e incluídos automaticamente no caminho para que você não precise incluí-los na pasta modules. Eles são armazenados localmente na pasta $env: LOCALAPPDATA/AzureFunctions e na pasta/data/ManagedDependencies quando executados na nuvem.

Para aproveitar o recurso de módulo personalizado, crie uma `Modules` pasta na raiz do seu aplicativo de funções. Copie os módulos que você deseja usar em suas funções para esse local.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Com uma pasta modules, seu aplicativo de funções deve ter a seguinte estrutura de pastas:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Quando você inicia seu aplicativo de funções, o operador de linguagem do `Modules` PowerShell adiciona essa `$env:PSModulePath` pasta ao para que você possa contar com o carregamento automático de módulo da mesma forma que faria em um script do PowerShell normal.

### <a name="language-worker-level-modules-folder"></a>Pasta de nível `Modules` de trabalho do idioma

Vários módulos são geralmente usados pelo operador de linguagem do PowerShell. Esses módulos são definidos na última posição de `PSModulePath`. 

A lista atual de módulos é a seguinte:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): módulo usado para trabalhar com arquivos mortos, `.zip`como `.nupkg`, e outros.
* **ThreadJob**: Uma implementação baseada em thread das APIs de trabalho do PowerShell.

A versão mais recente desses módulos é usada pelas funções do. Para usar uma versão específica desses módulos, você pode colocar a versão específica na `Modules` pasta do seu aplicativo de funções.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [as configurações de aplicativo](functions-app-settings.md), como cadeias de conexão de serviço, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações usando `$env:NAME_OF_ENV_VAR`, conforme mostrado no exemplo a seguir:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as configurações do aplicativo são lidas no arquivo de projeto [local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="concurrency"></a>Simultaneidade

Por padrão, o tempo de execução do PowerShell do Functions só pode processar uma invocação de uma função por vez. No entanto, esse nível de simultaneidade pode não ser suficiente nas seguintes situações:

* Quando você está tentando lidar com um grande número de invocações ao mesmo tempo.
* Quando você tem funções que invocam outras funções dentro do mesmo aplicativo de funções.

Você pode alterar esse comportamento definindo a seguinte variável de ambiente como um valor inteiro:

```
PSWorkerInProcConcurrencyUpperBound
```

Você define essa variável de ambiente nas [configurações de aplicativo](functions-app-settings.md) de seu aplicativo de funções.

### <a name="considerations-for-using-concurrency"></a>Considerações sobre o uso de simultaneidade

O PowerShell é uma linguagem de script de _thread único_ por padrão. No entanto, a simultaneidade pode ser adicionada usando vários espaços de uso do PowerShell no mesmo processo. A quantidade de Runspaces criados corresponderá à configuração do aplicativo PSWorkerInProcConcurrencyUpperBound. A taxa de transferência será afetada pela quantidade de CPU e memória disponíveis no plano selecionado.

Azure PowerShell usa alguns contextos de _nível de processo_ e um estado para ajudá-lo a evitar a digitação de excesso de tipos. No entanto, se você ativar a simultaneidade em seu aplicativo de funções e invocar ações que alteram o estado, poderá acabar com condições de corrida. Essas condições de corrida são difíceis de depurar porque uma invocação depende de um determinado Estado e a outra invocação alterou o estado.

Há um grande valor em simultaneidade com Azure PowerShell, já que algumas operações podem levar um tempo considerável. No entanto, você deve continuar com cautela. Se você suspeitar de que está enfrentando uma condição de corrida, defina a configuração de `1` aplicativo PSWorkerInProcConcurrencyUpperBound como e, em vez disso, use o [isolamento de nível de processo de trabalho de idioma](functions-app-settings.md#functions_worker_process_count) para simultaneidade.

## <a name="configure-function-scriptfile"></a>Configurar função`scriptFile`

Por padrão, uma função do PowerShell é executada `run.ps1`no, um arquivo que compartilha o mesmo diretório pai que o `function.json`correspondente.

A `scriptFile` propriedade`function.json` no pode ser usada para obter uma estrutura de pastas parecida com o exemplo a seguir:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Nesse caso, o `function.json` para `myFunction` inclui uma `scriptFile` propriedade que referencia o arquivo com a função exportada a ser executada.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Usar módulos do PowerShell Configurando um ponto de entrada

Este artigo mostrou as funções do PowerShell no arquivo `run.ps1` de script padrão gerado pelos modelos.
No entanto, você também pode incluir suas funções em módulos do PowerShell. Você pode referenciar seu código de função específico no módulo usando os `scriptFile` campos `entryPoint` e no arquivo de configuração function. JSON.

Nesse caso, `entryPoint` é o nome de uma função ou cmdlet no módulo do PowerShell referenciado em `scriptFile`.

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

Neste exemplo, a configuração de `myFunction` inclui uma `scriptFile` propriedade que faz referência `PSFunction.psm1`a, que é um módulo do PowerShell em outra pasta.  A `entryPoint` Propriedade faz referência `Invoke-PSTestFunc` à função, que é o ponto de entrada no módulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Com essa configuração, o `Invoke-PSTestFunc` é executado exatamente `run.ps1` como faria.

## <a name="considerations-for-powershell-functions"></a>Considerações para funções do PowerShell

Ao trabalhar com as funções do PowerShell, esteja ciente das considerações nas seções a seguir.

### <a name="cold-start"></a>Início frio

Ao desenvolver Azure Functions no [modelo de hospedagem sem servidor](functions-scale.md#consumption-plan), inícios frios são uma realidade. *Início frio* refere-se ao período de tempo que leva para seu aplicativo de funções iniciar a execução para processar uma solicitação. A inicialização a frio acontece com mais frequência no plano de consumo porque seu aplicativo de funções é desligado durante períodos de inatividade.

### <a name="bundle-modules-instead-of-using-install-module"></a>Agrupar módulos em vez de usar`Install-Module`

O script é executado em cada invocação. Evite usar `Install-Module` em seu script. Em vez `Save-Module` disso, use antes de publicar para que sua função não precise perder tempo baixando o módulo. Se a frio for iniciada, afetando suas funções, considere implantar seu aplicativo de funções em um [plano do serviço de aplicativo](functions-scale.md#app-service-plan) definido como *Always on* ou em um [plano Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Azure Functions gatilhos e associações](functions-triggers-bindings.md)

[referência de host. JSON]: functions-host-json.md
