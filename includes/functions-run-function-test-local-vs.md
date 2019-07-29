---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592888"
---
1. Para executar sua função, pressione **F5**. Talvez seja necessário habilitar uma exceção de firewall para que as ferramentas possam lidar com solicitações HTTP. Os níveis de autorização nunca são aplicados durante a execução local.

2. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Tempo de execução local do Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Cole o URL do pedido HTTP na barra de endereço do browser. Anexe a cadeia de consulta `?name=<YOUR_NAME>` a este URL e execute o pedido. O exemplo apresentado em seguida mostra a resposta no browser relativamente ao pedido GET devolvido retornado pela função: 

    ![Resposta da função localhost no browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Para parar a depuração, prima **Shift + F5**.