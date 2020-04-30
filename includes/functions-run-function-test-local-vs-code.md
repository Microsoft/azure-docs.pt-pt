---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76964156"
---
## <a name="run-the-function-locally"></a>Executar localmente a função

O Visual Studio Code integra-se com as [Ferramentas Core funções do Azure](../articles/azure-functions/functions-run-local.md) para permitir que execute este projeto no seu computador de desenvolvimento local antes de publicar no Azure.

1. Para ligar para a sua função, prima F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. Se ainda não tiver instalado as ferramentas core funções do Azure, **selecione Instalar** à mão. Quando as Ferramentas Core são instaladas, a sua aplicação começa no painel **Terminal.** Pode ver o ponto final url da sua função desencadeada em HTTP a funcionar localmente. 

    ![Resultado local do Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Com as Ferramentas Core em execução, navegue `?name=Functions` para o seguinte URL para executar um pedido GET, que inclui a corda de consulta.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. Uma resposta é devolvida, que se parece com a seguinte num browser:

    ![Resposta da função localhost no browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. A informação sobre o pedido é mostrada no painel **terminal.**

    ![Execução de funções no painel terminal](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Prima Ctrl + C para parar as Ferramentas Core e desligar o desbugger.
