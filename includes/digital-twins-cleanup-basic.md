---
author: baanders
description: incluir arquivo para limpeza de uma instância básica de Azure Digital Twins e registo de aplicativos
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: e8f167555434338416381aaf8978a3d551686fb2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606465"
---
Se já não necessitar dos recursos criados neste tutorial, siga estes passos para eliminá-los.

Utilizando o [Azure Cloud Shell,](https://shell.azure.com)pode eliminar todos os recursos Azure num grupo de recursos com o comando de eliminação do [grupo AZ.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) Isto remove o grupo de recursos e a instância Azure Digital Twins.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

Abra uma Azure Cloud Shell e execute o seguinte comando para eliminar o grupo de recursos e tudo o que contém.

```azurecli
az group delete --name <your-resource-group>
```

Em seguida, elimine o registo de aplicações Azure Ative Directory que criou para a sua aplicação cliente com este comando:

```azurecli
az ad app delete --id <your-application-ID>
```