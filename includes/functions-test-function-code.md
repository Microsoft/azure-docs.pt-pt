---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 44eb25be12b908c8f951cb20948068da3bfc2928
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203178"
---
## <a name="test"></a>Verificar a função no Azure

Use a rotação para verificar a função implantada. Usando a URL, incluindo a chave de função, que você copiou da etapa anterior, acrescente a cadeia de `&name=<yourname>` caracteres de consulta à URL.

![usando a rotação para chamar a função no Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Você também pode colar a URL copiada, incluindo a tecla de função, na barra de endereços do seu navegador da Web. Novamente, acrescente a cadeia de `&name=<yourname>` caracteres de consulta à URL antes de executar a solicitação.

![Usando um navegador da Web para chamar a função.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
