---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88602480"
---
<!-- Create a resource group -->

Utilize o seguinte comando para criar um grupo de recursos. Selecione a região geográfica que será usada para armazenar os registos de mídia e metadados para a sua conta de Media Services. Esta região será usada para processar e transmitir os seus meios de comunicação.

```azurecli
az group create --name amsResourceGroup --location westus2
```
