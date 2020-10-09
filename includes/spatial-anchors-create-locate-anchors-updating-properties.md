---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183972"
---
## <a name="update-properties"></a>Atualizar propriedades

Para atualizar as propriedades de uma âncora, utilize o `UpdateAnchorProperties()` método. Se dois ou mais dispositivos tentarem atualizar propriedades para a mesma âncora ao mesmo tempo, usamos um modelo de concordância otimista. O que significa que a primeira escrita vai ganhar.  Todas as outras escritas terão um erro de "Concurrency": seria necessária uma atualização das propriedades antes de tentar novamente.
