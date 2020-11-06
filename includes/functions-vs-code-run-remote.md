---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: e9465e014c1b8770f8968cbee89a5b455ad53470
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425068"
---
## <a name="run-the-function-in-azure"></a>Executar a função em Azure

1. De volta à área **Azure: Funções** na barra lateral, expanda a nova aplicação de função sob a sua subscrição. Expandir **funções** , clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) em **HttpExample** , e, em seguida, escolha **URL de função copiar**.

    ![Copie o URL de função para o novo gatilho HTTP](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Cole este URL para o pedido HTTP na barra de endereços do seu navegador, adicione o `name` string de consulta quanto ao final deste URL `?name=Functions` e, em seguida, execute o pedido. O URL que chama a função acionada por HTTP deve estar no seguinte formato:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/httpexample?name=Functions
    ```

    O exemplo a seguir mostra a resposta no navegador ao pedido remoto GET devolvido pela função:

    ![Resposta da função no browser](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
