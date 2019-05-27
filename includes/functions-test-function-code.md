---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132163"
---
## <a name="test"></a>Testar a função no Azure

Utilize o cURL para testar a função implementada. Com o URL que copiou no passo anterior, anexe a cadeia de consulta `&name=<yourname>` para o URL, como no exemplo seguinte:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![com o cURL para chamar a função no Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Também pode colar o URL copiado para o endereço do seu navegador da web. Novamente, anexe a cadeia de consulta `&name=<yourname>` para o URL antes de executar o pedido.

![Usando um navegador da web para chamar a função.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
