---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 55a75651b724a4fe975f655958e36fbd40e35db7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748256"
---
## <a name="run-the-function-in-azure"></a>Executar a função em Azure

1. De volta à área **Azure: Funções** na barra lateral, expanda a nova aplicação de função sob a sua subscrição. Expandir **funções**, clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) em **HttpExample**, e, em seguida, escolha **URL de função copiar**.

    ![Copie o URL de função para o novo gatilho HTTP](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Cole este URL para o pedido HTTP na barra de endereços do seu navegador, adicione o `name` string de consulta quanto ao final deste URL `?name=Functions` e, em seguida, execute o pedido. O URL que chama a função acionada por HTTP deve estar no seguinte formato:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions
    ```

    O exemplo a seguir mostra a resposta no navegador ao pedido remoto GET devolvido pela função:

    ![Resposta da função no browser](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
