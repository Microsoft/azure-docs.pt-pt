---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006489"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, reset ou parar a sessão

Para parar a sessão temporariamente, pode invocar. `Stop()` Ao fazê-lo, impedirá qualquer observador e `ProcessFrame()`processamento de ambiente, mesmo que invoque . Em seguida, `Start()` pode invocar para retomar o processamento. Ao retomar, os dados ambientais já captados na sessão são mantidos.
