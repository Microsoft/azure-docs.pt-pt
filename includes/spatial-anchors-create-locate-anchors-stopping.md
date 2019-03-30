---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632722"
---
## <a name="pause-reset-or-stop-the-session"></a>Colocar em pausa, repor ou parar a sessão

Para parar a sessão temporariamente, pode invocar `Stop()`. Se o fizer, irá parar todos os observadores e processamento de ambiente, mesmo que invoca ProcessFrame(). Em seguida, pode invocar `Start()` para retomar o processamento. Quando a retomar, dados de ambiente já capturados na sessão são mantidos.
