---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132764"
---
## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o comando de seguir pode ser utilizado para remover o grupo de recursos, a Cache do Azure para a instância de Redis e a quaisquer recursos relacionados no grupo de recursos.

```azurecli
az group delete --name contosoGroup
```