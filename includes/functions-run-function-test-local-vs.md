---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80056640"
---
1. Para executar a sua função, prima F5 no Estúdio Visual. Pode ser necessário permitir uma exceção à firewall para que as ferramentas possam lidar com pedidos HTTP. Os níveis de autorização nunca são aplicados quando se executa uma função localmente.

2. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Tempo de execução local do Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Cole o URL do pedido HTTP na barra de endereço do browser. Anexar a corda `?name=<YOUR_NAME>` de consulta a este URL e executar o pedido. A imagem seguinte mostra a resposta no navegador ao pedido de GET local devolvido pela função: 

    ![Resposta da função localhost no browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Para parar de depurar, prima Shift+F5 no Estúdio Visual.