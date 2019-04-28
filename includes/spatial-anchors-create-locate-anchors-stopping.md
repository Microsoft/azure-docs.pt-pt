---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232600"
---
## <a name="pause-reset-or-stop-the-session"></a>Colocar em pausa, repor ou parar a sessão

Para parar a sessão temporariamente, pode invocar `Stop()`. Se o fizer, irá parar todos os observadores e processamento de ambiente, mesmo que invoca ProcessFrame(). Em seguida, pode invocar `Start()` para retomar o processamento. Quando a retomar, dados de ambiente já capturados na sessão são mantidos.
