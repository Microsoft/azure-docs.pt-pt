---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183970"
---
Não é possível atualizar a localização de uma âncora depois de este ter sido criado no serviço - tem de criar uma nova âncora e eliminar os antigo para controlar uma nova posição.

Se não precisa localizar uma âncora para atualizar as respetivas propriedades, pode utilizar o `GetAnchorPropertiesAsync()` método, que retorna um `CloudSpatialAnchor` objeto com propriedades.
