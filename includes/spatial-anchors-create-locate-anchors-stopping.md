---
ms.openlocfilehash: 8e02067b32d9404a5bbdf7362b2cc32712b96250
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907780"
---
## <a name="pause-reset-or-stop-the-session"></a>Colocar em pausa, repor ou parar a sessão

Para parar a sessão temporariamente, pode invocar Stop(). Se o fizer, irá parar todos os observadores e processamento de ambiente, mesmo que invoca ProcessFrame(). Em seguida, pode invocar Start() para retomar o processamento. Quando a retomar, dados de ambiente já capturados na sessão são mantidos.
