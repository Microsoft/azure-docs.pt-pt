---
title: Gerir o nível de acesso de uma bolha numa conta de Armazenamento Azure
description: Saiba como alterar o nível de uma bolha numa conta de Armazenamento de GPv2 ou Blob
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 183593022c934eaf52ffe18649c23e8deced34d8
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166518"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Gerir o nível de acesso de uma bolha numa conta de Armazenamento Azure

Cada bolha tem um nível de acesso predefinido, quente, fresco ou arquivado. Uma bolha assume o nível de acesso predefinido da conta Azure Storage onde é criada. As contas Blob Storage e GPv2 expõem o atributo **Access Tier** ao nível da conta. Este atributo especifica o nível de acesso predefinido para qualquer bolha que não o tenha explicitamente definido ao nível do objeto. Para objetos com o nível definido ao nível do objeto, o nível de conta não se aplica. O nível de arquivo só pode ser aplicado ao nível do objeto. Pode alternar entre os níveis de acesso a qualquer momento seguindo os passos abaixo.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Alterar o nível de uma bolha numa conta de Armazenamento de GPv2 ou Blob

Os seguintes cenários utilizam o portal Azure ou PowerShell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos.**

1. Selecione a sua conta de armazenamento.

1. Selecione o seu recipiente e, em seguida, selecione a sua bolha.

1. Nas **propriedades Blob**, selecione **Alterar o nível**.

1. Selecione o nível de acesso **Hot,** **Cool** ou **Archive.** Se a sua bolha estiver atualmente arquivada e pretender reidratar para um nível online, também pode selecionar uma Prioridade Rehidrata de **Standard** ou **Alta**.

1. **Selecione Guarde** na parte inferior.

![Alterar nível blob no portal Azure](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O seguinte script PowerShell pode ser usado para alterar o nível blob. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. A `$containerName` variável deve ser inicializada com o nome do seu recipiente. A `$blobName` variável deve ser inicializada com o seu nome blob.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Passos seguintes

- [Como gerir o nível de acesso à conta padrão de uma conta de Armazenamento Azure](../common/manage-account-default-access-tier.md)
- [Saiba mais sobre a reidratação de dados blob a partir do nível de arquivo](storage-blob-rehydration.md)
- [Verificar os preços das camadas de armazenamento frequente, esporádica e de arquivo nas contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
