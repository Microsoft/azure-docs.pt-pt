---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
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
