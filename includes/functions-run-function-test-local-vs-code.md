---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 5bc1d2ddb5b9ddb185a8349fb42d5f5db79c1498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201354"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

O Código do Estúdio Visual integra-se com [as ferramentas core do Azure Functions](../articles/azure-functions/functions-run-local.md) para permitir que execute este projeto no seu computador de desenvolvimento local antes de publicar para o Azure.

1. Para ligar para a sua função, prima <kbd>F5</kbd> para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**. A sua aplicação começa no painel **Terminal.** Pode ver o ponto final url da sua função desencadeada por HTTP a funcionar localmente.

    ![Função local saída do Código VS](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Se tiver problemas em funcionamento no Windows, certifique-se de que o terminal padrão do Código do Estúdio Visual não está definido para **WSL Bash**.

1. Com as Ferramentas Core em funcionamento, vá para a área **Azure: Funções.** Em **Funções**, expandir funções **de projeto**  >  **locais.** Clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) na `HttpExample` função e escolha **Executar Função Agora...**.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Execute a função agora a partir do Código do Estúdio Visual":::
    
1. In **Enter request body** você vê o valor do corpo da mensagem de pedido de `{ "name": "Azure" }` . Prima Insira para enviar esta mensagem de pedido para a sua função. 

   Em vez disso, poderia ter enviado um pedido HTTP GET para o <http://localhost:7071/api/HttpExample> endereço num browser.

1. Quando a função executa localmente e devolve uma resposta, uma notificação é levantada no Código do Estúdio Visual. As informações sobre a execução da função são mostradas no painel **terminal.**

1. Prima <kbd>Ctrl + C</kbd> para parar as Ferramentas Core e desligar o depurar.
