---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070276"
---
Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Utilize o [comando de criação do grupo az](/cli/azure/group#az_group_create) para criar um grupo de recursos chamado *myResourceGroup* na localização *leste.*

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
