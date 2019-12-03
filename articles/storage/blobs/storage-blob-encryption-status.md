---
title: Verificar o status de criptografia de um blob-armazenamento do Azure
description: Saiba como usar portal do Azure, PowerShell ou CLI do Azure para verificar se um determinado blob está criptografado. Se um BLOB não for criptografado, saiba como usar o AzCopy para forçar a criptografia baixando e recarregando o blob.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707584"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Verificar o status de criptografia de um blob

Todo blob de blocos, BLOB de acréscimo ou BLOB de páginas que foi gravado no armazenamento do Azure após 20 de outubro de 2017 é criptografado com a criptografia de armazenamento do Azure. Os BLOBs criados antes dessa data continuam a ser criptografados por um processo em segundo plano.

Este artigo mostra como determinar se um determinado blob foi criptografado.

## <a name="check-a-blobs-encryption-status"></a>Verificar o status de criptografia de um blob

Use o portal do Azure, o PowerShell ou o CLI do Azure para determinar se um blob é criptografado sem código.

### <a name="azure-portaltabportal"></a>[Portal do Azure](#tab/portal)

Para usar o portal do Azure para verificar se um blob foi criptografado, siga estas etapas:

1. No portal do Azure, navegue para a sua conta de armazenamento.
1. Selecione **contêineres** para navegar para uma lista de contêineres na conta.
1. Localize o blob e exiba sua guia **visão geral** .
1. Exiba a propriedade **criptografada do servidor** . Se for **true**, conforme mostrado na imagem a seguir, o blob será criptografado. Observe que as propriedades do blob também incluem a data e a hora em que o blob foi criado.

    ![Captura de tela mostrando como verificar a propriedade criptografada do servidor no portal do Azure](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Para usar o PowerShell para verificar se um blob foi criptografado, verifique a propriedade **IsServerEncrypted** do blob. Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Para determinar quando o blob foi criado, verifique o valor da propriedade **criada** :

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-clitabcli"></a>[CLI do Azure](#tab/cli)

Para usar CLI do Azure para verificar se um blob foi criptografado, verifique a propriedade **IsServerEncrypted** do blob. Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Para determinar quando o blob foi criado, verifique o valor da propriedade **criada** .

---

## <a name="force-encryption-of-a-blob"></a>Forçar a criptografia de um blob

Se um blob criado antes de 20 de outubro de 2017 ainda não tiver sido criptografado pelo processo em segundo plano, você poderá forçar a criptografia a ocorrer imediatamente baixando e recarregando o blob. Uma maneira simples de fazer isso é com o AzCopy.

Para baixar um blob em seu sistema de arquivos local com o AzCopy, use a seguinte sintaxe:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Para carregar novamente o blob no armazenamento do Azure com o AzCopy, use a seguinte sintaxe:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Para obter mais informações sobre como usar o AzCopy para copiar dados de BLOB, consulte [transferir dados com o armazenamento de BLOBs e AzCopy](../common/storage-use-azcopy-blobs.md).

## <a name="next-steps"></a>Passos seguintes

[Criptografia de armazenamento do Azure para dados em repouso](../common/storage-service-encryption.md)
