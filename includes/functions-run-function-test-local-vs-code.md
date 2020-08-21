---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 052e0c93732b99efa37b029cad29dc2efded78ee
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88704494"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

O Código do Estúdio Visual integra-se com [as Ferramentas Core do Azure Functions](../articles/azure-functions/functions-run-local.md) para permitir que execute este projeto no seu computador de desenvolvimento local antes de publicar para o Azure.

1. Para ligar para a sua função, prima F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. Se ainda não instalou ferramentas principais do Azure Functions, selecione **Instale** na mensagem. Quando as Ferramentas Core são instaladas, a sua aplicação começa no painel **Terminal.** Pode ver o ponto final url da sua função desencadeada por HTTP a funcionar localmente. 

    ![Resultado local do Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Com as Ferramentas Core em execução, navegue para o seguinte URL para executar um pedido GET, que inclui `?name=Functions` cadeia de consulta.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Uma resposta é devolvida, que se parece com o seguinte num browser:

    ![Resposta da função localhost no browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. As informações sobre o pedido são mostradas no painel **terminal.**

    ![Execução de funções no painel terminal](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Prima Ctrl + C para parar as Ferramentas Core e desligar o depurar.
