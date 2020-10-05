---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "80056640"
---
1. Para executar a sua função, prima F5 em Visual Studio. Pode ser necessário ativar uma exceção de firewall para que as ferramentas possam lidar com pedidos HTTP. Os níveis de autorização nunca são aplicados quando se executa uma função local.

2. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Tempo de execução local do Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Cole o URL do pedido HTTP na barra de endereço do browser. Anexar o fio de consulta `?name=<YOUR_NAME>` a este URL e executar o pedido. A imagem a seguir mostra a resposta no navegador ao pedido GET local devolvido pela função: 

    ![Resposta da função localhost no browser](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Para parar de depurar, prima Shift+F5 em Visual Studio.