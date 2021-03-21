---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070276"
---
Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Utilize o [comando de criação do grupo az](/cli/azure/group#az_group_create) para criar um grupo de recursos chamado *myResourceGroup* na localização *leste.*

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
