---
title: Depurar Azure Functions do PowerShell localmente
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
ms.openlocfilehash: 5b396ef6b00d53a313ed4fb426685c12e2c1549d
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981852"
---
# <a name="debug-powershell-azure-functions-locally"></a>Depurar Azure Functions do PowerShell localmente

Azure Functions permite desenvolver suas funções como scripts do PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Você pode depurar suas funções do PowerShell localmente como faria com qualquer script do PowerShell usando as seguintes ferramentas de desenvolvimento padrão:

* [Visual Studio Code](https://code.visualstudio.com/): Editor de texto gratuito, leve e de software livre da Microsoft com a extensão do PowerShell que oferece uma experiência de desenvolvimento completa do PowerShell.
* Um console do PowerShell: Depurar usando os mesmos comandos que você usaria para depurar qualquer outro processo do PowerShell.

O [Azure Functions Core Tools](functions-run-local.md) dá suporte à depuração local de Azure functions, incluindo o PowerShell functions.

## <a name="example-function-app"></a>Exemplo de aplicativo de funções

O aplicativo de funções usado neste artigo tem uma única função disparada por HTTP e tem os seguintes arquivos:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Esse aplicativo de funções é semelhante ao que você obtém quando conclui o [início rápido do PowerShell](functions-create-first-function-powershell.md).

O código de função em `run.ps1` é semelhante ao seguinte script:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Definir o ponto de anexo

Para depurar qualquer função do PowerShell, a função precisa parar para que o depurador seja anexado. O cmdlet `Wait-Debugger` interrompe a execução e aguarda o depurador.

Tudo o que você precisa fazer é adicionar uma chamada ao cmdlet `Wait-Debugger` logo acima da instrução `if`, da seguinte maneira:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

A depuração começa na instrução `if`. 

Com `Wait-Debugger` em vigor, agora você pode depurar as funções usando o Visual Studio Code ou um console do PowerShell.

## <a name="debug-in-visual-studio-code"></a>Depurar no Visual Studio Code

Para depurar as funções do PowerShell no Visual Studio Code, você deve ter o seguinte instalado:

* [Extensão do PowerShell para Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Extensão das Funções do Azure para o Visual Studio Code](functions-create-first-function-vs-code.md)
* [PowerShell Core 6,2 ou superior](/powershell/scripting/install/installing-powershell-core-on-windows)

Depois de instalar essas dependências, carregue um projeto de funções do PowerShell existente ou [crie seu primeiro projeto de funções do PowerShell](functions-create-first-function-powershell.md).

>[!NOTE]
> Se o seu projeto não tiver os arquivos de configuração necessários, você será solicitado a adicioná-los.

### <a name="set-the-powershell-version"></a>Definir a versão do PowerShell

O PowerShell Core é instalado lado a lado com o Windows PowerShell. Defina o PowerShell Core como a versão do PowerShell a ser usada com a extensão do PowerShell para Visual Studio Code.

1. Pressione F1 para exibir o palete de comandos e, em seguida, procure `Session`.

1. Escolha **PowerShell: Mostrar menu de sessão @ no__t-0.

1. Se a **sessão atual** não for o **PowerShell Core 6**, escolha **Switch para: PowerShell Core 6 @ no__t-0.

Quando você tiver um arquivo do PowerShell aberto, verá a versão exibida em verde na parte inferior direita da janela. A seleção desse texto também exibe o menu sessão. Para saber mais, consulte [escolhendo uma versão do PowerShell para usar com a extensão](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Iniciar o aplicativo de funções

Verifique se `Wait-Debugger` está definido na função em que você deseja anexar o depurador.  Com `Wait-Debugger` adicionado, você pode depurar seu aplicativo de funções usando Visual Studio Code.

Escolha o painel **depurar** e **anexe à função do PowerShell**.

![depurador](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Você também pode pressionar a tecla F5 para iniciar a depuração.

A operação iniciar depuração realiza as seguintes tarefas:

* Executa `func extensions install` no terminal para instalar qualquer extensão de Azure Functions exigida pelo seu aplicativo de funções.
* Executa `func host start` no terminal para iniciar o aplicativo de funções no host do functions.
* Anexe o depurador do PowerShell ao runspace do PowerShell dentro do tempo de execução do functions.

>[!NOTE]
> Você precisa garantir que PSWorkerInProcConcurrencyUpperBound esteja definido como 1 para garantir a experiência de depuração correta em Visual Studio Code. Esse é o padrão.

Com seu aplicativo de funções em execução, você precisa de um console do PowerShell separado para chamar a função disparada por HTTP.

Nesse caso, o console do PowerShell é o cliente do. O `Invoke-RestMethod` é usado para disparar a função.

Em um console do PowerShell, execute o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Você observará que uma resposta não é retornada imediatamente. Isso ocorre porque `Wait-Debugger` anexou o depurador e a execução do PowerShell entrou no modo de interrupção assim que possível. Isso ocorre devido ao [conceito de BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), que é explicado posteriormente. Depois de pressionar o botão `continue`, o depurador agora será interrompido na linha logo após `Wait-Debugger`.

Neste ponto, o depurador é anexado e você pode fazer todas as operações normais do depurador. Para obter mais informações sobre como usar o depurador no Visual Studio Code, consulte [a documentação oficial](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Depois de continuar e chamar totalmente o script, você observará que:

* O console do PowerShell que fez o `Invoke-RestMethod` retornou um resultado
* O console integrado do PowerShell no Visual Studio Code está aguardando a execução de um script

Posteriormente, quando você invoca a mesma função, o depurador na extensão do PowerShell é interrompido logo após o `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Depuração em um console do PowerShell

>[!NOTE]
> Esta seção pressupõe que você leu o [Azure Functions Core Tools docs](functions-run-local.md) e saberá como usar o comando `func host start` para iniciar seu aplicativo de funções.

Abra um console do, `cd` no diretório do seu aplicativo de funções e execute o seguinte comando:

```sh
func host start
```

Com o aplicativo de funções em execução e o `Wait-Debugger` em vigor, você pode anexar ao processo. Você precisa de mais dois consoles do PowerShell.

Um dos consoles atua como o cliente. A partir disso, você chama `Invoke-RestMethod` para disparar a função. Por exemplo, você pode executar o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Você observará que ele não retorna uma resposta, que é um resultado do `Wait-Debugger`. O runspace do PowerShell agora está aguardando a anexação de um depurador. Vamos colocar isso em anexo.

No outro console do PowerShell, execute o seguinte comando:

```powershell
Get-PSHostProcessInfo
```

Esse cmdlet retorna uma tabela parecida com a seguinte saída:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Anote o `ProcessId` para o item na tabela com o `ProcessName` como `dotnet`. Esse processo é seu aplicativo de funções.

Em seguida, execute o seguinte trecho:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Depois de iniciado, o depurador é interrompido e mostra algo semelhante à seguinte saída:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Neste ponto, você está parado em um pontos de interrupção no [depurador do PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). A partir daqui, você pode fazer todas as operações de depuração usuais, depurar, entrar, continuar, encerrar e outras. Para ver o conjunto completo de comandos de depuração disponíveis no console do, execute os comandos `h` ou `?`.

Você também pode definir pontos de interrupção nesse nível com o cmdlet `Set-PSBreakpoint`.

Depois de continuar e chamar totalmente o script, você observará que:

* O console do PowerShell em que você executou `Invoke-RestMethod` agora retornou um resultado.
* O console do PowerShell em que você executou `Debug-Runspace` está aguardando a execução de um script.

Você pode invocar a mesma função novamente (usando `Invoke-RestMethod`, por exemplo) e o depurador é interrompido logo após o comando `Wait-Debugger`.

## <a name="considerations-for-debugging"></a>Considerações sobre depuração

Tenha em mente os seguintes problemas ao depurar seu código de funções.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` pode fazer com que o depurador quebre em um local inesperado

A extensão do PowerShell usa `Debug-Runspace`, que por sua vez depende do recurso de `BreakAll` do PowerShell. Esse recurso informa ao PowerShell para parar no primeiro comando que é executado. Esse comportamento oferece a oportunidade de definir pontos de interrupção dentro do runspace depurado.

O tempo de execução de Azure Functions executa alguns comandos antes de realmente invocar o script de `run.ps1`, portanto, é possível que o depurador acabe a interrupção dentro do `Microsoft.Azure.Functions.PowerShellWorker.psm1` ou `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Se essa interrupção ocorrer, execute o comando `continue` ou `c` para ignorar esse ponto de interrupção. Em seguida, você pára no ponto de interrupção esperado.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como desenvolver funções usando o PowerShell, confira [Azure Functions guia do desenvolvedor do PowerShell](functions-reference-powershell.md).
