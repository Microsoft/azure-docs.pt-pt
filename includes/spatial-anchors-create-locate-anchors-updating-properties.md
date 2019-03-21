---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907760"
---
## <a name="update-properties"></a>Atualizar propriedades

Para atualizar as propriedades numa âncora, use o método UpdateAnchorProperties. Se dois ou mais dispositivos tentarem atualizar as propriedades para a mesma âncora ao mesmo tempo, vamos utilizar um modelo de simultaneidade otimista. Isto significa que a primeira operação de escrita ganhará.  Todas as outras escritas obterá um erro de "Simultaneidade": uma atualização das propriedades seria necessário antes de tentar novamente.
