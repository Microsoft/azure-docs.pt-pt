---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 94df457cc93a059de3f9bb294bd1c568b095781c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94884941"
---
<!-- List and set subscriptions -->

1. Obtenha uma lista das subscrições com o comando [az account list](/cli/azure/account#az-account-list):

    ```
    az account list --output table
    ```

2. Utilize `az account set` com o nome ou o ID da subscrição para a qual pretende mudar.

    ```
    az account set --subscription "My Demos"
    ```
