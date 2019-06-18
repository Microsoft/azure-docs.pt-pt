---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110633"
---
Não é possível atualizar a localização de uma âncora depois de este ter sido criado no serviço - tem de criar uma nova âncora e eliminar os antigo para controlar uma nova posição.

Se não precisa localizar uma âncora para atualizar as respetivas propriedades, pode utilizar o `GetAnchorPropertiesAsync()` método, que retorna um `CloudSpatialAnchor` objeto com propriedades.
