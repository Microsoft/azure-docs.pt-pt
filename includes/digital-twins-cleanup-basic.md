---
author: baanders
description: incluir arquivo para limpeza de uma instância básica de Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 29a07ffa917153c0cb062d34e2807d43d039a373
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494700"
---
Se já não necessitar dos recursos criados neste tutorial, siga estes passos para eliminá-los.

Utilizando o [Azure Cloud Shell,](https://shell.azure.com)pode eliminar todos os recursos Azure num grupo de recursos com o comando de eliminação do [grupo AZ.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) Isto remove o grupo de recursos e a instância Azure Digital Twins.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

Abra uma Azure Cloud Shell e execute o seguinte comando para eliminar o grupo de recursos e tudo o que contém.

```azurecli-interactive
az group delete --name <your-resource-group>
```