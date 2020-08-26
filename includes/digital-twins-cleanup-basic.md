---
author: baanders
description: incluir arquivo para limpeza de uma instância básica de Azure Digital Twins e registo de aplicativos
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 45d6a806e94ceca9574b0ed5f73c2b87ef438438
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891505"
---
Se já não necessitar dos recursos criados neste tutorial, siga estes passos para eliminá-los.

Utilizando o [Azure Cloud Shell,](https://shell.azure.com)pode eliminar todos os recursos Azure num grupo de recursos com o comando de eliminação do [grupo AZ.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) Isto remove o grupo de recursos e a instância Azure Digital Twins.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

Abra uma Azure Cloud Shell e execute o seguinte comando para eliminar o grupo de recursos e tudo o que contém.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Em seguida, elimine o registo de aplicações Azure Ative Directory que criou para a sua aplicação cliente com este comando:

```azurecli-interactive
az ad app delete --id <your-application-ID>
```