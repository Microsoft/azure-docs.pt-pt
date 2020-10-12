---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602290"
---
<!--Create a media services account -->

O seguinte comando da CLI do Azure cria uma nova conta dos Serviços de Multimédia. Pode substituir os seguintes valores: `amsaccount` `storageaccountforams` (deve corresponder ao valor que deu para a sua conta de armazenamento) e `amsResourceGroup` (deve corresponder ao valor que deu para o grupo de recursos).  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```