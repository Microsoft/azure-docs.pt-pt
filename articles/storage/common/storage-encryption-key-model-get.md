---
title: Determine qual o modelo chave de encriptação que está a ser utilizado para a conta de armazenamento
titleSuffix: Azure Storage
description: Utilize o portal Azure, PowerShell ou Azure CLI para verificar como as chaves de encriptação estão a ser geridas para a conta de armazenamento. As teclas podem ser geridas pela Microsoft (predefinido), ou pelo cliente. As chaves geridas pelo cliente devem ser armazenadas no Cofre de Chaves Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409865"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Determine qual o modelo de chave de encriptação do Armazenamento Azure está a ser utilizado para a conta de armazenamento

Os dados na sua conta de armazenamento são automaticamente encriptados pelo Armazenamento Azure. A encriptação Azure Storage oferece duas opções para gerir chaves de encriptação ao nível da conta de armazenamento:

- **Chaves geridas pela Microsoft.** Por padrão, a Microsoft gere as chaves utilizadas para encriptar a sua conta de armazenamento.
- **Chaves geridas pelo cliente.** Pode optar opcionalmente por gerir as chaves de encriptação para a sua conta de armazenamento. As chaves geridas pelo cliente devem ser armazenadas no Cofre de Chaves Azure.

Além disso, pode fornecer uma chave de encriptação ao nível de um pedido individual para algumas operações de armazenamento blob. Quando uma chave de encriptação é especificada no pedido, essa chave substitui a chave de encriptação que está ativa na conta de armazenamento. Para mais informações, consulte [Especifique uma chave fornecida pelo cliente num pedido de armazenamento Blob](../blobs/storage-blob-customer-provided-key.md).

Para obter mais informações sobre chaves de encriptação, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Verifique o modelo chave de encriptação para a conta de armazenamento

Para determinar se uma conta de armazenamento está a usar chaves geridas pela Microsoft ou chaves geridas pelo cliente para encriptação, utilize uma das seguintes abordagens.

# <a name="azure-portal"></a>[Portal Azure](#tab/portal)

Para verificar o modelo de encriptação da conta de armazenamento utilizando o portal Azure, siga estes passos:

1. No portal do Azure, navegue para a sua conta de armazenamento.
1. Selecione a definição de **encriptação** e note a definição.

A imagem seguinte mostra uma conta de armazenamento que é encriptada com chaves geridas pela Microsoft:

![Ver conta encriptada com chaves geridas pela Microsoft](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

E a seguinte imagem mostra uma conta de armazenamento que é encriptada com chaves geridas pelo cliente:

![Screenshot mostrando a definição da chave de encriptação no portal Azure](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o modelo de encriptação da conta de armazenamento utilizando o PowerShell, ligue para o comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) e, em seguida, verifique a propriedade **KeySource** para obter a conta.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Se o valor da propriedade `Microsoft.Storage` **KeySource** for , então a conta é encriptada com chaves geridas pela Microsoft. Se o valor da propriedade `Microsoft.Keyvault` **KeySource** for , então a conta é encriptada com chaves geridas pelo cliente.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Para verificar o modelo de encriptação da conta de armazenamento utilizando o Azure CLI, ligue para o comando da conta de [armazenamento az](/cli/azure/storage/account#az-storage-account-show) e, em seguida, verifique a propriedade **keySource** para a conta.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Se o valor da propriedade `Microsoft.Storage` **keySource** for , então a conta é encriptada com chaves geridas pela Microsoft. Se o valor da propriedade `Microsoft.Keyvault` **keySource** for , então a conta é encriptada com chaves geridas pelo cliente.

---

## <a name="next-steps"></a>Passos seguintes

- [Encriptação azure storage para dados em repouso](storage-service-encryption.md)
- [Utilize chaves geridas pelo cliente com cofre de chaves Azure para gerir encriptação de armazenamento azure](encryption-customer-managed-keys.md)