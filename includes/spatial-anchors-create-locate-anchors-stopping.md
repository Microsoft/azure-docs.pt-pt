---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006489"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, reset ou parar a sessão

Para interromper a sessão temporariamente, pode invocar `Stop()` . Ao fazê-lo, impedirá qualquer observador e processamento de ambiente, mesmo que invoque `ProcessFrame()` . Em seguida, pode invocar `Start()` para retomar o processamento. Ao retomar, os dados ambientais já capturados na sessão são mantidos.
