---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "67183980"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, reset ou parar a sessão

Para parar a sessão temporariamente, pode invocar. `Stop()` Ao fazê-lo, impedirá qualquer observador e processamento de ambiente, mesmo que invoque o ProcessFrame(). Em seguida, `Start()` pode invocar para retomar o processamento. Ao retomar, os dados ambientais já captados na sessão são mantidos.
