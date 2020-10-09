---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006489"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, reset ou parar a sessão

Para interromper a sessão temporariamente, pode invocar `Stop()` . Ao fazê-lo, impedirá qualquer observador e processamento de ambiente, mesmo que invoque `ProcessFrame()` . Em seguida, pode invocar `Start()` para retomar o processamento. Ao retomar, os dados ambientais já capturados na sessão são mantidos.
