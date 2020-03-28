---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "67183970"
---
Não é possível atualizar a localização de uma âncora uma vez que tenha sido criada no serviço - tem de criar uma nova âncora e apagar a antiga para rastrear uma nova posição.

Se não precisar de localizar uma âncora para atualizar as `GetAnchorPropertiesAsync()` suas propriedades, `CloudSpatialAnchor` pode utilizar o método, que devolve um objeto com propriedades.
