---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602480"
---
<!-- Create a resource group -->

Utilize o seguinte comando para criar um grupo de recursos. Selecione a região geográfica que será usada para armazenar os registos de mídia e metadados para a sua conta de Media Services. Esta região será usada para processar e transmitir os seus meios de comunicação.

```azurecli
az group create --name amsResourceGroup --location westus2
```
