---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "67183972"
---
## <a name="update-properties"></a>Atualizar propriedades

Para atualizar as propriedades de uma âncora, utilize o `UpdateAnchorProperties()` método. Se dois ou mais dispositivos tentarem atualizar propriedades para a mesma âncora ao mesmo tempo, usamos um modelo de concordância otimista. O que significa que a primeira escrita vai ganhar.  Todas as outras escritas terão um erro de "Concurrency": seria necessária uma atualização das propriedades antes de tentar novamente.
