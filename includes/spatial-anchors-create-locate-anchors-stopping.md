---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183980"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, reset ou parar a sessão

Para parar a sessão temporariamente, pode invocar. `Stop()` Ao fazê-lo, impedirá qualquer observador e processamento de ambiente, mesmo que invoque o ProcessFrame(). Em seguida, `Start()` pode invocar para retomar o processamento. Ao retomar, os dados ambientais já captados na sessão são mantidos.
