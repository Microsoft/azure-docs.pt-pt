---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "67183970"
---
Não é possível atualizar a localização de uma âncora uma vez criada no serviço - tem de criar uma nova âncora e apagar a antiga para rastrear uma nova posição.

Se não precisar de localizar uma âncora para atualizar as suas propriedades, pode utilizar o `GetAnchorPropertiesAsync()` método, que devolve um `CloudSpatialAnchor` objeto com propriedades.
