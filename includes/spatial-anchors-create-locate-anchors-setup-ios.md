---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006434"
---
## <a name="setting-up-the-library"></a>Criação da biblioteca

Invocar `Start()` para permitir a sua sessão para processar dados ambientais.

Para lidar com os eventos levantados pela sua sessão, desajei a `delegate` propriedade da sua sessão a um objeto, como a sua vista. Este objeto deve implementar o `SSCCloudSpatialAnchorSessionDelegate` protocolo.
