---
title: incluir ficheiro
description: incluir ficheiro
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 02/21/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 79af6512e9ce3d3f897be216ee3626c5d4fbcf1d
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56741269"
---
## <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

Tem primeiro de criar uma conta dos Serviços de Multimédia. Esta secção mostra o que precisa para a criação de conta com a CLI do Azure.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos ao utilizar o comando seguinte. Um grupo de recursos do Azure é um contentor lógico no qual os recursos, como as contas dos Serviços de Multimédia do Azure e as contas de Armazenamento associadas, são implementados e geridos.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia. Para obter mais informações sobre a forma como as contas de armazenamento são utilizadas nos Serviços de Multimédia, veja [Storage accounts](../articles/media-services/latest/storage-account-concept.md) (Contas de armazenamento).

Tem de ter uma conta de armazenamento **Principal** e pode ter qualquer número de contas de armazenamento **Secundárias** associadas à conta dos Serviços de Multimédia. Os Serviços de Multimédia suportam contas para **Fins gerais v2** (GPv2) ou **Fins gerais v1** (GPv1). As contas apenas de blobs não são permitidas como **Principais**. Se quiser saber mais sobre as contas de armazenamento, veja [Opções de conta de armazenamento do Azure](../articles/storage/common/storage-account-options.md). 

Neste exemplo, vamos criar uma conta de LRS padrão, v2 de fins gerais. Se quiser experimentar as contas de armazenamento, utilize `--sku Standard_LRS`. No entanto, ao escolher um SKU para produção deve considerar, `--sku Standard_RAGRS`, que fornece replicação geográfica para continuidade do negócio. Para obter mais informações, consulte [contas de armazenamento](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
O comando seguinte cria uma conta de Armazenamento que será associada à conta dos Serviços de Multimédia. No script abaixo, pode substituir `storageaccountforams` pelo seu valor. O nome da conta tem de ter menos de 24 carateres.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

O seguinte comando da CLI do Azure cria uma nova conta dos Serviços de Multimédia. Pode substituir os seguintes valores: `amsaccount`, `storageaccountforams` (têm de corresponder ao valor que forneceu para a sua conta de armazenamento) e `amsResourceGroup` (tem de corresponder ao valor que forneceu para o grupo de recursos).

```azurecli
az ams account create --name amsaccount \
  -l westus2 \
  -g amsResourceGroup --storage-account storageaccountforams
```
