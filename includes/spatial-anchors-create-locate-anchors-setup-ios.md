---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006434"
---
## <a name="setting-up-the-library"></a>Configuração da biblioteca

Invoque `Start()` para permitir que a sua sessão processe dados ambientais.

Para lidar com os eventos `delegate` levantados pela sua sessão, detete a propriedade da sua sessão para um objeto, como a sua vista. Este objeto deve `SSCCloudSpatialAnchorSessionDelegate` implementar o protocolo.
