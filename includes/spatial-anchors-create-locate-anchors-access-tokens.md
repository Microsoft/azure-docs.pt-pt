---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183954"
---
### <a name="access-tokens"></a>Tokens de Acesso

Os Tokens de acesso são um método mais robusto para autenticar com âncoras espaciais Azure. Especialmente quando prepara a sua candidatura para uma implantação de produção. O resumo desta abordagem é criar um serviço back-end com o qual a sua aplicação do cliente possa autenticar de forma segura. O seu serviço back-end interfaces com a AAD em tempo de execução e com o Serviço de Token Seguro de Âncoras Espaciais Azure para solicitar um Token de acesso. Este token é então entregue à aplicação do cliente e usado no SDK para autenticar com âncoras espaciais Azure.
