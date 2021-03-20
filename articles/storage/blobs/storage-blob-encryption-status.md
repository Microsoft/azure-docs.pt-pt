---
title: Verifique o estado de encriptação de uma bolha - Azure Storage
description: Aprenda a usar o portal Azure, PowerShell ou Azure CLI para verificar se uma determinada bolha está encriptada. Se uma bolha não estiver encriptada, aprenda a usar o AzCopy para forçar a encriptação descarregando e re-carregando a bolha.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ee54357250e3f31ef9db633d933d897fff362f48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878566"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Verifique o estado de encriptação de uma bolha

Cada blob de bloco, blob de apêndice ou blob de página que foi escrito para Azure Storage depois de 20 de outubro de 2017 é encriptado com encriptação de armazenamento Azure. As manchas criadas antes desta data continuam a ser encriptadas por um processo de fundo.

Este artigo mostra como determinar se uma determinada bolha foi encriptada.

## <a name="check-a-blobs-encryption-status"></a>Verifique o estado de encriptação de uma bolha

Utilize o portal Azure, PowerShell ou Azure CLI para determinar se uma bolha é encriptada sem código.

### <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para utilizar o portal Azure para verificar se uma bolha foi encriptada, siga estes passos:

1. No portal do Azure, navegue para a sua conta de armazenamento.
1. Selecione **Recipientes** para navegar para uma lista de contentores na conta.
1. Localize a bolha e apresente o **separador Visão Geral.**
1. Ver a propriedade **encriptada do Servidor.** Se **verdadeiro**, como mostrado na imagem seguinte, então a bolha é encriptada. Note que as propriedades da bolha também incluem a data e a hora que a bolha foi criada.

    ![Screenshot mostrando como verificar propriedade encriptada do Servidor no portal Azure](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Para utilizar o PowerShell para verificar se uma bolha foi encriptada, verifique a propriedade **isServerEncrypted** da blob. Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Para determinar quando a bolha foi criada, verifique o valor da propriedade **Criada:**

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para utilizar o CLI do Azure para verificar se uma bolha foi encriptada, verifique a propriedade **IsServerEncrypted** da blob. Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Para determinar quando a bolha foi criada, verifique o valor do **imóvel criado.**

---

## <a name="force-encryption-of-a-blob"></a>Encriptação de força de uma bolha

Se uma bolha que foi criada antes de 20 de outubro de 2017 ainda não foi encriptada pelo processo de fundo, pode forçar a encriptação a ocorrer imediatamente, descarregando e re-carregando a bolha. Uma maneira simples de fazer isto é com a AzCopy.

Para descarregar uma bolha para o seu sistema de ficheiros local com AzCopy, utilize a seguinte sintaxe:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Para reessarcar a bolha para Azure Storage com AzCopy, utilize a seguinte sintaxe:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Para obter mais informações sobre a utilização do AzCopy para copiar dados de bolhas, consulte [os dados de Transferência com armazenamento AzCopy e Blob](../common/storage-use-azcopy-v10.md#transfer-data).

## <a name="next-steps"></a>Passos seguintes

[Azure Storage encryption for data at rest](../common/storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
