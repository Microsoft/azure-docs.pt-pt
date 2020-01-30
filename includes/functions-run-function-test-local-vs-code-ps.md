---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: c54145cf48912d3911a39e681d85cb6907be8e52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842321"
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

    Você também pode executar a solicitação GET de um navegador.

    Quando liga para o ponto final httpTrigger sem passar um parâmetro de `name` quer como parâmetro de consulta ou no corpo, a função devolve um erro [HttpStatusCode]::BadRequest erro. Ao examinar o código em Run. ps1, você verá que esse erro ocorre por design.

1. Para parar a depuração, prima Shift + F5.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

> [!NOTE]
> Lembre-se de remover todas as chamadas para `Wait-Debugger` antes de publicar suas funções no Azure. 