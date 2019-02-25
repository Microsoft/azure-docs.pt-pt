---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753105"
---
### <a name="access-tokens"></a>Tokens de acesso

Tokens de acesso são um método mais robusto para autenticar com as âncoras espaciais do Azure. Especialmente como preparar a sua aplicação para uma implementação de produção. O resumo desta abordagem é configurar um serviço de back-end que a aplicação cliente pode autenticar em segurança com. As interfaces de serviço de back-end com o AAD no tempo de execução e com o serviço de Azure geográficos âncoras STS para pedir um Token de acesso. Este token é, em seguida, entregue para a aplicação cliente e utilizado no SDK para autenticar com as âncoras espaciais do Azure.
