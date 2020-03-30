---
title: Verifique o estado de encriptação de uma bolha - Armazenamento Azure
description: Saiba como utilizar o portal Azure, PowerShell ou Azure CLI para verificar se uma determinada bolha está encriptada. Se uma bolha não estiver encriptada, aprenda a usar o AzCopy para forçar a encriptação, baixando e recarregando a bolha.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707584"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Verifique o estado de encriptação de uma bolha

Cada blob de bloco, append blob ou blob de página que foi escrito para O Armazenamento Azure após 20 de outubro de 2017 é encriptado com encriptação de Armazenamento Azure. As bolhas criadas antes desta data continuam a ser encriptadas por um processo de fundo.

Este artigo mostra como determinar se uma dada bolha foi encriptada.

## <a name="check-a-blobs-encryption-status"></a>Verifique o estado de encriptação de uma bolha

Utilize o portal Azure, PowerShell ou Azure CLI para determinar se uma bolha é encriptada sem código.

### <a name="azure-portal"></a>[Portal Azure](#tab/portal)

Para utilizar o portal Azure para verificar se uma bolha foi encriptada, siga estes passos:

1. No portal do Azure, navegue para a sua conta de armazenamento.
1. Selecione **Recipientes** para navegar para uma lista de contentores na conta.
1. Localize a bolha e exiba o seu separador **overview.**
1. Consulte a propriedade **Encriptada do Servidor.** Se **True**, como mostra a seguinte imagem, então a bolha é encriptada. Note que as propriedades da bolha também incluem a data e hora que a bolha foi criada.

    ![Screenshot mostrando como verificar propriedade encriptada do servidor no portal Azure](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Para utilizar o PowerShell para verificar se uma bolha foi encriptada, verifique a propriedade **IsServerEncrypted** da blob. Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

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

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Para utilizar o Azure CLI para verificar se uma bolha foi encriptada, verifique a propriedade **IsServerEncrypted** da blob. Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Para determinar quando a bolha foi criada, verifique o valor da propriedade **criada.**

---

## <a name="force-encryption-of-a-blob"></a>Encriptação de força de uma bolha

Se uma bolha que foi criada antes de 20 de outubro de 2017 ainda não foi encriptada pelo processo de fundo, pode forçar a encriptação a ocorrer imediatamente, descarregando e recarregando a bolha. Uma maneira simples de fazer isto é com a AzCopy.

Para descarregar uma bolha para o seu sistema de ficheiros local com a AzCopy, utilize a seguinte sintaxe:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Para voltar a carregar a bolha para o Armazenamento Azure com a AzCopy, utilize a seguinte sintaxe:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Para obter mais informações sobre a utilização do AzCopy para copiar dados blob, consulte [os dados de transferência com armazenamento AzCopy e Blob](../common/storage-use-azcopy-blobs.md).

## <a name="next-steps"></a>Passos seguintes

[Encriptação azure storage para dados em repouso](../common/storage-service-encryption.md)
