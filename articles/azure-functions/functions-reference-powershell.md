---
title: Referência de desenvolvimento powerShell para funções Azure
description: Compreenda como desenvolver funções utilizando o PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485136"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guia de desenvolvimento de funções Azure PowerShell

Este artigo fornece detalhes sobre como escreve funções Azure usando powerShell.

Uma função PowerShell Azure (função) é representada como um script PowerShell que executa quando ativado. Cada script de função tem um ficheiro `function.json` relacionado que define como a função se comporta, como como como é desencadeada e os seus parâmetros de entrada e saída. Para saber mais, consulte os [Gatilhos e artigo vinculativo.](functions-triggers-bindings.md) 

Tal como outros tipos de funções, as funções de script PowerShell assumem parâmetros que correspondem aos nomes de todas as ligações de entrada definidas no ficheiro `function.json`. Um parâmetro `TriggerMetadata` também é passado que contém informações adicionais sobre o gatilho que iniciou a função.

Este artigo assume que já leu a referência do desenvolvedor de [Funções Azure](functions-reference.md). Também deve ter completado as [funções quickstart para a PowerShell](functions-create-first-function-powershell.md) criar a sua primeira função PowerShell.

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

Na raiz do projeto, há um ficheiro [`host.json`](functions-host-json.md) partilhado que pode ser usado para configurar a aplicação de função. Cada função tem uma pasta com o seu próprio ficheiro de código (.ps1) e ficheiro de configuração de ligação (`function.json`). O nome do directório-mãe do ficheiro função.json é sempre o nome da sua função.

Certas ligações requerem a presença de um ficheiro `extensions.csproj`. As extensões de encadernação, exigidas na [versão 2.x e versões posteriores](functions-versions.md) do tempo de execução das Funções, são definidas no ficheiro `extensions.csproj`, com os ficheiros da biblioteca reais na pasta `bin`. Ao desenvolver-se localmente, deve [registar extensões vinculativas](functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal Azure, este registo é feito para si.

Nas Aplicações de Função PowerShell, pode ter opcionalmente um `profile.ps1` que funciona quando uma aplicação de função começa a funcionar (de outra forma conhecido como um *[início frio](#cold-start)* . Para mais informações, consulte o [perfil PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definindo um script PowerShell como uma função

Por predefinição, o tempo de funcionamento das Funções procura a sua função em `run.ps1`, onde `run.ps1` partilha o mesmo directório-mãe que o seu `function.json`correspondente .

O seu guião foi aprovado em vários argumentos sobre a execução. Para lidar com estes parâmetros, adicione um bloco `param` ao topo do seu script como no seguinte exemplo:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parâmetro de Metadados de gatilho

O parâmetro `TriggerMetadata` é utilizado para fornecer informações adicionais sobre o gatilho. Os metadados adicionais variam de ligação a encadernação, mas todos eles contêm uma propriedade `sys` que contém os seguintes dados:

```powershell
$TriggerMetadata.sys
```

| Propriedade   | Descrição                                     | Tipo     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Quando, na UTC, a função foi desencadeada        | DateTime |
| Nome do método | O nome da Função que foi desencadeada     | Cadeia de caracteres   |
| RandGuid   | um guia único para esta execução da função | Cadeia de caracteres   |

Cada tipo de gatilho tem um conjunto diferente de metadados. Por exemplo, o `$TriggerMetadata` para `QueueTrigger` contém o `InsertionTime`, `Id`, `DequeueCount`, entre outras coisas. Para obter mais informações sobre os metadados do gatilho da fila, vá à [documentação oficial para os gatilhos](functions-bindings-storage-queue-trigger.md#message-metadata)da fila . Verifique a documentação dos [gatilhos](functions-triggers-bindings.md) com os quais está a trabalhar para ver o que entra nos metadados do gatilho.

## <a name="bindings"></a>Enlaces

No PowerShell, as [ligações](functions-triggers-bindings.md) são configuradas e definidas na função de uma função.json. As funções interagem com ligações de várias maneiras.

### <a name="reading-trigger-and-input-data"></a>Dados do gatilho de leitura e da entrada

As encadernações de gatilho e de entrada são lidas à medida que os parâmetros passam para a sua função. As encadernações de entrada têm um conjunto `direction` para `in` em função.json. A propriedade `name` definida em `function.json` é o nome do parâmetro, no bloco `param`. Uma vez que o PowerShell utiliza parâmetros nomeados para a ligação, a ordem dos parâmetros não importa. No entanto, é uma boa prática seguir a ordem das encadernações definidas na `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Escrever dados de saída

Em Funções, uma ligação de saída tem um conjunto `direction` para `out` na função.json. Pode escrever para uma ligação de saída utilizando o `Push-OutputBinding` cmdlet, que está disponível para o tempo de funcionamento das Funções. Em todos os casos, a `name` propriedade da encadernação tal como definida em `function.json` corresponde ao parâmetro `Name` do `Push-OutputBinding` cmdlet.

O seguinte mostra como chamar `Push-OutputBinding` no seu script de função:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Também pode passar em valor para uma ligação específica através do oleoduto.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` se comporta de forma diferente com base no valor especificado para `-Name`:

* Quando o nome especificado não puder ser resolvido a uma ligação de saída válida, então um erro é lançado.

* Quando a ligação de saída aceita uma coleção de valores, pode chamar `Push-OutputBinding` repetidamente para empurrar vários valores.

* Quando a ligação de saída só aceita um valor singleton, chamar `Push-OutputBinding` uma segunda vez levanta um erro.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` sintaxe

Os seguintes parâmetros são válidos para chamar `Push-OutputBinding`:

| Nome | Tipo | Posição | Descrição |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | O nome da ligação de saída que quer definir. |
| **`-Value`** | Objeto | 2 | O valor da ligação de saída que pretende definir, que é aceite a partir do pipeline ByValue. |
| **`-Clobber`** | Parâmetro do comutador | Nomeado | (Opcional) Quando especificado, força o valor a definir para uma ligação de saída especificada. | 

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

Para mais informações, consulte [sobre os Parâmetros Comuns](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Exemplo de ligação push-output: respostas HTTP

Um gatilho HTTP devolve uma resposta utilizando uma ligação de saída chamada `response`. No exemplo seguinte, a ligação de saída da `response` tem o valor de "#1 de saída":

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

Para saídas que só aceitam valores singleton, pode usar o parâmetro `-Clobber` para sobrepor-se ao valor antigo em vez de tentar adicionar a uma coleção. O exemplo que se segue pressupõe que já tenha acrescentado um valor. Utilizando `-Clobber`, a resposta do seguinte exemplo sobrepõe-se ao valor existente para devolver um valor de "#3 de saída":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Exemplo de ligação de saída de impulso: encadernação de saída de fila

`Push-OutputBinding` é utilizado para enviar dados para encadernações de saída, como uma [encadernação](functions-bindings-storage-queue-output.md)de saída de armazenamento de fila Azure. No exemplo seguinte, a mensagem escrita na fila tem um valor de "#1 de saída":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

A ligação de saída para uma fila de armazenamento aceita vários valores de saída. Neste caso, chamando o seguinte exemplo após o primeiro escrever à fila uma lista com dois itens: "#1 de saída" e "#2 de saída".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

O exemplo seguinte, quando chamado após os dois anteriores, adiciona mais dois valores à coleção de saída:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Quando escrita na fila, a mensagem contém estes quatro valores: "#1 de saída", "#2 de saída", "#3 de saída" e "#4 de saída".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` cmdlet

Pode utilizar o `Get-OutputBinding` cmdlet para recuperar os valores atualmente definidos para as suas encadernações de saída. Este cmdlet recupera um hashtable que contém os nomes das encadernações de saída com os respetivos valores. 

Segue-se um exemplo de utilização `Get-OutputBinding` para devolver os valores de ligação atuais:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` também contém um parâmetro chamado `-Name`, que pode ser usado para filtrar a encadernação devolvida, como no seguinte exemplo:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Os wildcards (*) são suportados em `Get-OutputBinding`.

## <a name="logging"></a>Registo

O registo em funções PowerShell funciona como registo regular da PowerShell. Pode utilizar os cmdlets de registo para escrever em cada fluxo de saída. Cada cmdlet mapeia para um nível de log utilizado pelas Funções.

| Funções nível de exploração | Login cmdlet |
| ------------- | -------------- |
| Erro | **`Write-Error`** |
| Aviso | **`Write-Warning`**  | 
| Informações | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informações | Escreve para o nível de _registo de informação._ |
| depurar | **`Write-Debug`** |
| Rastreio | **`Write-Progress`** <br /> **`Write-Verbose`** |

Além destes cmdlets, qualquer coisa escrita para o gasoduto é redirecionada para o nível de registo `Information` e exibida com a formatação padrão PowerShell.

> [!IMPORTANT]
> A utilização do `Write-Verbose` ou `Write-Debug` cmdlets não é suficiente para verbose e depuração do nível de extração. Também deve configurar o limiar de nível de registo, que declara o nível de registos com que realmente se preocupa. Para saber mais, consulte [Configurar o nível de registo da aplicação de função](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configure o nível de registo da aplicação de função

As Funções Azure permitem definir o nível de limiar para facilitar o controlo da forma como as Funções escrevem para os registos. Para definir o limiar para todos os vestígios escritos na consola, utilize a propriedade `logging.logLevel.default` na referência [`host.json`][host.json reference]. Esta definição aplica-se a todas as funções da sua aplicação de funções.

O exemplo seguinte estabelece o limiar para permitir o abate verbose para todas as funções, mas estabelece o limiar para permitir o abate de depuração para uma função denominada `MyFunction`:

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

Para mais informações, consulte [host.json reference].

### <a name="viewing-the-logs"></a>Visualização dos registos

Se a sua App de Funções estiver a funcionar em Azure, pode utilizar os Insights de Aplicação para monitorizá-la. Leia a monitorização das [Funções Azure](functions-monitoring.md) para saber mais sobre os registos de funções de visualização e consulta.

Se estiver a executar a sua App de Funções localmente para desenvolvimento, regista o padrão no sistema de ficheiros. Para ver os registos na consola, detete a variável ambiente `AZURE_FUNCTIONS_ENVIRONMENT` para `Development` antes de iniciar a App de Funções.

## <a name="triggers-and-bindings-types"></a>Tipos de gatilhos e encadernações

Existem vários gatilhos e encadernações disponíveis para utilizar com a sua aplicação de função. A lista completa de gatilhos e encadernações [pode ser consultada aqui.](functions-triggers-bindings.md#supported-bindings)

Todos os gatilhos e encadernações estão representados em código como alguns tipos de dados reais:

* Hashtable
* Cadeia de caracteres
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Os primeiros cinco tipos desta lista são os tipos padrão .NET. Os dois últimos são utilizados apenas pelo [gatilho HttpTrigger](#http-triggers-and-bindings).

Cada parâmetro de ligação nas suas funções deve ser um destes tipos.

### <a name="http-triggers-and-bindings"></a>HTTP gatilhos e encadernações

OS gatilhos HTTP e webhook e as encadernações de saída HTTP utilizam objetos de pedido e resposta para representar as mensagens HTTP.

#### <a name="request-object"></a>Objeto de pedido

O objeto de pedido que é passado para o script é do tipo `HttpRequestContext`, que tem as seguintes propriedades:

| Propriedade  | Descrição                                                    | Tipo                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Um objeto que contém o corpo do pedido. `Body` é serializado no melhor tipo com base nos dados. Por exemplo, se os dados forem JSON, é passado como um hashtable. Se os dados são uma corda, é passado como uma corda. | object |
| **`Headers`** | Um dicionário que contém os cabeçalhos de pedido.                | Dicionário<string,string><sup>*</sup> |
| **`Method`** | O método HTTP do pedido.                                | Cadeia de caracteres                    |
| **`Params`**  | Um objeto que contém os parâmetros de encaminhamento do pedido. | Dicionário<string,string><sup>*</sup> |
| **`Query`** | Um objeto que contém os parâmetros de consulta.                  | Dicionário<string,string><sup>*</sup> |
| **`Url`** | A URL do pedido.                                        | Cadeia de caracteres                    |

<sup>*</sup> Todas as chaves `Dictionary<string,string>` são insensíveis aos casos.

#### <a name="response-object"></a>Objeto de resposta

O objeto de resposta que deve enviar de volta é do tipo `HttpResponseContext`, que tem as seguintes propriedades:

| Propriedade      | Descrição                                                 | Tipo                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Um objeto que contém o corpo da resposta.           | object                    |
| **`ContentType`** | Uma mão curta para definir o tipo de conteúdo para a resposta. | Cadeia de caracteres                    |
| **`Headers`** | Um objeto que contém os cabeçalhos de resposta.               | Dicionário ou Hashtable   |
| **`StatusCode`**  | O código de estado HTTP da resposta.                       | corda ou int             |

#### <a name="accessing-the-request-and-response"></a>Acesso ao pedido e resposta

Quando trabalha com os gatilhos HTTP, pode aceder ao pedido http da mesma forma que faria com qualquer outra encadernação de entrada. Está no quarteirão `param`.

Utilize um objeto `HttpResponseContext` para devolver uma resposta, como se pode ver no seguinte:

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

Para certas ligações como a ligação da bolha, é possível especificar o tipo de parâmetro.

Por exemplo, para ter dados do armazenamento Blob fornecidos como uma corda, adicione o seguinte molde do tipo ao meu bloco de `param`:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Perfil PowerShell

Na PowerShell, há o conceito de um perfil PowerShell. Se não estiver familiarizado com os perfis da PowerShell, consulte [os perfis](/powershell/module/microsoft.powershell.core/about/about_profiles).

Nas Funções PowerShell, o script de perfil executa quando a aplicação de função começa. As aplicações de função começam quando foram implantadas pela primeira vez e depois de serem deslocadas[(arranque a frio).](#cold-start)

Quando cria uma aplicação de função utilizando ferramentas, como visual studio code e ferramentas nucleares de funções azure, é criado um `profile.ps1` padrão para si. O perfil predefinido é mantido [no repositório Core Tools GitHub](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contém:

* Autenticação Automática MSI para Azure.
* A capacidade de ligar o Azure PowerShell `AzureRM` pseudónimos PowerShell, se quiser.

## <a name="powershell-version"></a>Versão PowerShell

A tabela que se segue mostra a versão PowerShell utilizada por cada versão principal do tempo de execução das Funções:

| Versão funções | Versão PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (bloqueado pelo tempo de execução) |
| 2.x               | PowerShell Core 6                              |

Pode ver a versão atual imprimindo `$PSVersionTable` de qualquer função.

## <a name="dependency-management"></a>Gestão de dependências

As funções permitem alavancar a [galeria PowerShell](https://www.powershellgallery.com) para gerir dependências. Com a gestão da dependência ativada, o ficheiro requirements.psd1 é usado para descarregar automaticamente os módulos necessários. Você permite este comportamento definindo a propriedade `managedDependency` para `true` na raiz do [ficheiro host.json](functions-host-json.md), como no seguinte exemplo:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Quando cria um novo projeto de funções PowerShell, a gestão da dependência é ativada por padrão, com o [módulo De`Az`](/powershell/azure/new-azureps-module-az) Azure incluído. O número máximo de módulos atualmente suportados é de 10. A sintaxe suportada é _`MajorNumber`_ versão`.*` ou módulo exato, como mostra o seguinte exemplo.psd1:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Quando atualiza os requisitos.psd1 file, os módulos atualizados são instalados após um reinício.

> [!NOTE]
> As dependências geridas requerem acesso a www.powershellgallery.com para descarregar módulos. Ao correr localmente, certifique-se de que o tempo de funcionamento pode aceder a este URL adicionando quaisquer regras de firewall necessárias. 

As seguintes definições de aplicação podem ser utilizadas para alterar a forma como as dependências geridas são descarregadas e instaladas. A atualização da sua aplicação começa dentro de `MDMaxBackgroundUpgradePeriod`, e o processo de atualização termina dentro de aproximadamente o `MDNewSnapshotCheckPeriod`.

| Definição de aplicativo de função              | Valor predefinido             | Descrição                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 dias)     | Cada processo de trabalhador da PowerShell inicia a verificação de atualizações de módulos na PowerShell Gallery no início do processo e todos os `MDMaxBackgroundUpgradePeriod` depois disso. Quando uma nova versão do módulo está disponível na PowerShell Gallery, está instalada no sistema de ficheiros e disponibilizada aos trabalhadores da PowerShell. Diminuir este valor permite que a sua aplicação de funções obtenha versões mais recentes de módulos mais cedo, mas também aumenta o uso de recursos da aplicação (rede I/O, CPU, armazenamento). O aumento deste valor diminui o uso de recursos da app, mas também pode atrasar a entrega de novas versões de módulos para a sua aplicação. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 hora)       | Depois de instaladas novas versões de módulos no sistema de ficheiros, todos os processos de trabalhador da PowerShell devem ser reiniciados. Reiniciar os trabalhadores da PowerShell afeta a disponibilidade da sua aplicação, uma vez que pode interromper a execução da função atual. Até que todos os processos de trabalhador da PowerShell sejam reiniciados, as invocações de funções podem utilizar as versões antigas ou as novas do módulo. Reiniciar todos os trabalhadores da PowerShell completos dentro `MDNewSnapshotCheckPeriod`. O aumento deste valor diminui a frequência das interrupções, mas também pode aumentar o período de tempo em que as invocações de funções utilizam as versões antigas ou as novas versões do módulo não determinicamente. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 dia)     | Para evitar atualizações excessivas de módulos em reiniciações frequentes do Trabalhador, a verificação das atualizações dos módulos não é efetuada quando qualquer trabalhador já iniciou esse check no último `MDMinBackgroundUpgradePeriod`. |

Aproveitar os seus próprios módulos personalizados é um pouco diferente do que faria normalmente.

No seu computador local, o módulo é instalado numa das pastas disponíveis globalmente no seu `$env:PSModulePath`. Ao correr em Azure, não tem acesso aos módulos instalados na sua máquina. Isto significa que o `$env:PSModulePath` para uma aplicação de função PowerShell difere de `$env:PSModulePath` num script powerShell regular.

Em Funções, `PSModulePath` contém dois caminhos:

* Uma pasta `Modules` que existe na raiz da sua aplicação de função.
* Um caminho para uma pasta `Modules` que é controlada pelo trabalhador da linguagem PowerShell.

### <a name="function-app-level-modules-folder"></a>Função app-nível `Modules` pasta

Para utilizar módulos personalizados, pode colocar módulos nos quais as suas funções dependem de uma pasta `Modules`. A partir desta pasta, os módulos estão automaticamente disponíveis para o tempo de funcionamento das funções. Qualquer função na aplicação de funções pode utilizar estes módulos. 

> [!NOTE]
> Os módulos especificados nos requisitos.o ficheiro psd1 é automaticamente descarregado e incluído no caminho para que não seja necessário incluí-los na pasta de módulos. Estes são armazenados localmente na pasta `$env:LOCALAPPDATA/AzureFunctions` e na pasta `/data/ManagedDependencies` quando executados na nuvem.

Para tirar partido da funcionalidade de módulo personalizado, crie uma pasta `Modules` na raiz da sua aplicação de função. Copie os módulos que pretende utilizar nas suas funções para este local.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Com uma pasta `Modules`, a sua aplicação de função deve ter a seguinte estrutura de pasta:

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

Quando inicia a sua aplicação de funções, o trabalhador da linguagem PowerShell adiciona esta pasta `Modules` ao `$env:PSModulePath` para que possa contar com a auto-carregamento do módulo, tal como faria num script regular da PowerShell.

### <a name="language-worker-level-modules-folder"></a>Pasta de `Modules` nível de trabalhador de linguagem

Vários módulos são comumente usados pelo trabalhador da linguagem PowerShell. Estes módulos são definidos na última posição de `PSModulePath`. 

A lista atual de módulos é a seguinte:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): módulo utilizado para trabalhar com arquivos, como `.zip`, `.nupkg`, e outros.
* **ThreadJob**: Uma implementação baseada em fios das APIs de trabalho powerShell.

Por padrão, as Funções utilizam a versão mais recente destes módulos. Para utilizar uma versão específica do módulo, coloque essa versão específica na pasta `Modules` da sua aplicação de função.

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as definições](functions-app-settings.md)de aplicativos , tais como cordas de ligação ao serviço, são expostas como variáveis ambientais durante a execução. Pode aceder a estas definições utilizando `$env:NAME_OF_ENV_VAR`, como se pode ver no seguinte exemplo:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as definições de aplicativos são lidas a partir do ficheiro do projeto [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="concurrency"></a>Simultaneidade

Por predefinição, o tempo de funcionamento do PowerShell funciona apenas pode processar uma invocação de uma função de cada vez. No entanto, este nível de moeda pode não ser suficiente nas seguintes situações:

* Quando se tenta lidar com um grande número de invocações ao mesmo tempo.
* Quando tiver funções que invoquem outras funções dentro da mesma aplicação de funções.

Pode alterar este comportamento definindo a seguinte variável ambiental para um valor inteiro:

```
PSWorkerInProcConcurrencyUpperBound
```

Definiu esta variável ambiental nas definições da [aplicação](functions-app-settings.md) da sua App de Funções.

### <a name="considerations-for-using-concurrency"></a>Considerações para o uso de moedas

PowerShell é uma única linguagem de script _threaded_ por padrão. No entanto, a concurrency pode ser adicionada usando vários espaços de execução PowerShell no mesmo processo. A quantidade de espaços de execução criados corresponderá à definição de aplicação PSWorkerInProcConcurrencyUpperBound. A entrada será impactada pela quantidade de CPU e memória disponível no plano selecionado.

O Azure PowerShell utiliza alguns contextos de nível de processo e estado para ajudar a _salvá-lo_ do excesso de dactilografia. No entanto, se ligar a moeda na sua app de funções e invocar ações que mudem de estado, pode acabar com as condições de corrida. Estas condições de raça são difíceis de depurar porque uma invocação depende de um certo estado e a outra invocação mudou o estado.

Há um imenso valor em condivisão com o Azure PowerShell, uma vez que algumas operações podem demorar bastante tempo. No entanto, deve proceder com cautela. Se suspeitar que está a passar por uma condição de raça, defina a definição da aplicação PSWorkerInProcConcurrencyUpperBound para `1` e, em vez disso, utilize o isolamento do [processo do trabalhador linguístico](functions-app-settings.md#functions_worker_process_count) para a conmoedação.

## <a name="configure-function-scriptfile"></a>Configurar `scriptFile` de função

Por predefinição, uma função PowerShell é executada a partir de `run.ps1`, um ficheiro que partilha o mesmo directório-mãe que o seu `function.json`correspondente .

A propriedade `scriptFile` no `function.json` pode ser usada para obter uma estrutura de pasta que se parece com o seguinte exemplo:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Neste caso, o `function.json` para `myFunction` inclui uma `scriptFile` propriedade que referencia o ficheiro com a função exportada a executar.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Utilize os módulos PowerShell configurando um ponto de entrada

Este artigo mostrou funções powerShell no ficheiro padrão `run.ps1` script gerado pelos modelos.
No entanto, também pode incluir as suas funções nos módulos PowerShell. Pode fazer referência ao seu código de função específico no módulo utilizando os campos `scriptFile` e `entryPoint` no ficheiro de configuração function.json.

Neste caso, `entryPoint` é o nome de uma função ou cmdlet no módulo PowerShell referenciado em `scriptFile`.

Considere a seguinte estrutura de pasta:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Sempre que `PSFunction.psm1` contenha:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Neste exemplo, a configuração para `myFunction` inclui uma propriedade `scriptFile` que refere `PSFunction.psm1`, que é um módulo PowerShell em outra pasta.  A propriedade `entryPoint` refere a função `Invoke-PSTestFunc`, que é o ponto de entrada no módulo.

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

Quando trabalhar com as funções PowerShell, esteja atento às considerações nas seguintes secções.

### <a name="cold-start"></a>Início frio

Ao desenvolver funções Azure no [modelo de hospedagem sem servidores,](functions-scale.md#consumption-plan)o arranque a frio é uma realidade. *O arranque* a frio refere-se ao período de tempo que a sua aplicação de funções leva a começar a executar para processar um pedido. O arranque a frio acontece com mais frequência no plano de consumo porque a sua aplicação de funções é desligada durante períodos de inatividade.

### <a name="bundle-modules-instead-of-using-install-module"></a>Bundle módulos em vez de usar `Install-Module`

O teu guião é feito em todas as invocações. Evite utilizar `Install-Module` no seu guião. Em vez disso, utilize `Save-Module` antes de publicar para que a sua função não tenha de perder tempo a descarregar o módulo. Se o arranque a frio estiver a afetar as suas funções, considere implementar a sua aplicação de função para um [plano de Serviço de Aplicações](functions-scale.md#app-service-plan) definido para sempre *num* [plano Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Funções Azure desencadeiam e encadernam](functions-triggers-bindings.md)

[host.json reference]: functions-host-json.md
