---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: ca8da227c97a44abe14354a5c530d508ce357884
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88704081"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

A Azure Functions Core Tools integra-se com o Código do Estúdio Visual para permitir que você corra e depure um projeto Azure Functions localmente. Para obter detalhes sobre como depurar no Código do Estúdio Visual, consulte [as Funções Debug PowerShell Azure localmente](../articles/azure-functions/functions-debug-powershell-local.md). 

1. Prima F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Anexar o fio de consulta `?name=<yourname>` a este URL e, em seguida, usar `Invoke-RestMethod` num segundo comando PowerShell pronta para executar o pedido, da seguinte forma:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Também pode executar o pedido GET a partir de um navegador a partir do seguinte URL:

    `http://localhost:7071/api/HttpExample?name=PowerShell`

    Quando liga para o ponto final httpTrigger sem passar um `name` parâmetro como parâmetro de consulta ou no corpo, a função retorna um `BadRequest` erro. Quando revê o código em run.ps1, vê que este erro ocorre por design.

1. As informações sobre o pedido são mostradas no painel **terminal.**

    ![Execução de funções no painel terminal](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Quando terminar, prima **Ctrl + C** para parar as Ferramentas Core.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.