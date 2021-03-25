---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: f0d0322f6f5f14b94a67285fe8688d72c941b3a4
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105246"
---
<!-- ### Create a storage account -->

Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia. Para obter mais informações sobre a forma como as contas de armazenamento são utilizadas nos Serviços de Multimédia, veja [Storage accounts](../storage-account-concept.md) (Contas de armazenamento).

Tem de ter uma conta de armazenamento **Principal** e pode ter qualquer número de contas de armazenamento **Secundárias** associadas à conta dos Serviços de Multimédia. Os Serviços de Multimédia suportam contas para **Fins gerais v2** (GPv2) ou **Fins gerais v1** (GPv1). As contas apenas de blobs não são permitidas como **Principais**. Se quiser saber mais sobre as contas de armazenamento, veja [Opções de contas de Armazenamento do Azure](../../../storage/common/storage-account-overview.md). 

Neste exemplo, criamos uma conta LRS Standard para Fins Gerais v2. Se quiser experimentar contas de armazenamento, use `--sku Standard_LRS` . No entanto, ao escolher um SKU para produção deve considerar, `--sku Standard_RAGRS` que fornece replicação geográfica para a continuidade do negócio. Para mais informações, consulte [as contas de armazenamento.](/cli/azure/storage/account)

O comando seguinte cria uma conta de Armazenamento que será associada à conta dos Serviços de Multimédia. No script abaixo, substitua `storageaccountforams` o seu próprio nome unqiue por um comprimento inferior a 24 caracteres. `amsResourceGroup` deve corresponder ao valor que deu para o grupo de recursos no passo anterior.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
