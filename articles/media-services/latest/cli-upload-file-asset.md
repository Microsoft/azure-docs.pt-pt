---
title: Exemplo do Script da CLI do Azure - Carregar um ficheiro para um contentor | Microsoft Docs
description: Este artigo mostra como usar o script Azure CLI para carregar um ficheiro local para um recipiente de armazenamento.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: d0a28bafef887bf0c40aa88eba1873f82cf36fd7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346086"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>Exemplo da CLI: Carregar um ficheiro local para um contentor 

Neste artigo, o script da CLI do Azure mostra como carregar um ficheiro local para um contentor de armazenamento.

## <a name="prerequisites"></a>Pré-requisitos 

* [Criar uma conta de Media Services.](create-account-cli-how-to.md)
* Rever [os ativos de gestão.](manage-asset-concept.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

```Azure CLI 
#!/bin/bash
# Update the following variables for your own settings:
storageAccountName=build2018storage
assetContainer="asset-4c834446-7e55-4760-9a25-f2d4fb1f4657"
localFile="..\Media\ignite-short.mp4"
blobName="ignite-short.mp4"
sasToken="?sv=2015-07-08&sr=c&sig=u1uy9OIeXnZUEN62hE0bDgg%2FPXYgRDNGnQxE%2BSi51dM%3D&se=2018-04-29T18:42:02Z&sp=rwl"
# Use the az storage modules to upload a local file to the container using the SAS URL from previous step.
# If you are logged in already to the subscription with access to the storage account, you do not need to use the --sas-token at all. Just eliminate it below.
# The container name is in the SAS URL path, and should be set with the -c option.
# Use the -f option to point to a local file on your machine.
# Use the -n option to name the blob in storage.
# Use the --account-name option to point to the storage account name to use 
# Use the --sas-token option to place the SAS token after the query string from previous step. 
# NOTE that the SAS Token is only good for up to 24 hours max. 
#
az storage blob upload \
    -c $assetContainer \
    -f $localFile \
    -n $blobName \
    --account-name $storageAccountName \
    --sas-token $sasToken \
echo "press  [ENTER]  to continue."
read continue
```

## <a name="next-steps"></a>Passos seguintes

[Visão geral dos Serviços de Media](media-services-overview.md)
