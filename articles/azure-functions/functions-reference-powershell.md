---
title: Referência do desenvolvedor do PowerShell para Azure Functions
description: Entenda como desenvolver funções usando o PowerShell.
author: eamonoreilly
manager: gwallace
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: glenga
ms.openlocfilehash: ae3b8294c7bd91bcd6a2e0e533f5903f44e8aaea
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173667"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guia do desenvolvedor do Azure Functions PowerShell

Este artigo fornece detalhes sobre como você escreve Azure Functions usando o PowerShell.

Uma função do PowerShell do Azure (função) é representada como um script do PowerShell que é executado quando disparado. Cada script de função tem um arquivo de `function.json` relacionado que define como a função se comporta, como a forma como ela é disparada e seus parâmetros de entrada e saída. Para saber mais, confira o [artigo gatilhos e Associação](functions-triggers-bindings.md). 

Assim como outros tipos de funções, as funções de script do PowerShell assumem parâmetros que correspondem aos nomes de todas as associações de entrada definidas no arquivo `function.json`. Um parâmetro `TriggerMetadata` também é passado que contém informações adicionais sobre o gatilho que iniciou a função.

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

Na raiz do projeto, há um arquivo de [`host.json`](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função tem uma pasta com seu próprio arquivo de código (. ps1) e arquivo de configuração de associação (`function.json`). O nome do diretório pai do arquivo function. JSON é sempre o nome da sua função.

Determinadas associações exigem a presença de um arquivo de `extensions.csproj`. As extensões de associação, necessárias na [versão 2. x](functions-versions.md) do tempo de execução do functions, são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você deve [registrar extensões de associação](functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

Em aplicativos de funções do PowerShell, você pode, opcionalmente, ter um `profile.ps1` que é executado quando um aplicativo de funções começa a ser executado (caso contrário, é conhecido como um *[início frio](#cold-start)* . Para obter mais informações, consulte [perfil do PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definindo um script do PowerShell como uma função

Por padrão, o tempo de execução do Functions procura sua função em `run.ps1`, em que `run.ps1` compartilha o mesmo diretório pai que seu `function.json`correspondente.

Seu script é passado por vários argumentos na execução. Para lidar com esses parâmetros, adicione um bloco de `param` na parte superior do seu script, como no exemplo a seguir:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parâmetro TriggerMetadata

O parâmetro `TriggerMetadata` é usado para fornecer informações adicionais sobre o gatilho. Os metadados adicionais variam de associação à associação, mas todos contêm uma propriedade `sys` que contém os seguintes dados:

```powershell
$TriggerMetadata.sys
```

| Propriedade   | Descrição                                     | Tipo     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Quando, em UTC, a função foi disparada        | DateTime |
| MethodName | O nome da função que foi disparada     | string   |
| RandGuid   | um GUID exclusivo para esta execução da função | string   |

Cada tipo de gatilho tem um conjunto diferente de metadados. Por exemplo, o `$TriggerMetadata` para `QueueTrigger` contém a `InsertionTime`, `Id`, `DequeueCount`, entre outras coisas. Para obter mais informações sobre os metadados do gatilho de fila, acesse a [documentação oficial para gatilhos de fila](functions-bindings-storage-queue.md#trigger---message-metadata). Verifique a documentação nos [gatilhos](functions-triggers-bindings.md) com os quais você está trabalhando para ver o que acontece nos metadados do gatilho.

## <a name="bindings"></a>Enlaces

No PowerShell, as [associações](functions-triggers-bindings.md) são configuradas e definidas no function. JSON de uma função. As funções interagem com associações de várias maneiras.

### <a name="reading-trigger-and-input-data"></a>Lendo dados de gatilho e de entrada

As associações de entrada e gatilho são lidas como parâmetros passados para sua função. As associações de entrada têm um `direction` definido como `in` em function. JSON. A propriedade `name` definida em `function.json` é o nome do parâmetro, no bloco de `param`. Como o PowerShell usa parâmetros nomeados para associação, a ordem dos parâmetros não importa. No entanto, é uma prática recomendada seguir a ordem das associações definidas no `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Gravando dados de saída

No functions, uma associação de saída tem um `direction` definido como `out` no function. JSON. Você pode gravar em uma associação de saída usando o cmdlet `Push-OutputBinding`, que está disponível para o tempo de execução do functions. Em todos os casos, a propriedade `name` da associação, conforme definido em `function.json`, corresponde ao parâmetro `Name` do cmdlet `Push-OutputBinding`.

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

`Push-OutputBinding` se comporta de maneira diferente com base no valor especificado para `-Name`:

* Quando o nome especificado não puder ser resolvido para uma associação de saída válida, um erro será gerado.

* Quando a associação de saída aceita uma coleção de valores, você pode chamar `Push-OutputBinding` repetidamente para enviar vários valores por push.

* Quando a associação de saída aceita apenas um valor singleton, chamar `Push-OutputBinding` uma segunda vez gera um erro.

#### <a name="push-outputbinding-syntax"></a>sintaxe de `Push-OutputBinding`

Veja a seguir os parâmetros válidos para chamar `Push-OutputBinding`:

| Nome | Tipo | propostas | Descrição |
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

#### <a name="push-outputbinding-example-http-responses"></a>Exemplo de push-OutputBinding: respostas HTTP

Um gatilho HTTP retorna uma resposta usando uma associação de saída chamada `response`. No exemplo a seguir, a associação de saída de `response` tem o valor de "saída #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Como a saída é para HTTP, que aceita apenas um valor singleton, um erro é gerado quando `Push-OutputBinding` é chamado uma segunda vez.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Para saídas que aceitam apenas valores singleton, você pode usar o parâmetro `-Clobber` para substituir o valor antigo em vez de tentar adicionar a uma coleção. O exemplo a seguir pressupõe que você já adicionou um valor. Usando `-Clobber`, a resposta do exemplo a seguir substitui o valor existente para retornar um valor de "saída #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Exemplo de push-OutputBinding: Associação de saída da fila

`Push-OutputBinding` é usado para enviar dados para associações de saída, como uma [Associação de saída do armazenamento de filas do Azure](functions-bindings-storage-queue.md#output). No exemplo a seguir, a mensagem gravada na fila tem um valor de "saída #1":

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

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` cmdlet

Você pode usar o cmdlet `Get-OutputBinding` para recuperar os valores definidos atualmente para suas associações de saída. Esse cmdlet recupera uma tabela de hash que contém os nomes das associações de saída com seus respectivos valores. 

Veja a seguir um exemplo de como usar `Get-OutputBinding` para retornar os valores de associação atuais:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` também contém um parâmetro chamado `-Name`, que pode ser usado para filtrar a associação retornada, como no exemplo a seguir:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Há suporte para caracteres curinga (*) em `Get-OutputBinding`.

## <a name="logging"></a>Registo

O registro em log nas funções do PowerShell funciona como log normal do PowerShell. Você pode usar os cmdlets de log para gravar em cada fluxo de saída. Cada cmdlet é mapeado para um nível de log usado pelas funções.

| Nível de log de funções | Cmdlet de registro em log |
| ------------- | -------------- |
| Erro | **`Write-Error`** |
| Aviso | **`Write-Warning`**  | 
| Informações | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informações | Grava no log do nível de _informações_ . |
| Depurar | **`Write-Debug`** |
| Rastreio | **`Write-Progress`** <br /> **`Write-Verbose`** |

Além desses cmdlets, qualquer coisa gravada no pipeline é redirecionada para o nível de log `Information` e exibido com a formatação padrão do PowerShell.

> [!IMPORTANT]
> O uso dos cmdlets `Write-Verbose` ou `Write-Debug` não é suficiente para ver o log detalhado e de nível de depuração. Você também deve configurar o limite de nível de log, que declara o nível de logs que você realmente importa. Para saber mais, confira [Configurar o nível de log do aplicativo de funções](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configurar o nível de log do aplicativo de funções

Azure Functions permite que você defina o nível de limite para facilitar o controle da maneira como as funções são gravadas nos logs. Para definir o limite para todos os rastreamentos gravados no console, use a propriedade `logging.logLevel.default` na[referência de host. JSON]do [arquivo`host.json`]. Essa configuração se aplica a todas as funções em seu aplicativo de funções.

O exemplo a seguir define o limite para habilitar o log detalhado para todas as funções, mas define o limite para habilitar o log de depuração para uma função chamada `MyFunction`:

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

Se você estiver executando o Aplicativo de funções localmente para desenvolvimento, os logs serão padrão para o sistema de arquivos. Para ver os logs no console, defina a variável de ambiente `AZURE_FUNCTIONS_ENVIRONMENT` como `Development` antes de iniciar a Aplicativo de funções.

## <a name="triggers-and-bindings-types"></a>Tipos de associações e gatilhos

Há vários gatilhos e associações disponíveis para você usar com seu aplicativo de funções. A lista completa de gatilhos e associações [pode ser encontrada aqui](functions-triggers-bindings.md#supported-bindings).

Todos os gatilhos e associações são representados no código como alguns tipos de dados reais:

* Tabela
* string
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

| Propriedade  | Descrição                                                    | Tipo                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Um objeto que contém o corpo da solicitação. `Body` é serializado no melhor tipo com base nos dados. Por exemplo, se os dados forem JSON, eles serão passados como uma tabela de hash. Se os dados forem uma cadeia de caracteres, eles serão passados como uma cadeia de caracteres. | objeto |
| **`Headers`** | Um dicionário que contém os cabeçalhos de solicitação.                | < De cadeia de caracteres, Cadeia de caracteres > de dicionário<sup>*</sup> |
| **`Method`** | O método HTTP da solicitação.                                | string                    |
| **`Params`**  | Um objeto que contém os parâmetros de roteamento da solicitação. | < De cadeia de caracteres, Cadeia de caracteres > de dicionário<sup>*</sup> |
| **`Query`** | Um objeto que contém os parâmetros de consulta.                  | < De cadeia de caracteres, Cadeia de caracteres > de dicionário<sup>*</sup> |
| **`Url`** | A URL da solicitação.                                        | string                    |

<sup>*</sup> Todas as chaves de `Dictionary<string,string>` não diferenciam maiúsculas de minúsculas.

#### <a name="response-object"></a>Objeto de resposta

O objeto de resposta que você deve enviar de volta é do tipo `HttpResponseContext`, que tem as seguintes propriedades:

| Propriedade      | Descrição                                                 | Tipo                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Um objeto que contém o corpo da resposta.           | objeto                    |
| **`ContentType`** | Uma pequena mão para definir o tipo de conteúdo para a resposta. | string                    |
| **`Headers`** | Um objeto que contém os cabeçalhos de resposta.               | Dicionário ou Hashtable   |
| **`StatusCode`**  | O código de status HTTP da resposta.                       | Cadeia de caracteres ou int             |

#### <a name="accessing-the-request-and-response"></a>Acessando a solicitação e a resposta

Ao trabalhar com gatilhos HTTP, você pode acessar a solicitação HTTP da mesma maneira que faria com qualquer outra associação de entrada. Está no bloco de `param`.

Use um objeto `HttpResponseContext` para retornar uma resposta, conforme mostrado a seguir:

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

Por exemplo, para ter dados do armazenamento de BLOBs fornecidos como uma cadeia de caracteres, adicione a seguinte conversão de tipo ao meu bloco de `param`:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Perfil do PowerShell

No PowerShell, há o conceito de um perfil do PowerShell. Se você não estiver familiarizado com os perfis do PowerShell, consulte [about Profiles](/powershell/module/microsoft.powershell.core/about/about_profiles).

Em funções do PowerShell, o script de perfil é executado quando o aplicativo de funções é iniciado. Os aplicativos de funções começam quando são implantados pela primeira vez e depois ficam ociosos ([inicialização a frio](#cold-start)).

Quando você cria um aplicativo de funções usando ferramentas, como Visual Studio Code e Azure Functions Core Tools, um `profile.ps1` padrão é criado para você. O perfil padrão é mantido [no repositório GitHub das ferramentas principais](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contém:

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

O Functions permite aproveitar a [Galeria do PowerShell](https://www.powershellgallery.com) para gerenciar dependências. Com o gerenciamento de dependência habilitado, o arquivo Requirements. psd1 é usado para baixar automaticamente os módulos necessários. Você habilita esse comportamento definindo a propriedade `managedDependency` como `true` na raiz do [arquivo host. JSON](functions-host-json.md), como no exemplo a seguir:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Quando você cria um novo projeto de funções do PowerShell, o gerenciamento de dependência é habilitado por padrão, com o [módulo de`Az`](/powershell/azure/new-azureps-module-az) do Azure incluído. O número máximo de módulos com suporte no momento é 10. A sintaxe com suporte é _`MajorNumber`_ `.*` ou versão exata do módulo, conforme mostrado nos seguintes requisitos. psd1 exemplo:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Quando você atualiza o arquivo Requirements. psd1, os módulos atualizados são instalados após uma reinicialização.

> [!NOTE]
> As dependências gerenciadas exigem acesso ao www.powershellgallery.com para baixar módulos. Ao executar localmente, verifique se o tempo de execução pode acessar essa URL adicionando as regras de firewall necessárias. 

As configurações de aplicativo a seguir podem ser usadas para alterar a forma como as dependências gerenciadas são baixadas e instaladas. A atualização do aplicativo é iniciada no `MDMaxBackgroundUpgradePeriod`e o processo de atualização é concluído em aproximadamente o `MDNewSnapshotCheckPeriod`.

| Aplicativo de funções configuração              | Valor predefinido             | Descrição                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 dias)     | Cada processo de trabalho do PowerShell inicia a verificação de atualizações de módulo no Galeria do PowerShell no início do processo e a cada `MDMaxBackgroundUpgradePeriod` depois disso. Quando uma nova versão de módulo está disponível no Galeria do PowerShell, ela é instalada no sistema de arquivos e disponibilizada para trabalhadores do PowerShell. Diminuir esse valor permite que seu aplicativo de funções obtenha versões mais recentes do módulo mais cedo, mas também aumenta o uso de recursos do aplicativo (e/s de rede, CPU, armazenamento). Aumentar esse valor diminui o uso de recursos do aplicativo, mas também pode atrasar a entrega de novas versões de módulo ao seu aplicativo. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 hora)       | Depois que novas versões de módulo são instaladas no sistema de arquivos, todos os processos de trabalho do PowerShell devem ser reiniciados. Reiniciar os trabalhadores do PowerShell afeta a disponibilidade do aplicativo, pois ele pode interromper a execução da função atual. Até que todos os processos de trabalho do PowerShell sejam reiniciados, as invocações de função podem usar as versões de módulo antiga ou nova. A reinicialização de todos os trabalhadores do PowerShell é concluída dentro do `MDNewSnapshotCheckPeriod`. Aumentar esse valor diminui a frequência de interrupções, mas também pode aumentar o período de tempo em que as invocações de função usam as versões de módulo antigo ou novo de forma não determinística. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 dia)     | Para evitar atualizações excessivas de módulo em reinicializações de trabalhador frequentes, a verificação de atualizações de módulo não será executada quando algum trabalho já tiver iniciado essa verificação na última `MDMinBackgroundUpgradePeriod`. |

Aproveitar seus próprios módulos personalizados é um pouco diferente de como você faria normalmente.

No computador local, o módulo é instalado em uma das pastas disponíveis globalmente em seu `$env:PSModulePath`. Ao executar no Azure, você não tem acesso aos módulos instalados em seu computador. Isso significa que o `$env:PSModulePath` para um aplicativo de funções do PowerShell difere de `$env:PSModulePath` em um script do PowerShell regular.

No functions, `PSModulePath` contém dois caminhos:

* Uma pasta `Modules` que existe na raiz do seu aplicativo de funções.
* Um caminho para uma pasta de `Modules` que é controlada pelo operador de linguagem do PowerShell.

### <a name="function-app-level-modules-folder"></a>Pasta de `Modules` no nível do aplicativo de funções

Para usar módulos personalizados, você pode posicionar os módulos nos quais suas funções dependem de uma pasta `Modules`. Nessa pasta, os módulos ficam automaticamente disponíveis para o tempo de execução do functions. Qualquer função no aplicativo de funções pode usar esses módulos. 

> [!NOTE]
> Os módulos especificados no arquivo Requirements. psd1 são baixados e incluídos automaticamente no caminho para que você não precise incluí-los na pasta modules. Eles são armazenados localmente na pasta `$env:LOCALAPPDATA/AzureFunctions` e na pasta `/data/ManagedDependencies` quando executados na nuvem.

Para aproveitar o recurso de módulo personalizado, crie uma pasta `Modules` na raiz do seu aplicativo de funções. Copie os módulos que você deseja usar em suas funções para esse local.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Com uma pasta `Modules`, seu aplicativo de funções deve ter a seguinte estrutura de pastas:

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

Quando você inicia seu aplicativo de funções, o operador de linguagem do PowerShell adiciona essa pasta `Modules` ao `$env:PSModulePath` para que você possa contar com o carregamento automático de módulo, exatamente como faria em um script do PowerShell regular.

### <a name="language-worker-level-modules-folder"></a>Pasta `Modules` nível de trabalho de idioma

Vários módulos são geralmente usados pelo operador de linguagem do PowerShell. Esses módulos são definidos na última posição de `PSModulePath`. 

A lista atual de módulos é a seguinte:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): módulo usado para trabalhar com arquivos mortos, como `.zip`, `.nupkg`e outros.
* **ThreadJob**: uma implementação baseada em thread das APIs de trabalho do PowerShell.

Por padrão, o Functions usa a versão mais recente desses módulos. Para usar uma versão de módulo específica, coloque essa versão específica na pasta `Modules` do seu aplicativo de funções.

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

Há um grande valor em simultaneidade com Azure PowerShell, já que algumas operações podem levar um tempo considerável. No entanto, você deve continuar com cautela. Se você suspeitar de que está enfrentando uma condição de corrida, defina a configuração de aplicativo PSWorkerInProcConcurrencyUpperBound como `1` e, em vez disso, use o [isolamento de nível de processo de trabalho de linguagem](functions-app-settings.md#functions_worker_process_count) para simultaneidade.

## <a name="configure-function-scriptfile"></a>Configurar `scriptFile` de função

Por padrão, uma função do PowerShell é executada a partir de `run.ps1`, um arquivo que compartilha o mesmo diretório pai que seu `function.json`correspondente.

A propriedade `scriptFile` na `function.json` pode ser usada para obter uma estrutura de pastas parecida com o exemplo a seguir:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Nesse caso, o `function.json` para `myFunction` inclui uma propriedade `scriptFile` referenciando o arquivo com a função exportada a ser executada.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Usar módulos do PowerShell Configurando um ponto de entrada

Este artigo mostrou as funções do PowerShell no arquivo de script padrão `run.ps1` gerado pelos modelos.
No entanto, você também pode incluir suas funções em módulos do PowerShell. Você pode referenciar seu código de função específico no módulo usando os campos `scriptFile` e `entryPoint` no arquivo de configuração function. JSON.

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

Neste exemplo, a configuração de `myFunction` inclui uma propriedade `scriptFile` que faz referência a `PSFunction.psm1`, que é um módulo do PowerShell em outra pasta.  A propriedade `entryPoint` faz referência à função `Invoke-PSTestFunc`, que é o ponto de entrada no módulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Com essa configuração, a `Invoke-PSTestFunc` é executada exatamente como uma `run.ps1`.

## <a name="considerations-for-powershell-functions"></a>Considerações para funções do PowerShell

Ao trabalhar com as funções do PowerShell, esteja ciente das considerações nas seções a seguir.

### <a name="cold-start"></a>Início frio

Ao desenvolver Azure Functions no [modelo de hospedagem sem servidor](functions-scale.md#consumption-plan), inícios frios são uma realidade. *Início frio* refere-se ao período de tempo que leva para seu aplicativo de funções iniciar a execução para processar uma solicitação. A inicialização a frio acontece com mais frequência no plano de consumo porque seu aplicativo de funções é desligado durante períodos de inatividade.

### <a name="bundle-modules-instead-of-using-install-module"></a>Agrupar módulos em vez de usar `Install-Module`

O script é executado em cada invocação. Evite usar `Install-Module` em seu script. Em vez disso, use `Save-Module` antes da publicação para que sua função não precise perder tempo baixando o módulo. Se a frio for iniciada, afetando suas funções, considere implantar seu aplicativo de funções em um [plano do serviço de aplicativo](functions-scale.md#app-service-plan) definido como *Always on* ou em um [plano Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Azure Functions gatilhos e associações](functions-triggers-bindings.md)

[referência de host. JSON]: functions-host-json.md
