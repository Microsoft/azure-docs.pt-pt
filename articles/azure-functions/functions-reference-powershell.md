---
title: Referência do desenvolvedor powerShell para funções Azure
description: Compreenda como desenvolver funções utilizando o PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: 1da4154530f823d391aea779011a34a35edfd070
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071164"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guia de desenvolvedores powershell de funções Azure Functions

Este artigo fornece detalhes sobre como escreve funções Azure usando PowerShell.

Uma função PowerShell Azure (função) é representada como um script PowerShell que executa quando ativado. Cada script de função tem um ficheiro relacionado `function.json` que define como a função se comporta, como como é desencadeada e os seus parâmetros de entrada e saída. Para saber mais, consulte os [Gatilhos e artigo de vinculação](functions-triggers-bindings.md). 

À semelhança de outros tipos de funções, as funções de script PowerShell assumem parâmetros que correspondem aos nomes de todas as ligações de entrada definidas no `function.json` ficheiro. `TriggerMetadata`Também é passado um parâmetro que contém informações adicionais sobre o gatilho que iniciou a função.

Este artigo pressupõe que já leu a referência do [programador Azure Functions](functions-reference.md). Também deve ter concluído o arranque rápido de [Funções para PowerShell](./functions-create-first-function-vs-code.md?pivots=programming-language-powershell) para criar a sua primeira função PowerShell.

## <a name="folder-structure"></a>Estrutura de pasta

A estrutura de pasta necessária para um projeto PowerShell parece ser a seguinte. Este padrão pode ser alterado. Para mais informações, consulte a secção [scriptFile](#configure-function-scriptfile) abaixo.

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

Na origem do projeto, há um ficheiro partilhado [`host.json`](functions-host-json.md) que pode ser usado para configurar a aplicação de função. Cada função tem uma pasta com o seu próprio ficheiro de código (.ps1) e ficheiro de configuração de ligação `function.json` (). O nome do function.jsno diretório dos pais do ficheiro é sempre o nome da sua função.

Certas ligações requerem a presença de um `extensions.csproj` ficheiro. As extensões de encadernação, necessárias na [versão 2.x e versões posteriores](functions-versions.md) do tempo de execução das Funções, são definidas no `extensions.csproj` ficheiro, com os ficheiros reais da biblioteca na `bin` pasta. Ao desenvolver-se localmente, deve [registar extensões vinculativas](functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal Azure, este registo é feito para si.

Nas Aplicações de Função PowerShell, pode opcionalmente ter um `profile.ps1` que funciona quando uma aplicação de função começa a funcionar (caso contrário, sabe como um começo a *[frio](#cold-start)*. Para obter mais informações, consulte [o perfil PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definindo um script PowerShell como uma função

Por predefinição, o tempo de execução de Funções procura a sua função `run.ps1` em, onde `run.ps1` partilha o mesmo directório-mãe que o correspondente `function.json` .

O seu guião é aprovado uma série de argumentos sobre execução. Para lidar com estes parâmetros, adicione um `param` bloco ao topo do seu script como no seguinte exemplo:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parâmetro TriggerMetadata

O `TriggerMetadata` parâmetro é usado para fornecer informações adicionais sobre o gatilho. Os metadados adicionais variam de ligação a encadernação, mas todos contêm uma `sys` propriedade que contém os seguintes dados:

```powershell
$TriggerMetadata.sys
```

| Propriedade   | Descrição                                     | Tipo     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Quando, na UTC, a função foi desencadeada        | DateTime |
| Nome metóddio | O nome da Função que foi desencadeada     | string   |
| Rio RandGuid   | um guia único para esta execução da função | string   |

Cada tipo de gatilho tem um conjunto diferente de metadados. Por exemplo, o `$TriggerMetadata` for contém o , , , , entre `QueueTrigger` `InsertionTime` `Id` `DequeueCount` outras coisas. Para obter mais informações sobre os metadados do gatilho da fila, aceda à [documentação oficial para os gatilhos da fila](functions-bindings-storage-queue-trigger.md#message-metadata). Verifique a documentação dos [gatilhos](functions-triggers-bindings.md) com que está a trabalhar para ver o que vem dentro dos metadados do gatilho.

## <a name="bindings"></a>Enlaces

No PowerShell, [as ligações](functions-triggers-bindings.md) são configuradas e definidas no function.jsde uma função. As funções interagem com encadernações de várias maneiras.

### <a name="reading-trigger-and-input-data"></a>Ler dados de gatilho e entrada

As ligações do gatilho e da entrada são lidas à medida que os parâmetros passam para a sua função. As encadernações de entrada têm um `direction` conjunto `in` de function.js. A `name` propriedade definida é o nome do `function.json` parâmetro, no `param` bloco. Uma vez que o PowerShell usa parâmetros nomeados para a ligação, a ordem dos parâmetros não importa. No entanto, é uma boa prática seguir a ordem das encadernações definidas no `function.json` .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Escrever dados de saída

Em Funções, uma ligação de saída tem um `direction` conjunto `out` no function.jsligado. Pode escrever para uma ligação de saída utilizando o `Push-OutputBinding` cmdlet, que está disponível para o tempo de funcionamento das Funções. Em todos os casos, a `name` propriedade da ligação tal como definida `function.json` corresponde ao parâmetro do `Name` `Push-OutputBinding` cmdlet.

O seguinte mostra como ligar `Push-OutputBinding` no seu script de função:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Também pode passar num valor para uma ligação específica através do oleoduto.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` comporta-se de forma diferente com base no valor especificado `-Name` para:

* Quando o nome especificado não puder ser resolvido para uma ligação de saída válida, então um erro é lançado.

* Quando a ligação de saída aceita uma coleção de valores, pode ligar `Push-OutputBinding` repetidamente para empurrar vários valores.

* Quando a ligação de saída apenas aceita um valor singleton, chamar `Push-OutputBinding` uma segunda vez levanta um erro.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` sintaxe

Seguem-se os seguintes parâmetros válidos para a `Push-OutputBinding` chamada:

| Nome | Tipo | Posição | Descrição |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Cadeia | 1 | O nome da vinculação de saída que pretende definir. |
| **`-Value`** | Objeto | 2 | O valor da vinculação de saída que pretende definir, que é aceite a partir do pipeline ByValue. |
| **`-Clobber`** | ParâmetroOpcional | Nomeado | (Opcional) Quando especificado, força o valor a ser definido para uma ligação de saída especificada. | 

São também suportados os seguintes parâmetros comuns: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Para mais informações, consulte [Os CommonParameters.](https://go.microsoft.com/fwlink/?LinkID=113216)

#### <a name="push-outputbinding-example-http-responses"></a>Push-OutputBinding exemplo: RESPOSTAS HTTP

Um gatilho HTTP devolve uma resposta utilizando uma ligação de saída denominada `response` . No exemplo seguinte, a vinculação de saída `response` tem o valor de "#1 de produção":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Como a saída é para HTTP, que aceita apenas um valor singleton, um erro é lançado quando `Push-OutputBinding` é chamado uma segunda vez.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Para saídas que apenas aceitam valores singleton, pode usar o `-Clobber` parâmetro para sobrepor o valor antigo em vez de tentar adicionar a uma coleção. O exemplo a seguir pressupõe que já adicionou um valor. Ao `-Clobber` utilizar, a resposta do exemplo a seguir sobrepõe-se ao valor existente para devolver um valor de "#3 de produção":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Push-OutputBinding exemplo: Encadernação de saída de fila

`Push-OutputBinding` é utilizado para enviar dados para encadernações de saída, como uma [ligação de saída de armazenamento da Fila Azure](functions-bindings-storage-queue-output.md). No exemplo seguinte, a mensagem escrita para a fila tem um valor de "output #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

A ligação de saída para uma fila de armazenamento aceita vários valores de saída. Neste caso, chamando o exemplo seguinte após o primeiro escrever para a fila uma lista com dois itens: "output #1" e "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

O exemplo a seguir, quando chamado após os dois anteriores, adiciona mais dois valores à coleção de saída:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Quando escrita na fila, a mensagem contém estes quatro valores: "output #1", "output #2", "output #3" e "output #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` cmdlet

Pode utilizar o `Get-OutputBinding` cmdlet para recuperar os valores atualmente definidos para as suas ligações de saída. Este cmdlet recupera um haxixe que contém os nomes das ligações de saída com os respetivos valores. 

Segue-se um exemplo de utilização `Get-OutputBinding` para devolver os valores de encadernação atuais:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` contém também um parâmetro chamado `-Name` , que pode ser usado para filtrar a encadernação devolvida, como no exemplo seguinte:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Wildcards (*) são suportados em `Get-OutputBinding` .

## <a name="logging"></a>Registo

O registo nas funções PowerShell funciona como o registo regular do PowerShell. Pode utilizar os cmdlets de registo para escrever em cada fluxo de saída. Cada cmdlet mapeia para um nível de registo utilizado pelas Funções.

| Nível de registo de funções | Cmdlet de registo |
| ------------- | -------------- |
| Erro | **`Write-Error`** |
| Aviso | **`Write-Warning`**  | 
| Informações | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informações | Escreve para o registo de nível _de informação._ |
| Depurar | **`Write-Debug`** |
| Rastreio | **`Write-Progress`** <br /> **`Write-Verbose`** |

Além destes cmdlets, qualquer coisa escrita para o oleoduto é redirecionada para o `Information` nível de registo e exibida com a formatação padrão powerShell.

> [!IMPORTANT]
> A utilização dos `Write-Verbose` `Write-Debug` cmdlets ou cmdlets não é suficiente para verbose e depurar o nível de registo. Também deve configurar o limiar do nível de registo, que declara o nível de registos com que realmente se preocupa. Para saber mais, consulte [configurar o nível de registo de aplicações de função](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configure o nível de registo de aplicações de função

As Funções Azure permitem definir o nível de limiar para facilitar o controlo da forma como as Funções escrevem nos registos. Para fixar o limiar para todos os vestígios escritos na consola, utilize a `logging.logLevel.default` propriedade no [ `host.json` ficheiro] [host.jsa referência]. Esta definição aplica-se a todas as funções da sua aplicação de funções.

O exemplo a seguir define o limiar para permitir a registo verboso para todas as funções, mas define o limiar para permitir a registo de depurar para uma função denominada `MyFunction` :

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

Para mais informações, consulte [host.jsa referência.]

### <a name="viewing-the-logs"></a>Visualização dos registos

Se a sua App de Função estiver a funcionar no Azure, pode utilizar o Application Insights para monitorizá-la. Leia [a monitorização das Funções Azure](functions-monitoring.md) para saber mais sobre registos de funções de visualização e consulta.

Se estiver a executar a sua App de Função localmente para desenvolvimento, regista o sistema de ficheiros por defeito. Para ver os registos na consola, desaprote a variável ambiente `AZURE_FUNCTIONS_ENVIRONMENT` para antes de iniciar a App de `Development` Função.

## <a name="triggers-and-bindings-types"></a>Tipos de gatilhos e encadernações

Existem vários gatilhos e encadernações disponíveis para utilizar com a sua aplicação de função. A lista completa de gatilhos e encadernações [pode ser consultada aqui.](functions-triggers-bindings.md#supported-bindings)

Todos os gatilhos e encadernações são representados em código como alguns tipos reais de dados:

* Haxixe
* string
* byte[]
* int
* double
* HttpRequestContexto
* HttpResponseContext

Os primeiros cinco tipos desta lista são os tipos standard .NET. Os dois últimos são utilizados apenas pelo [gatilho HttpTrigger](#http-triggers-and-bindings).

Cada parâmetro de ligação nas suas funções deve ser um destes tipos.

### <a name="http-triggers-and-bindings"></a>Disparadores e encadernações HTTP

Os gatilhos HTTP e Webhook e as ligações de saída HTTP utilizam objetos de pedido e resposta para representar as mensagens HTTP.

#### <a name="request-object"></a>Objeto de pedido

O objeto de pedido que é passado para o script é do `HttpRequestContext` tipo, que tem as seguintes propriedades:

| Propriedade  | Descrição                                                    | Tipo                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Um objeto que contém o corpo do pedido. `Body` é serializado para o melhor tipo com base nos dados. Por exemplo, se os dados são JSON, é passado como um haxixe. Se os dados são uma corda, é passado como uma corda. | objeto |
| **`Headers`** | Um dicionário que contém os cabeçalhos de pedido.                | Cadeia de<do dicionário,>de cordas <sup>*</sup> |
| **`Method`** | O método HTTP do pedido.                                | string                    |
| **`Params`**  | Um objeto que contém os parâmetros de encaminhamento do pedido. | Cadeia de<do dicionário,>de cordas <sup>*</sup> |
| **`Query`** | Um objeto que contém os parâmetros de consulta.                  | Cadeia de<do dicionário,>de cordas <sup>*</sup> |
| **`Url`** | A URL do pedido.                                        | string                    |

<sup>*</sup> Todas `Dictionary<string,string>` as chaves são insensíveis.

#### <a name="response-object"></a>Objeto de resposta

O objeto de resposta que deve enviar de volta é do `HttpResponseContext` tipo, que tem as seguintes propriedades:

| Propriedade      | Descrição                                                 | Tipo                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Um objeto que contém o corpo da resposta.           | objeto                    |
| **`ContentType`** | Uma mão curta para definir o tipo de conteúdo para a resposta. | string                    |
| **`Headers`** | Um objeto que contém os cabeçalhos de resposta.               | Dicionário ou Hashtable   |
| **`StatusCode`**  | O código de estado HTTP da resposta.                       | corda ou int             |

#### <a name="accessing-the-request-and-response"></a>Acede ao pedido e resposta

Quando trabalha com os gatilhos HTTP, pode aceder ao pedido HTTP da mesma forma que faria com qualquer outra ligação de entrada. Está no `param` quarteirão.

Utilize um `HttpResponseContext` objeto para devolver uma resposta, como mostra o seguinte:

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

O resultado da invocação desta função seria:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Fundição de tipo para gatilhos e encadernações

Para certas encadernações como a encadernação do blob, é possível especificar o tipo de parâmetro.

Por exemplo, para ter dados do armazenamento Blob fornecidos como uma cadeia, adicione o seguinte tipo de molde ao meu `param` bloco:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Perfil PowerShell

No PowerShell, há o conceito de um perfil PowerShell. Se não está familiarizado com os perfis powerShell, consulte [sobre perfis.](/powershell/module/microsoft.powershell.core/about/about_profiles)

Nas Funções PowerShell, o script de perfil é executado uma vez por exemplo de trabalhador powerShell na aplicação quando implantado pela primeira vez e depois de ser idled[(arranque a frio](#cold-start). Quando a concurrency é ativada definindo o valor [PSWorkerInProcConcurrencyUpper,](#concurrency) o script de perfil é executado para cada espaço de execução criado.

Quando cria uma aplicação de função utilizando ferramentas, como o Código do Estúdio Visual e as Ferramentas Core de Funções Azure, é criado um padrão `profile.ps1` para si. O perfil predefinido é mantido [no repositório Core Tools GitHub](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contém:

* Autenticação automática de MSI para Azure.
* A capacidade de ligar os pseudónimos Azure `AzureRM` PowerShell PowerShell, se quiser.

## <a name="powershell-versions"></a>Versões PowerShell

A tabela a seguir mostra as versões PowerShell disponíveis para cada versão principal do tempo de execução das Funções, e a versão .NET necessária:

| Versão de funções | Versão PowerShell                               | Versão .NET  | 
|-------------------|--------------------------------------------------|---------------|
| 3.x (recomendado) | PowerShell 7 (recomendado)<br/>PowerShell Core 6 | .NET Core 3.1<br/>.NET Core 2.1 |
| 2.x               | PowerShell Core 6                                | .NET Core 2.2 |

Pode ver a versão atual imprimindo `$PSVersionTable` a partir de qualquer função.

### <a name="running-local-on-a-specific-version"></a>Executando local em uma versão específica

Ao executar localmente, o tempo de execução das funções Azure está a predefinição da utilização do PowerShell Core 6. Em vez disso, para utilizar o PowerShell 7 quando estiver a funcionar localmente, é necessário adicionar a definição `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"` à matriz no local.setting.jsficheiro na raiz do `Values` projeto. Ao correr localmente no PowerShell 7, o seu local.settings.jsno ficheiro parece o seguinte exemplo: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>Alterar a versão PowerShell

A sua aplicação de função deve estar a funcionar na versão 3.x para poder fazer o upgrade do PowerShell Core 6 para o PowerShell 7. Para aprender a fazê-lo, consulte [ver e atualizar a versão atual do tempo de execução](set-runtime-version.md#view-and-update-the-current-runtime-version).

Utilize os seguintes passos para alterar a versão PowerShell utilizada pela sua aplicação de função. Pode fazê-lo no portal Azure ou utilizando o PowerShell.

# <a name="portal"></a>[Portal](#tab/portal)

1. No [portal Azure,](https://portal.azure.com)navegue pela sua aplicação de função.

1. Em **Definições**, escolha **Configuração**. No separador **Definições Gerais,** localize a **versão PowerShell**. 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="Escolha a versão PowerShell utilizada pela aplicação de função"::: 

1. Escolha a **versão powerShell Core** desejada e selecione **Save**. Quando avisado sobre o reinício pendente escolha **Continue**. A aplicação de função reinicia na versão escolhida do PowerShell. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Execute o seguinte script para alterar a versão PowerShell: 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

Substitua `<SUBSCRIPTION_ID>` , `<RESOURCE_GROUP>` e pelo `<FUNCTION_APP>` ID da sua subscrição Azure, o nome do seu grupo de recursos e app de função, respectivamente.  Além disso, `<VERSION>` substitua-o por um `~6` ou. `~7` Pode verificar o valor atualizado da `powerShellVersion` configuração da `Properties` tabela de haxixe devolvida. 

---

A aplicação de função reinicia após a alteração ser feita para a configuração.

## <a name="dependency-management"></a>Gestão de dependências

As funções permitem-lhe alavancar a [galeria PowerShell](https://www.powershellgallery.com) para gerir as dependências. Com a gestão da dependência ativada, o ficheiro requirements.psd1 é utilizado para descarregar automaticamente os módulos necessários. Você ativa este comportamento colocando a `managedDependency` propriedade na raiz dohost.jsno `true` [ arquivo](functions-host-json.md), como no exemplo seguinte:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Quando cria um novo projeto de funções PowerShell, a gestão da dependência é ativada por padrão, com o [ `Az` módulo](/powershell/azure/new-azureps-module-az) Azure incluído. O número máximo de módulos atualmente suportados é de 10. A sintaxe suportada é _`MajorNumber`_ `.*` ou a versão exata do módulo, tal como mostrado no seguinte exemplo requirements.psd1:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Quando atualiza o ficheiro requirements.psd1, os módulos atualizados são instalados após um reinício.

> [!NOTE]
> As dependências geridas requerem acesso a www.powershellgallery.com para descarregar módulos. Ao correr localmente, certifique-se de que o tempo de execução pode aceder a este URL adicionando as regras de firewall necessárias.

> [!NOTE]
> Atualmente, as dependências geridas não suportam módulos que exijam que o utilizador aceite uma licença, quer aceitando a licença interativamente, quer fornecendo `-AcceptLicense` o interruptor ao invocar `Install-Module` .

As seguintes definições de aplicação podem ser usadas para alterar a forma como as dependências geridas são descarregadas e instaladas. A atualização da sua aplicação começa dentro `MDMaxBackgroundUpgradePeriod` de , e o processo de atualização completa dentro de aproximadamente o `MDNewSnapshotCheckPeriod` .

| Definição de aplicativo de função              | Valor predefinido             | Descrição                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 dias)     | Cada processo de trabalhador PowerShell inicia a verificação de atualizações de módulos na PowerShell Gallery no início do processo e em todas as `MDMaxBackgroundUpgradePeriod` seguintes. Quando uma nova versão do módulo está disponível na PowerShell Gallery, é instalada no sistema de ficheiros e disponibilizada aos trabalhadores do PowerShell. Diminuir este valor permite que a sua aplicação de função obtenha versões de módulos mais recentes mais cedo, mas também aumenta o uso de recursos da aplicação (rede I/O, CPU, armazenamento). O aumento deste valor diminui o uso de recursos da app, mas também pode atrasar a entrega de novas versões de módulos à sua app. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 hora)       | Depois de serem instaladas novas versões de módulos no sistema de ficheiros, todos os processos de trabalhadores powerShell devem ser reiniciados. Reiniciar os trabalhadores do PowerShell afeta a disponibilidade da sua aplicação, uma vez que pode interromper a execução da função atual. Até que todos os processos do trabalhador powerShell sejam reiniciados, as invocações de função podem usar as versões antigas ou novas do módulo. Reiniciar todos os trabalhadores da PowerShell completos dentro `MDNewSnapshotCheckPeriod` de . Aumentar este valor diminui a frequência de interrupções, mas também pode aumentar o período de tempo em que as invocações de função usam as versões antigas ou novas do módulo não deterministicamente. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 dia)     | Para evitar atualizações excessivas de módulos em recomeçamento frequente do Trabalhador, a verificação de atualizações de módulos não é realizada quando qualquer trabalhador já iniciou esse check no último `MDMinBackgroundUpgradePeriod` . |

Aproveitar os seus próprios módulos personalizados é um pouco diferente de como o faria normalmente.

No seu computador local, o módulo é instalado numa das pastas globalmente disponíveis na sua `$env:PSModulePath` . Ao correr em Azure, não tem acesso aos módulos instalados na sua máquina. Isto significa que a `$env:PSModulePath` aplicação de função PowerShell difere de um script regular do `$env:PSModulePath` PowerShell.

Em Funções, `PSModulePath` contém dois caminhos:

* Uma `Modules` pasta que existe na raiz da sua aplicação de função.
* Um caminho para uma `Modules` pasta que é controlada pelo trabalhador linguístico PowerShell.


### <a name="function-app-level-modules-folder"></a>Pasta de nível de aplicação de função `Modules`

Para utilizar módulos personalizados, pode colocar módulos dos quais as suas funções dependem numa `Modules` pasta. A partir desta pasta, os módulos estão automaticamente disponíveis para o tempo de funcionamento das funções. Qualquer função na aplicação de função pode utilizar estes módulos. 

> [!NOTE]
> Os módulos especificados no ficheiro requirements.psd1 são automaticamente descarregados e incluídos no caminho para que não seja necessário incluí-los na pasta dos módulos. Estes são armazenados localmente na `$env:LOCALAPPDATA/AzureFunctions` pasta e na pasta quando `/data/ManagedDependencies` executados na nuvem.

Para tirar partido da funcionalidade do módulo personalizado, crie uma `Modules` pasta na raiz da sua aplicação de função. Copie os módulos que pretende utilizar nas suas funções para este local.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Com uma `Modules` pasta, a sua aplicação de função deve ter a seguinte estrutura de pasta:

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

Quando inicia a sua aplicação de função, o trabalhador linguístico PowerShell adiciona esta `Modules` pasta ao modo de poder confiar no carregamento automático do `$env:PSModulePath` módulo, tal como faria num script regular do PowerShell.

### <a name="language-worker-level-modules-folder"></a>Pasta de nível de trabalhador de `Modules` língua

Vários módulos são comumente usados pelo trabalhador da língua PowerShell. Estes módulos são definidos na última posição de `PSModulePath` . 

A lista atual de módulos é a seguinte:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): módulo utilizado para trabalhar com arquivos, `.zip` `.nupkg` como, e outros.
* **ThreadJob**: Uma implementação baseada em linha das APIs de trabalho powerShell.

Por predefinição, as Funções utilizam a versão mais recente destes módulos. Para utilizar uma versão específica do módulo, coloque essa versão específica na `Modules` pasta da sua aplicação de função.

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as configurações da aplicação](functions-app-settings.md), como as cadeias de ligação de serviço, são expostas como variáveis ambientais durante a execução. Pode aceder a estas definições utilizando `$env:NAME_OF_ENV_VAR` , como mostra o seguinte exemplo:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao correr localmente, as definições de aplicações são lidas a partir do [local.settings.jsno](functions-run-local.md#local-settings-file) ficheiro do projeto.

## <a name="concurrency"></a>Simultaneidade

Por predefinição, o tempo de funcionamento do PowerShell funcionamento das funções só pode processar uma invocação de uma função de cada vez. Todavia, este nível de concordância pode não ser suficiente nas seguintes situações:

* Quando se está a tentar lidar com um grande número de invocações ao mesmo tempo.
* Quando tiver funções que invoquem outras funções dentro da mesma aplicação de função.

Existem alguns modelos de concordância que você poderia explorar dependendo do tipo de carga de trabalho:

* Aumentar ```FUNCTIONS_WORKER_PROCESS_COUNT``` . Isto permite lidar com invocações de funções em múltiplos processos dentro do mesmo caso, que introduz certos CPU e sobrecarga de memória. Em geral, as funções ligadas à I/O não sofrerão com esta sobrecarga. Para as funções ligadas à CPU, o impacto pode ser significativo.

* Aumente o ```PSWorkerInProcConcurrencyUpperBound``` valor de definição da aplicação. Isto permite criar múltiplos espaços de funcionamento dentro do mesmo processo, o que reduz significativamente a CPU e a memória.

Você define estas variáveis ambientais nas definições de [aplicação](functions-app-settings.md) da sua aplicação de função.

Dependendo do seu caso de utilização, as funções duráveis podem melhorar significativamente a escalabilidade. Para saber mais, consulte [os padrões de aplicação de Funções Duráveis](/azure/azure-functions/durable/durable-functions-overview?tabs=powershell#application-patterns).

>[!NOTE]
> Poderá receber "pedidos que estão a ser solicitados devido a nenhum espaço de funcionamento disponível", por favor tenha em atenção que isto não é um erro. A mensagem diz-lhe que os pedidos estão a ser preenchidos e que serão tratados quando os pedidos anteriores estiverem concluídos.

### <a name="considerations-for-using-concurrency"></a>Considerações para a utilização da concordância

PowerShell é uma única linguagem de script _roscada_ por defeito. No entanto, a concordância pode ser adicionada utilizando vários espaços de funcionamento PowerShell no mesmo processo. A quantidade de espaço de execução criado corresponderá à definição de ```PSWorkerInProcConcurrencyUpperBound``` aplicação. A produção será impactada pela quantidade de CPU e memória disponível no plano selecionado.

O Azure PowerShell utiliza alguns contextos _e estados de nível de processo_ para ajudar a salvá-lo do excesso de dactilografia. No entanto, se ligar a sua aplicação de função e invocar ações que mudam de estado, pode acabar com as condições de corrida. Estas condições de corrida são difíceis de depurar porque uma invocação depende de um determinado estado e a outra invocação mudou o estado.

Há um imenso valor em concordância com a Azure PowerShell, uma vez que algumas operações podem demorar um tempo considerável. No entanto, deve proceder com cautela. Se suspeitar que está a sentir uma condição de raça, defina a definição da aplicação PSWorkerInProcConcurrencyUpperBound para `1` e, em vez disso, use [o isolamento do nível de processo do trabalhador linguístico](functions-app-settings.md#functions_worker_process_count) para a conuncy.

## <a name="configure-function-scriptfile"></a>Função de configure `scriptFile`

Por predefinição, uma função PowerShell é executada a partir `run.ps1` de, um ficheiro que partilha o mesmo directório-mãe que o correspondente `function.json` .

A `scriptFile` propriedade na pode ser usada para obter uma estrutura de pasta que se parece com o seguinte `function.json` exemplo:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Neste caso, o `function.json` for `myFunction` inclui um `scriptFile` imóvel que refere o ficheiro com a função exportada a executar.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Utilize módulos PowerShell configurando um ponto de entrada

Este artigo mostrou funções PowerShell no ficheiro de script padrão `run.ps1` gerado pelos modelos.
No entanto, também pode incluir as suas funções em módulos PowerShell. Pode fazer referência ao seu código de função específico no módulo utilizando o `scriptFile` e os campos no ficheiro de `entryPoint` configuração function.jsligados.

Neste caso, `entryPoint` é o nome de uma função ou cmdlet no módulo PowerShell referenciado em `scriptFile` .

Considere a seguinte estrutura de pasta:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Onde `PSFunction.psm1` contiver:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Neste exemplo, a configuração para `myFunction` inclui uma propriedade que faz `scriptFile` `PSFunction.psm1` referências , que é um módulo PowerShell noutra pasta.  A `entryPoint` propriedade refere a `Invoke-PSTestFunc` função, que é o ponto de entrada no módulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Com esta configuração, o `Invoke-PSTestFunc` é executado exatamente como um `run.ps1` faria.

## <a name="considerations-for-powershell-functions"></a>Considerações para funções PowerShell

Quando trabalhar com funções PowerShell, esteja ciente das considerações nas seguintes secções.

### <a name="cold-start"></a>Início a Frio

Ao desenvolver Funções Azure no [modelo de hospedagem sem servidor,](functions-scale.md#consumption-plan)o frio é uma realidade. *Início a frio* refere-se ao período de tempo que a sua aplicação de função leva a começar a funcionar para processar um pedido. O arranque a frio acontece com mais frequência no plano de Consumo porque a sua aplicação de função é desligada durante períodos de inatividade.

### <a name="bundle-modules-instead-of-using-install-module"></a>Módulos de pacote em vez de usar `Install-Module`

O teu guião é executado em todas as invocações. Evite usar `Install-Module` no seu script. Em vez disso, utilize `Save-Module` antes de publicar para que a sua função não tenha de perder tempo a descarregar o módulo. Se os arranques frios estiverem a afetar as suas funções, considere implementar a sua aplicação de função num [plano de Serviço de Aplicações](functions-scale.md#app-service-plan) definido para sempre *num* [plano Premium.](functions-scale.md#premium-plan)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Funções Azure dispara e encaderna](functions-triggers-bindings.md)

[Referência host.json]: functions-host-json.md
