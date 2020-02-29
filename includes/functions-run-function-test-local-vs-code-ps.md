---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191070"
---
## <a name="run-the-function-locally"></a>Executar localmente a função

As Ferramentas Nucleares azure integram-se com o Código do Estúdio Visual para permitir que você execute e depura um projeto de Funções Azure localmente. Para mais detalhes sobre como depurar o Código do Estúdio Visual, consulte as [funções Debug PowerShell Azure localmente](../articles/azure-functions/functions-debug-powershell-local.md). 

1. Pressione F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP.

    ![Resultado local do Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Anexar a cadeia de consulta `?name=<yourname>` a este URL e, em seguida, utilizar `Invoke-RestMethod` numa segunda solicitação de comando PowerShell para executar o pedido, da seguinte forma:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Também pode executar o pedido GET a partir de um browser a partir do seguinte URL:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Quando liga para o ponto final httpTrigger sem passar um parâmetro `name`, quer como parâmetro de consulta, quer no corpo, a função devolve um erro `BadRequest`. Ao rever o código em run.ps1, vê-se que este erro ocorre por design.

1. A informação sobre o pedido é mostrada no painel **terminal.**

    ![Execução de funções no painel terminal](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Quando terminar, prima **CTRL + C** para parar as Ferramentas Core.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.