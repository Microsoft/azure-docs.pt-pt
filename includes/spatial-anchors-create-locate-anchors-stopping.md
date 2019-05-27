---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110826"
---
## <a name="pause-reset-or-stop-the-session"></a>Colocar em pausa, repor ou parar a sessão

Para parar a sessão temporariamente, pode invocar `Stop()`. Se o fizer, irá parar todos os observadores e processamento de ambiente, mesmo que invoca ProcessFrame(). Em seguida, pode invocar `Start()` para retomar o processamento. Quando a retomar, dados de ambiente já capturados na sessão são mantidos.
