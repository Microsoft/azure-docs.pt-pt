---
title: Depurar funções do PowerShell do Azure localmente
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
ms.openlocfilehash: f02d2eed2030a5d2f54b3bee85885bbb572ac762
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476669"
---
# <a name="debug-powershell-azure-functions-locally"></a>Depurar funções do PowerShell do Azure localmente

As funções do Azure permite-lhe desenvolver as suas funções como scripts do PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Pode depurar as suas funções de PowerShell localmente, tal como faria com qualquer scripts do PowerShell com as seguintes ferramentas de desenvolvimento padrão:

* [Visual Studio Code](https://code.visualstudio.com/): Editor de texto gratuitas, leves e código-fonte aberto da Microsoft com a extensão de PowerShell, que oferece uma experiência de desenvolvimento completa do PowerShell.
* A consola do PowerShell: Depuração usando os mesmos comandos que utilizaria para depurar qualquer outro processo do PowerShell.

[As ferramentas de núcleo das funções do Azure](functions-run-local.md) oferece suporte à depuração local das funções do Azure, incluindo funções do PowerShell.

## <a name="example-function-app"></a>Aplicação de funções de exemplo

A aplicação de funções usada neste artigo tem uma única função de acionada por HTTP e tem os seguintes ficheiros:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Esta aplicação de função é semelhante ao que obtém ao concluir a [início rápido do PowerShell](functions-create-first-function-powershell.md).

O código de função na `run.ps1` se parece com o seguinte script:

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

## <a name="set-the-attach-point"></a>Definir o ponto de anexar

Para depurar qualquer função de PowerShell, a função precisa parar para o depurador ser anexado. O `Wait-Debugger` cmdlet interrompe a execução e aguarda que o depurador.

Tudo o que precisa fazer é adicionar uma chamada para o `Wait-Debugger` cmdlet imediatamente acima do `if` instrução, da seguinte forma:

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

Depuração us o `if` instrução. 

Com `Wait-Debugger` in-loco, agora pode depurar as funções com o Visual Studio Code ou a consola do PowerShell.

## <a name="debug-in-visual-studio-code"></a>Depurar no Visual Studio Code

Para depurar as suas funções do PowerShell no Visual Studio Code, tem de ter o seguinte instalado:

* [Extensão de PowerShell para Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Extensão das Funções do Azure para o Visual Studio Code](functions-create-first-function-vs-code.md)
* [O PowerShell Core 6.2 ou superior](/powershell/scripting/install/installing-powershell#powershell-core)

Depois de instalar estas dependências, carregar um projeto de funções do PowerShell existente, ou [criar seu primeiro projeto de funções do PowerShell](functions-create-first-function-powershell.md).

>[!NOTE]
> O projeto não deve ter os arquivos de configuração necessárias, lhe for pedido para adicioná-los.

### <a name="set-the-powershell-version"></a>Definir a versão do PowerShell

O PowerShell Core instala lado a lado com Windows PowerShell. Defina o PowerShell Core como a versão do PowerShell para utilizar com a extensão de PowerShell para Visual Studio Code.

1. Prima F1 para exibir o palete de comando, em seguida, procure `Session`.

1. Escolha **PowerShell: Mostrar Menu de sessão**.

1. Se sua **sessão atual** não está **PowerShell Core 6**, escolha **mudar para: O PowerShell Core 6**.

Quando tiver um arquivo de PowerShell aberto, verá a versão apresentada a verde na parte inferior direito da janela. Também selecionar este texto apresenta o menu de sessão. Para obter mais informações, consulte a [escolhendo uma versão do PowerShell para utilizar com a extensão](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Iniciar a aplicação de função

Certifique-se de que `Wait-Debugger` está definido na função onde pretende anexar o depurador.  Com `Wait-Debugger` adicionado, pode depurar a aplicação de funções com o Visual Studio Code.

Escolha o **depurar** painel e, em seguida **anexar a função do PowerShell**.

![Depurador](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Também pode premir a tecla F5 para iniciar a depuração.

A iniciar depuração operação faz as seguintes tarefas:

* Execuções `func extensions install` no terminal para instalar o exigido pela sua aplicação de funções de nenhuma extensão de funções do Azure.
* Execuções `func host start` no terminal para iniciar a aplicação de função no anfitrião de funções.
* Anexe o depurador do PowerShell para o espaço de execução do PowerShell dentro do runtime de funções.

Com a sua aplicação de função em execução, precisa de uma consola do PowerShell separada para chamar a função de acionada por HTTP.

Neste caso, a consola do PowerShell é o cliente. O `Invoke-RestMethod` é utilizado para acionar a função.

Na consola do PowerShell, execute o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Observará que uma resposta não é imediatamente devolvida. Isso ocorre porque `Wait-Debugger` foi anexado, o depurador e o PowerShell execução entraram em modo de interrupção, assim que ele foi. Isso é devido a [conceito de BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), que é explicado mais tarde. Depois que pressionar os `continue` botão, o depurador agora quebra na linha de certo após `Wait-Debugger`.

Neste momento, o depurador for anexado e pode fazer o depurador normal operações. Para obter mais informações sobre como utilizar o depurador no Visual Studio Code, consulte [documentação oficial](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Depois de continua e invoca totalmente o seu script, observará que:

* A consola do PowerShell que fez a `Invoke-RestMethod` devolveu um resultado
* A consola do PowerShell integrada no Visual Studio Code está à espera que um script a ser executado

Mais tarde quando invoca a mesma função, o depurador na extensão de PowerShell quebra correto depois do `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Depuração numa consola do PowerShell

>[!NOTE]
> Esta secção assume que leu a [docs de ferramentas de núcleo de funções do Azure](functions-run-local.md) e saber como utilizar o `func host start` comando para iniciar a aplicação de funções.

Abra uma consola `cd` para o diretório da sua aplicação de função e execute o seguinte comando:

```sh
func host start
```

Com a aplicação de função em execução e o `Wait-Debugger` no lugar, pode anexar ao processo. É necessário duas consolas de PowerShell mais.

Uma das consolas do atua como o cliente. A partir disso, chama `Invoke-RestMethod` para acionar a função. Por exemplo, pode executar o seguinte comando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Observará que ele não retorna uma resposta, o que é um resultado do `Wait-Debugger`. O espaço de execução do PowerShell agora está à espera de um depurador para ser anexado. Vamos fazer com que anexados.

Na outra consola do PowerShell, execute o seguinte comando:

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

Anote o `ProcessId` para o item na tabela com o `ProcessName` como `dotnet`. Este processo é a sua aplicação de funções.

Em seguida, execute o seguinte fragmento:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Uma vez iniciado, o depurador quebra e mostra algo como a seguinte saída:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Neste momento, está parada num ponto de interrupção no [PowerShell depurador](/powershell/module/microsoft.powershell.core/about/about_debuggers). A partir daqui, pode fazer todas as operações de depuração habitual, passo ao longo, examinar, continuar, sair entre outros. Para ver o conjunto completo de comandos de depuração disponíveis na consola, execute o `h` ou `?` comandos.

Também pode definir pontos de interrupção nesse nível com a `Set-PSBreakpoint` cmdlet.

Depois de continua e invoca totalmente o seu script, observará que:

* A consola do PowerShell onde executada `Invoke-RestMethod` agora devolveu um resultado.
* A consola do PowerShell onde executada `Debug-Runspace` está à espera de um script a ser executado.

É possível invocar a mesma função novamente (usando `Invoke-RestMethod` por exemplo) e o depurador quebras de após o `Wait-Debugger` comando.

## <a name="considerations-for-debugging"></a>Considerações para depuração

Tenha em atenção os seguintes problemas ao depurar seu código de funções.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` Pode fazer com que o depurador seja interrompido num ponto de partida inesperado

As utilizações de extensão de PowerShell `Debug-Runspace`, que por sua vez se baseia em do PowerShell `BreakAll` funcionalidade. Esta funcionalidade instrui o PowerShell para parar no primeiro comando é executado. Este comportamento dá-lhe a oportunidade de configurar pontos de interrupção dentro do espaço de execução depurado.

O runtime das funções do Azure executa alguns comandos antes de invocar, na verdade, sua `run.ps1` script, portanto, é possível que o depurador acaba de última hora dentro do `Microsoft.Azure.Functions.PowerShellWorker.psm1` ou `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Esta garantia de reparação deve acontecer, execute o `continue` ou `c` comando ignorar este ponto de interrupção. Em seguida, parar no ponto de interrupção esperado.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o desenvolvimento das funções com o PowerShell, veja [Guia do programador do PowerShell de funções do Azure](functions-reference-powershell.md).
