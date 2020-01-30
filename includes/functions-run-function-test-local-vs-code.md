---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842221"
---
## <a name="run-the-function-locally"></a>Executar localmente a função

O Visual Studio Code integra-se com as [Ferramentas Core funções do Azure](../articles/azure-functions/functions-run-local.md) para permitir que execute este projeto no seu computador de desenvolvimento local antes de publicar no Azure.

1. Para ligar para a sua função, prima F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. Se ainda não tiver instalado as ferramentas core funções do Azure, **selecione Instalar** à mão. Quando as Ferramentas Core são instaladas, a sua aplicação começa no painel **Terminal.**

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP. 

    ![Resultado local do Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Cole o URL do pedido HTTP na barra de endereço do browser. Anexar a cadeia de consulta `?name=<yourname>` a este URL e executar o pedido GET. 

1. Uma resposta é devolvida, que se parece com a seguinte num browser:

    ![Resposta da função localhost no browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Pressione o Shift + F5 para parar as Ferramentas Core e desligar o desbugger.
