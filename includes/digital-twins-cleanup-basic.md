---
author: baanders
description: incluir arquivo para limpeza de uma instância básica de Gémeos Digitais Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557308"
---
Se já não necessitar dos recursos criados neste tutorial, siga estes passos para eliminá-los.

Utilizando [a Azure Cloud Shell,](https://shell.azure.com)pode eliminar todos os recursos Azure num grupo de recursos com o comando de eliminação do [grupo AZ.](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) Este comando remove o grupo de recursos e a instância Azure Digital Twins.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados.

Abra o Azure Cloud Shell e execute o seguinte comando para eliminar o grupo de recursos e tudo o que contém.

```azurecli-interactive
az group delete --name <your-resource-group>
```