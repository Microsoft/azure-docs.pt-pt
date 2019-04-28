---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232520"
---
## <a name="update-properties"></a>Atualizar propriedades

Para atualizar as propriedades numa âncora, use o `UpdateAnchorProperties()` método. Se dois ou mais dispositivos tentarem atualizar as propriedades para a mesma âncora ao mesmo tempo, vamos utilizar um modelo de simultaneidade otimista. Isto significa que a primeira operação de escrita ganhará.  Todas as outras escritas obterá um erro de "Simultaneidade": uma atualização das propriedades seria necessário antes de tentar novamente.
