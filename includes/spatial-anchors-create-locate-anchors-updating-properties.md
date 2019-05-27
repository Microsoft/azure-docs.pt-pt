---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110606"
---
## <a name="update-properties"></a>Atualizar propriedades

Para atualizar as propriedades numa âncora, use o `UpdateAnchorProperties()` método. Se dois ou mais dispositivos tentarem atualizar as propriedades para a mesma âncora ao mesmo tempo, vamos utilizar um modelo de simultaneidade otimista. Isto significa que a primeira operação de escrita ganhará.  Todas as outras escritas obterá um erro de "Simultaneidade": uma atualização das propriedades seria necessário antes de tentar novamente.
