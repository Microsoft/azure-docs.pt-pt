---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/25/2018
ms.author: v-junlch
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607749"
---
## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o comando de seguir pode ser utilizado para remover o grupo de recursos, a Cache do Azure para a instância de Redis e a quaisquer recursos relacionados no grupo de recursos.

```azurecli
az group delete --name contosoGroup
```

<!-- ms.date: 12/25/2018 -->