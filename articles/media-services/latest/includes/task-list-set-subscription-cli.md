---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: ffd053b98a54d3e23eec62427f5c3d82df58954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800075"
---
<!-- List and set subscriptions -->

1. Obtenha uma lista das subscrições com o comando [az account list](/cli/azure/account#az_account_list):

    ```
    az account list --output table
    ```

2. Utilize `az account set` com o nome ou o ID da subscrição para a qual pretende mudar.

    ```
    az account set --subscription "My Demos"
    ```
