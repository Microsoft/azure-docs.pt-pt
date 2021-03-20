---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88602290"
---
<!--Create a media services account -->

O seguinte comando da CLI do Azure cria uma nova conta dos Serviços de Multimédia. Pode substituir os seguintes valores: `amsaccount` `storageaccountforams` (deve corresponder ao valor que deu para a sua conta de armazenamento) e `amsResourceGroup` (deve corresponder ao valor que deu para o grupo de recursos).  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```