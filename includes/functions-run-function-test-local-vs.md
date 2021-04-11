---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701409"
---
1. Para executar a sua função, prima <kbd>F5</kbd> em Visual Studio. Pode ser necessário ativar uma exceção de firewall para que as ferramentas possam lidar com pedidos HTTP. Os níveis de autorização nunca são aplicados quando se executa uma função local.

2. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Tempo de execução local do Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Cole o URL do pedido HTTP na barra de endereço do browser. Anexar o fio de consulta `?name=<YOUR_NAME>` a este URL e executar o pedido. A imagem a seguir mostra a resposta no navegador ao pedido GET local devolvido pela função: 

    ![Resposta da função localhost no browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Para parar de depurar, prima <kbd>Shift</kbd> + <kbd>F5</kbd> em Estúdio Visual.
