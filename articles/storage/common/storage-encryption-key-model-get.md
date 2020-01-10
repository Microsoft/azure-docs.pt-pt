---
title: Determinar qual modelo de chave de criptografia está em uso para a conta de armazenamento
titleSuffix: Azure Storage
description: Use portal do Azure, PowerShell ou CLI do Azure para verificar como as chaves de criptografia estão sendo gerenciadas para a conta de armazenamento. As chaves podem ser gerenciadas pela Microsoft (o padrão) ou pelo cliente. As chaves gerenciadas pelo cliente devem ser armazenadas em Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3806fead9226978c277e87f3d97b14ee38d9552d
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665414"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Determinar qual modelo de chave de criptografia de armazenamento do Azure está em uso para a conta de armazenamento

Os dados em sua conta de armazenamento são automaticamente criptografados pelo armazenamento do Azure. A criptografia de armazenamento do Azure oferece duas opções para gerenciar chaves de criptografia no nível da conta de armazenamento:

- **Chaves gerenciadas pela Microsoft.** Por padrão, a Microsoft gerencia as chaves usadas para criptografar sua conta de armazenamento.
- **Chaves gerenciadas pelo cliente.** Opcionalmente, você pode optar por gerenciar chaves de criptografia para sua conta de armazenamento. As chaves gerenciadas pelo cliente devem ser armazenadas em Azure Key Vault.

Além disso, você pode fornecer uma chave de criptografia no nível de uma solicitação individual para algumas operações de armazenamento de BLOBs. Quando uma chave de criptografia é especificada na solicitação, essa chave substitui a chave de criptografia que está ativa na conta de armazenamento. Para obter mais informações, consulte [especificar uma chave fornecida pelo cliente em uma solicitação para o armazenamento de BLOBs](../blobs/storage-blob-customer-provided-key.md).

Para obter mais informações sobre chaves de criptografia, consulte [criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Verificar o modelo de chave de criptografia para a conta de armazenamento

Para determinar se uma conta de armazenamento está usando chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente para criptografia, use uma das abordagens a seguir.

# <a name="azure-portaltabportal"></a>[Portal do Azure](#tab/portal)

Para verificar o modelo de criptografia para a conta de armazenamento usando o portal do Azure, siga estas etapas:

1. No portal do Azure, navegue para a sua conta de armazenamento.
1. Selecione a configuração de **criptografia** e anote a configuração.

A imagem a seguir mostra uma conta de armazenamento em que as chaves gerenciadas pelo cliente estão em uso para criptografia:

![Captura de tela mostrando a configuração de chave de criptografia no portal do Azure](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Para verificar o modelo de criptografia para a conta de armazenamento usando o PowerShell, chame o comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) e, em seguida, verifique a propriedade **keySource** da conta.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Se o valor da propriedade **keySource** for `Microsoft.Storage`, a conta será criptografada com chaves gerenciadas pela Microsoft. Se o valor da propriedade **keySource** for `Microsoft.Keyvault`, a conta será criptografada com chaves gerenciadas pelo cliente.

# <a name="azure-clitabcli"></a>[CLI do Azure](#tab/cli)

Para verificar o modelo de criptografia para a conta de armazenamento usando CLI do Azure, chame o comando [AZ Storage Account show](/cli/azure/storage/account#az-storage-account-show) e, em seguida, verifique a propriedade **keySource** da conta.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Se o valor da propriedade **keySource** for `Microsoft.Storage`, a conta será criptografada com chaves gerenciadas pela Microsoft. Se o valor da propriedade **keySource** for `Microsoft.Keyvault`, a conta será criptografada com chaves gerenciadas pelo cliente.

---

## <a name="next-steps"></a>Passos seguintes

[Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md)
