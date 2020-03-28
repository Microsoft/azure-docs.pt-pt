---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67184063"
---
## <a name="clean-up-deployment"></a>Limpar a implementação 

Após a execução da amostra do script, o comando de seguimento pode ser usado para remover o grupo de recursos, azure Cache para a instância Redis, e quaisquer recursos relacionados no grupo de recursos.

```azurecli
az group delete --name contosoGroup
```