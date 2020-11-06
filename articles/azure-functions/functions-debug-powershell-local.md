---
title: Debug PowerShell Azure Funções localmente
description: Aprenda a depurar as funções PowerShell quando estiver a funcionar localmente.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a668024db126c82f96756555aba513b77f7d7366
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422965"
---
# <a name="debug-powershell-azure-functions-locally"></a>Debug PowerShell Azure Funções localmente

As Funções Azure permitem-lhe desenvolver as suas funções como scripts PowerShell.

Pode depurar as suas funções PowerShell localmente, tal como faria com quaisquer scripts PowerShell utilizando as seguintes ferramentas de desenvolvimento padrão:

* [Visual Studio Code](https://code.visualstudio.com/): O editor de texto gratuito, leve e de código aberto da Microsoft com a extensão PowerShell que oferece uma experiência completa de desenvolvimento powerShell.
* Uma consola PowerShell: Debug usando os mesmos comandos que usaria para depurar qualquer outro processo PowerShell.

[As Ferramentas Principais de Funções Azure](functions-run-local.md) suportam a depuração local das funções Azure, incluindo funções PowerShell.

## <a name="example-function-app"></a>App de função de exemplo

A aplicação de função utilizada neste artigo tem uma única função HTTP desencadeada e tem os seguintes ficheiros:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Esta aplicação de função é semelhante à que obtém quando completa o [arranque rápido powerShell](./create-first-function-vs-code-powershell.md).

O código de função `run.ps1` parece o seguinte script:

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

Para depurar qualquer função PowerShell, a função tem de parar para que o depurar seja fixado. O `Wait-Debugger` cmdlet para a execução e espera pelo depurado.

>[!NOTE]
>Ao utilizar o PowerShell 7, não precisa de adicionar a `Wait-Debugger` chamada no seu código.

Tudo o que precisa fazer é adicionar uma chamada ao `Wait-Debugger` cmdlet logo acima da `if` declaração, da seguinte forma:

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

Depurgging começa na `if` declaração. 

Com `Wait-Debugger` o lugar, pode agora depurar as funções utilizando o Visual Studio Code ou uma consola PowerShell.

## <a name="debug-in-visual-studio-code"></a>Debug em Código de Estúdio Visual

Para depurar as suas funções PowerShell no Código do Estúdio Visual, tem de ter as seguintes instaladas:

* [Extensão PowerShell para Código do Estúdio Visual](/powershell/scripting/components/vscode/using-vscode)
* [Extensão das Funções do Azure para o Visual Studio Code](./create-first-function-cli-powershell.md)
* [PowerShell Core 6.2 ou superior](/powershell/scripting/install/installing-powershell-core-on-windows)

Depois de instalar estas dependências, carregue um projeto de Funções PowerShell existente ou [crie o seu primeiro projeto PowerShell Functions](./create-first-function-vs-code-powershell.md).

>[!NOTE]
> Se o seu projeto não tiver os ficheiros de configuração necessários, é solicitado que os adicione.

### <a name="set-the-powershell-version"></a>Definir a versão PowerShell

PowerShell Core instala lado a lado com o Windows PowerShell. Desata o PowerShell Core como a versão PowerShell para utilizar com a extensão PowerShell para Código de Estúdio Visual.

1. Prima F1 para exibir a palete de comando e, em seguida, procure `Session` .

1. Escolha **PowerShell: Menu de sessão de espetáculo**.

1. Se a **sessão atual** não for **PowerShell Core 6,** escolha **Switch para: PowerShell Core 6**.

Quando tiver um ficheiro PowerShell aberto, vê a versão apresentada a verde no canto inferior direito da janela. A seleção deste texto também mostra o menu de sessão. Para saber mais, consulte a [versão Escolha de Uma Versão do PowerShell para usar com a extensão](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Inicie a aplicação de função

Verifique se `Wait-Debugger` está definido na função em que pretende fixar o depurador.  Com `Wait-Debugger` adição, pode desordiá-la através do Código do Estúdio Visual.

Escolha o **painel Debug** e, em seguida, **fixe-se à função PowerShell**.

![depurar](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Também pode premir a tecla F5 para começar a depurar.

A operação de depuragem inicial faz as seguintes tarefas:

* Funciona `func extensions install` no terminal para instalar quaisquer extensões de Funções Azure exigidas pela sua aplicação de função.
* Funciona `func host start` no terminal para iniciar a aplicação de função no anfitrião Funções.
* Fixe o depurar PowerShell ao espaço de funcionamento PowerShell dentro do tempo de funcionamento das funções.

>[!NOTE]
> Tem de garantir que o PSWorkerInProcConcurrencyUpperBound está definido para 1 para garantir a experiência de depuração correta no Código do Estúdio Visual. Esta é a predefinição.

Com a aplicação de função em funcionamento, precisa de uma consola PowerShell separada para ligar para a função http triggered.

Neste caso, a consola PowerShell é o cliente. O `Invoke-RestMethod` é usado para ativar a função.

Numa consola PowerShell, executar o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Vai notar que uma resposta não é devolvida imediatamente. Isso porque `Wait-Debugger` prendeu o depurar e a execução PowerShell entrou em modo de rutura o mais rápido possível. Isto deve-se ao [conceito BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), que é explicado mais tarde. Depois de premir o `continue` botão, o depurar parte-se agora na linha logo a seguir `Wait-Debugger` .

Neste ponto, o depurar está ligado e você pode fazer todas as operações normais de depurar. Para obter mais informações sobre a utilização do depurar no Código do Estúdio Visual, consulte [a documentação oficial](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Depois de continuar e invocar totalmente o seu guião, vai notar que:

* A consola PowerShell que fez o `Invoke-RestMethod` que devolveu um resultado
* A Consola Integrada PowerShell no Código do Estúdio Visual está à espera que um script seja executado

Mais tarde, quando invoca a mesma função, o depurar na extensão PowerShell parte-se logo após o `Wait-Debugger` .

## <a name="debugging-in-a-powershell-console"></a>Depurando numa consola PowerShell

>[!NOTE]
> Esta secção pressupõe que leu os [docs Azure Functions Core Tools](functions-run-local.md) e sabe como usar o `func host start` comando para iniciar a sua aplicação de função.

Abra uma consola, `cd` no diretório da sua aplicação de funções, e execute o seguinte comando:

```sh
func host start
```

Com a aplicação de função em funcionamento e o `Wait-Debugger` no lugar, pode anexar-se ao processo. Precisas mesmo de mais duas consolas PowerShell.

Uma das consolas funciona como cliente. A partir daqui, ligue `Invoke-RestMethod` para desencadear a função. Por exemplo, pode executar o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Vai notar que não devolve uma resposta, o que é o resultado `Wait-Debugger` do. O espaço de funcionação powerShell está agora à espera que um depurar seja ligado. Vamos a que isto.

Na outra consola PowerShell, executar o seguinte comando:

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

Tome nota do `ProcessId` item na tabela com o como `ProcessName` `dotnet` . Este processo é a sua aplicação de função.

Em seguida, executar o seguinte corte:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Uma vez iniciado, o depurar quebra-se e mostra algo como a seguinte saída:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Neste momento, estás parado num ponto de rutura no [depuração powerShell.](/powershell/module/microsoft.powershell.core/about/about_debuggers) A partir daqui, podes fazer todas as operações habituais de depurar, passar por cima, entrar, continuar, desistir, e outras. Para ver o conjunto completo de comandos de depurg disponível na consola, executar os `h` comandos ou `?` comandos.

Também pode definir pontos de rutura a este nível com o `Set-PSBreakpoint` cmdlet.

Assim que continuares e invocares totalmente o teu guião, vais reparar que:

* A consola PowerShell onde executou `Invoke-RestMethod` devolveu agora um resultado.
* A consola PowerShell onde executaste `Debug-Runspace` está à espera que um script seja executado.

Pode invocar novamente a mesma função (usando `Invoke-RestMethod` por exemplo) e o depurado entra logo após o `Wait-Debugger` comando.

## <a name="considerations-for-debugging"></a>Considerações para depurar

Tenha em mente os seguintes problemas ao depurar o seu código funções.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` pode fazer o seu depurgger quebrar em um lugar inesperado

A extensão PowerShell `Debug-Runspace` utiliza, por sua vez, a funcionalidade powerShell. `BreakAll` Esta funcionalidade diz ao PowerShell para parar no primeiro comando que for executado. Este comportamento dá-lhe a oportunidade de definir pontos de rutura dentro do espaço de execução depurado.

O tempo de execução do Azure Functions executa alguns comandos antes de invocar o seu `run.ps1` script, por isso é possível que o depurador acabe por quebrar dentro do `Microsoft.Azure.Functions.PowerShellWorker.psm1` ou `Microsoft.Azure.Functions.PowerShellWorker.psd1` .

Se esta rutura acontecer, passe o `continue` ou o comando para passar por cima deste ponto de `c` rutura. Depois para no ponto de rutura esperado.

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver dificuldades durante a depuragem, deve verificar o seguinte:

| Marcar | Ação |
|------|------|
| Fugir `func --version` do terminal. Se tiver um erro que `func` não possa ser encontrado, as Ferramentas Core (func.exe) podem estar ausentes da `path` variável local.| [Reinstalar ferramentas principais](functions-run-local.md#v2).|  
| No Código do Estúdio Visual, o terminal predefinido precisa de ter acesso a func.exe. Certifique-se de que não está a utilizar um terminal predefinido que não tenha Ferramentas Core instaladas, como o Subsistema Windows para Linux (WSL).  | Deite a camada predefinida no Código do Estúdio Visual para o PowerShell 7 (recomendado) ou para o Windows PowerShell 5.1.|
  

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o desenvolvimento de funções utilizando o PowerShell, consulte [o guia de desenvolvedores powershell das funções Azure](functions-reference-powershell.md).
