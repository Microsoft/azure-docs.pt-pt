---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 43da0ea4ddfc5410425465d436522523739218fe
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408582"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

O Código do Estúdio Visual integra-se com [as ferramentas core do Azure Functions](../articles/azure-functions/functions-run-local.md) para permitir que execute este projeto no seu computador de desenvolvimento local antes de publicar para o Azure.

1. Para ligar para a sua função, prima <kbd>F5</kbd> para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. Se ainda não instalou ferramentas principais do Azure Functions, selecione **Instale** na mensagem. Quando as Ferramentas Core são instaladas, a sua aplicação começa no painel **Terminal.** Pode ver o ponto final url da sua função desencadeada por HTTP a funcionar localmente.

    ![Função local saída do Código VS](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Com as Ferramentas Core em execução, navegue para o seguinte URL para executar um pedido GET, que inclui `?name=Functions` cadeia de consulta.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Uma resposta é devolvida, que se parece com o seguinte num browser:

    ![Browser - saída de exemplo local](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. As informações sobre o pedido são mostradas no painel **terminal.**

    ![Início do anfitrião de tarefas - saída do terminal de código VS](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Prima <kbd>Ctrl + C</kbd> para parar as Ferramentas Core e desligar o depurar.
