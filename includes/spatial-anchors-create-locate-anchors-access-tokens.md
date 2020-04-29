---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183954"
---
### <a name="access-tokens"></a>Fichas de acesso

Os Tokens de Acesso são um método mais robusto para autenticar com âncoras espaciais Azure. Especialmente quando prepara a sua candidatura para uma implantação de produção. O resumo desta abordagem é criar um serviço back-end com o que a sua aplicação de cliente possa autenticar de forma segura. As suas interfaces de serviço de back-end com AAD em tempo de execução e com o Serviço de Token Seguro de Âncoras Espaciais Azure para solicitar um Token de Acesso. Esta ficha é então entregue à aplicação do cliente e utilizada no SDK para autenticar com âncoras espaciais Azure.
