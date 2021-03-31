---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93425197"
---
## <a name="invoke-the-function-on-azure"></a>Invocar a função em Azure

Como a sua função utiliza um gatilho HTTP, invoca-o fazendo um pedido HTTP ao seu URL no navegador ou com uma ferramenta como o curl. 

# <a name="browser"></a>[Browser](#tab/browser)

Copie o **URL completo de invocação** mostrado na saída do comando de publicação numa barra de endereços do navegador, anexando o parâmetro de consulta `&name=Functions` . O navegador deve apresentar uma saída semelhante à de quando executou a função localmente.

![A saída da função executada no Azure num browser](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Corra [`curl`](https://curl.haxx.se/) com o URL **invocado,** anexando o parâmetro `&name=Functions` . A saída do comando deve ser o texto"Olá Funções".

![A saída da função executada em Azure usando o caracol](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
