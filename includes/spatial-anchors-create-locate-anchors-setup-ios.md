---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006434"
---
## <a name="setting-up-the-library"></a>Criação da biblioteca

Invocar `Start()` para permitir a sua sessão para processar dados ambientais.

Para lidar com os eventos levantados pela sua sessão, desajei a `delegate` propriedade da sua sessão a um objeto, como a sua vista. Este objeto deve implementar o `SSCCloudSpatialAnchorSessionDelegate` protocolo.
