---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183970"
---
Não é possível atualizar a localização de uma âncora uma vez criada no serviço - tem de criar uma nova âncora e apagar a antiga para rastrear uma nova posição.

Se não precisar de localizar uma âncora para atualizar as suas propriedades, pode utilizar o `GetAnchorPropertiesAsync()` método, que devolve um `CloudSpatialAnchor` objeto com propriedades.
