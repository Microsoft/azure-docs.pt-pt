---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183954"
---
### <a name="access-tokens"></a>Tokens de acesso

Tokens de acesso são um método mais robusto para autenticar com as âncoras espaciais do Azure. Especialmente como preparar a sua aplicação para uma implementação de produção. O resumo desta abordagem é configurar um serviço de back-end que a aplicação cliente pode autenticar em segurança com. As interfaces de serviço de back-end com o AAD no tempo de execução e com o serviço do Azure geográficos as âncoras de proteger o Token para pedir um Token de acesso. Este token é, em seguida, entregue para a aplicação cliente e utilizado no SDK para autenticar com as âncoras espaciais do Azure.
