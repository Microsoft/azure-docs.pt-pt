---
title: Debug PowerShell Azure funciona localmente
description: Compreenda como desenvolver funções utilizando o PowerShell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163765"
---
# <a name="debug-powershell-azure-functions-locally"></a>Debug PowerShell Azure funciona localmente

As Funções Azure permitem desenvolver as suas funções como scripts PowerShell.

Pode desincomodá-lo as suas funções PowerShell localmente, tal como faria com os scripts PowerShell utilizando as seguintes ferramentas de desenvolvimento padrão:

* [Visual Studio Code](https://code.visualstudio.com/): O editor de texto gratuito, leve e de código aberto da Microsoft com a extensão PowerShell que oferece uma experiência de desenvolvimento powerShell completa.
* Uma consola PowerShell: Debug utilizando os mesmos comandos que utilizaria para depurar qualquer outro processo PowerShell.

[As Ferramentas Nucleares azure](functions-run-local.md) suportam a depuração local das funções Azure, incluindo funções PowerShell.

## <a name="example-function-app"></a>Aplicativo de função de exemplo

A aplicação de funções utilizada neste artigo tem uma única função de gatilho http e tem os seguintes ficheiros:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Esta aplicação de funções é semelhante à que obtém quando completa o arranque rápido da [PowerShell](functions-create-first-function-powershell.md).

O código de função em `run.ps1` se parece com o seguinte script:

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

## <a name="set-the-attach-point"></a>Definir o ponto de fixação

Para desimpedir qualquer função PowerShell, a função tem de parar para que o desbugger seja ligado. O `Wait-Debugger` cmdlet para a execução e espera pelo desbugger.

Tudo o que precisa fazer é adicionar uma chamada ao `Wait-Debugger` cmdlet logo acima da declaração `if`, da seguinte forma:

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

A depuração começa na declaração `if`. 

Com `Wait-Debugger` no lugar, pode agora desincomodá-lo as funções usando o Visual Studio Code ou uma consola PowerShell.

## <a name="debug-in-visual-studio-code"></a>Depuração no Código do Estúdio Visual

Para desinserir as suas funções PowerShell no Código do Estúdio Visual, deve ter os seguintes instalados:

* [Extensão PowerShell para Código de Estúdio Visual](/powershell/scripting/components/vscode/using-vscode)
* [Extensão das Funções do Azure para o Visual Studio Code](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 ou superior](/powershell/scripting/install/installing-powershell-core-on-windows)

Depois de instalar estas dependências, carregue um projeto de Funções PowerShell existente, ou [crie o seu primeiro projeto powerShell Functions](functions-create-first-function-powershell.md).

>[!NOTE]
> Caso o seu projeto não tenha os ficheiros de configuração necessários, é-lhe pedido que os adicione.

### <a name="set-the-powershell-version"></a>Definir a versão PowerShell

PowerShell Core instala-se lado a lado com o Windows PowerShell. Desloque o PowerShell Core como a versão PowerShell para utilizar com a extensão PowerShell para Código de Estúdio Visual.

1. Pressione f1 para exibir a palete de comando e, em seguida, procure `Session`.

1. Escolha **PowerShell: Menu de sessão de espetáculos**.

1. Se a sua **sessão atual** não for **PowerShell Core 6,** escolha **a Switch para: PowerShell Core 6**.

Quando tem um ficheiro PowerShell aberto, vê a versão exibida em verde na parte inferior direita da janela. A seleção deste texto também mostra o menu da sessão. Para saber mais, consulte a [Escolha de uma versão do PowerShell para utilizar com a extensão](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Inicie a aplicação de função

Verifique se `Wait-Debugger` está definida na função onde pretende prender o desador.  Com `Wait-Debugger` adicionados, pode desincomodá-lo usando o Código visual do estúdio.

Escolha o painel **Debug** e, em seguida, **fixe-o à função PowerShell**.

![debugger](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Também pode premir a tecla F5 para começar a depurar.

A operação de depuração inicial faz as seguintes tarefas:

* Executa `func extensions install` no terminal para instalar quaisquer extensões de Funções Azure exigidas pela sua aplicação de função.
* Executa `func host start` no terminal para iniciar a aplicação de funções no anfitrião funções.
* Fixe o debugger PowerShell ao espaço de execução powerShell dentro do tempo de funcionamento das funções.

>[!NOTE]
> Tem de garantir que o PSWorkerInProcConcurrencyUpperBound está definido para 1 para garantir uma experiência correta de depuração no Código do Estúdio Visual. Este é o padrão.

Com a aplicação de funções em funcionamento, precisa de uma consola PowerShell separada para ligar para a função ativada http.

Neste caso, a consola PowerShell é o cliente. O `Invoke-RestMethod` é usado para desencadear a função.

Numa consola PowerShell, execute o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Vai notar que uma resposta não é devolvida imediatamente. Isto porque `Wait-Debugger` ligado ao debugger e a execução da PowerShell entrou em modo de rutura o mais rápido possível. Isto deve-se ao [conceito BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), que é explicado mais tarde. Depois de carregar no botão `continue`, o desordeiro parte-se agora na linha logo após `Wait-Debugger`.

Neste momento, o debugger está ligado e podes fazer todas as operações normais de desbugger. Para obter mais informações sobre a utilização do debugger no Código do Estúdio Visual, consulte [a documentação oficial](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Depois de continuar e invocar totalmente o seu guião, vai notar que:

* A consola PowerShell que fez o `Invoke-RestMethod` devolveu um resultado
* A Consola Integrada PowerShell no Código do Estúdio Visual está à espera que um script seja executado

Mais tarde, quando invoca a mesma função, o descaremo na extensão PowerShell rompe logo após o `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Depuração numa consola PowerShell

>[!NOTE]
> Esta secção pressupõe que leu os [docs core tools das funções do Azure](functions-run-local.md) e sabe como usar o comando `func host start` para iniciar a sua aplicação de função.

Abra uma consola, `cd` no diretório da sua aplicação de função, e execute o seguinte comando:

```sh
func host start
```

Com a aplicação de funções em execução e a `Wait-Debugger` no lugar, pode anexar-se ao processo. Precisas de mais duas consolas PowerShell.

Uma das consolas funciona como cliente. A partir disto, chama-se `Invoke-RestMethod` para desencadear a função. Por exemplo, pode executar o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Vai notar que não devolve uma resposta, que é o resultado da `Wait-Debugger`. O espaço de corrida powerShell está agora à espera que um depurador seja ligado. Vamos pôr isto em anexo.

Na outra consola PowerShell, execute o seguinte comando:

```powershell
Get-PSHostProcessInfo
```

Este cmdlet devolve uma tabela que se parece com a seguinte saída:

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

Tome nota da `ProcessId` para o item na mesa com o `ProcessName` como `dotnet`. Este processo é a sua aplicação de funções.

Em seguida, executar o seguinte corte:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Uma vez iniciado, o debugger quebra-se e mostra algo como a seguinte saída:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Neste momento, estás parado num ponto de rutura no [debugger powerShell.](/powershell/module/microsoft.powershell.core/about/about_debuggers) A partir daqui, podes fazer todas as operações habituais de depuração, passar por cima, entrar, continuar, desistir, e outras. Para ver o conjunto completo de comandos de depuração disponíveis na consola, execute os comandos `h` ou `?`.

Também pode definir pontos de rutura a este nível com o `Set-PSBreakpoint` cmdlet.

Assim que continuare invocar totalmente o seu guião, notará que:

* A consola PowerShell onde executaste `Invoke-RestMethod` agora devolveu um resultado.
* A consola PowerShell onde executaste `Debug-Runspace` está à espera que um guião seja executado.

Pode invocar novamente a mesma função (usando `Invoke-RestMethod` por exemplo) e o desordeiro rompe logo após o comando `Wait-Debugger`.

## <a name="considerations-for-debugging"></a>Considerações para depuração

Tenha em mente os seguintes problemas ao depurar o código funções.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` pode fazer com que o seu depurador se rompa num lugar inesperado.

A extensão PowerShell utiliza `Debug-Runspace`, que por sua vez depende da funcionalidade `BreakAll` da PowerShell. Esta funcionalidade diz à PowerShell para parar no primeiro comando que é executado. Este comportamento dá-lhe a oportunidade de definir pontos de rutura dentro do espaço de corrida depurado.

O tempo de funcionamento das Funções Azure executa alguns comandos antes de invocar o seu roteiro `run.ps1`, por isso é possível que o debugger acabe por quebrar dentro do `Microsoft.Azure.Functions.PowerShellWorker.psm1` ou `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Se esta rutura acontecer, execute o comando `continue` ou `c` para saltar por cima deste ponto de rutura. Paras no ponto de rutura esperado.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o desenvolvimento de Funções utilizando powerShell, consulte o guia de [desenvolvimento powerShell funções do Azure.](functions-reference-powershell.md)
