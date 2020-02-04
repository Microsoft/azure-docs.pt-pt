---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: 916aa2552e5dd004ec767df98ce7c78f7320efd0
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964147"
---
## <a name="run-the-function-locally"></a>Executar localmente a função

Azure Functions Core Tools integra-se com o Visual Studio Code para permitir que você execute e depure um projeto de Azure Functions localmente.  

1. Para depurar sua função, insira uma chamada para o cmdlet [`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) no código de função antes de você desejar anexar o depurador e pressione F5 para iniciar o projeto do aplicativo de funções e anexar o depurador. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Anexar a cadeia de consulta `?name=<yourname>` a este URL e, em seguida, utilizar `Invoke-RestMethod` numa segunda solicitação de comando PowerShell para executar o pedido, da seguinte forma:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Também pode executar o pedido GET a partir de um browser a partir do seguinte URL:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Quando liga para o ponto final httpTrigger sem passar um parâmetro `name`, quer como parâmetro de consulta, quer no corpo, a função devolve um erro `BadRequest`. Ao examinar o código em Run. ps1, você verá que esse erro ocorre por design.

1. A informação sobre o pedido é mostrada no painel **terminal.**

    ![Execução de funções no painel terminal](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Para parar de depurar, prima Ctrl + C para parar as Ferramentas Core.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

> [!NOTE]
> Lembre-se de remover todas as chamadas para `Wait-Debugger` antes de publicar suas funções no Azure. 