---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232493"
---
Não é possível atualizar a localização de uma âncora depois de este ter sido criado no serviço - tem de criar uma nova âncora e eliminar os antigo para controlar uma nova posição.

Se não precisa localizar uma âncora para atualizar as respetivas propriedades, pode utilizar o `GetAnchorPropertiesAsync()` método, que retorna um `CloudSpatialAnchor` objeto com propriedades.
