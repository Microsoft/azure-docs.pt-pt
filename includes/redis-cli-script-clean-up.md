---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132764"
---
## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o comando de seguir pode ser utilizado para remover o grupo de recursos, a Cache do Azure para a instância de Redis e a quaisquer recursos relacionados no grupo de recursos.

```azurecli
az group delete --name contosoGroup
```