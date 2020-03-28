---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "67183972"
---
## <a name="update-properties"></a>Atualizar propriedades

Para atualizar as propriedades numa âncora, utiliza o `UpdateAnchorProperties()` método. Se dois ou mais dispositivos tentarem atualizar propriedades para a mesma âncora ao mesmo tempo, usamos um modelo de conmoeda otimista. O que significa que a primeira escrita vai ganhar.  Todas as outras escritas terão um erro "Concurrency": uma atualização das propriedades seria necessária antes de tentar novamente.
