---
title: Determinar qual o modelo chave de encriptação que está a ser utilizado para a conta de armazenamento
titleSuffix: Azure Storage
description: Utilize o portal Azure, PowerShell ou Azure CLI para verificar como as chaves de encriptação estão a ser geridas para a conta de armazenamento. As chaves podem ser geridas pela Microsoft (o padrão) ou pelo cliente. As chaves geridas pelo cliente devem ser armazenadas no Cofre da Chave Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f59e4238c983cdb336500a68c52730ae5346b1c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91612426"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Determinar qual o modelo chave de encriptação de armazenamento Azure está a ser utilizado para a conta de armazenamento

Os dados na sua conta de armazenamento são automaticamente encriptados pelo Azure Storage. A encriptação Azure Storage oferece duas opções para gerir chaves de encriptação ao nível da conta de armazenamento:

- **Chaves geridas pela Microsoft.** Por predefinição, a Microsoft gere as chaves utilizadas para encriptar a sua conta de armazenamento.
- **Chaves geridas pelo cliente.** Pode optar opcionalmente por gerir chaves de encriptação para a sua conta de armazenamento. As chaves geridas pelo cliente devem ser armazenadas no Cofre da Chave Azure.

Além disso, pode fornecer uma chave de encriptação ao nível de um pedido individual para algumas operações de armazenamento Blob. Quando uma chave de encriptação é especificada no pedido, essa chave substitui a chave de encriptação que está ativa na conta de armazenamento. Para obter mais informações, consulte [Especificar uma chave fornecida pelo cliente sobre um pedido de armazenamento blob](../blobs/storage-blob-customer-provided-key.md).

Para obter mais informações sobre as chaves de encriptação, consulte [a encriptação do Azure Storage para obter dados em repouso](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Verifique o modelo chave de encriptação para a conta de armazenamento

Para determinar se uma conta de armazenamento está a usar chaves geridas pela Microsoft ou chaves geridas pelo cliente para encriptação, utilize uma das seguintes abordagens.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para verificar o modelo de encriptação da conta de armazenamento utilizando o portal Azure, siga estes passos:

1. No portal do Azure, navegue para a sua conta de armazenamento.
1. Selecione a **definição de encriptação** e note a definição.

A imagem a seguir mostra uma conta de armazenamento encriptada com teclas geridas pela Microsoft:

![Ver conta encriptada com teclas geridas pela Microsoft](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

E a imagem a seguir mostra uma conta de armazenamento que é encriptada com chaves geridas pelo cliente:

![Screenshot mostrando a definição da chave de encriptação no portal Azure](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o modelo de encriptação da conta de armazenamento utilizando o PowerShell, ligue para o comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) e, em seguida, verifique a propriedade **KeySource** para obter a conta.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Se o valor da propriedade **KeySource** `Microsoft.Storage` for, então a conta é encriptada com as teclas geridas pela Microsoft. Se o valor da propriedade **KeySource** `Microsoft.Keyvault` for, então a conta é encriptada com chaves geridas pelo cliente.

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para verificar o modelo de encriptação da conta de armazenamento utilizando o Azure CLI, ligue para o comando da [conta de armazenamento az](/cli/azure/storage/account#az-storage-account-show) e, em seguida, verifique a propriedade **keySource** para a conta.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Se o valor da propriedade **keySource** `Microsoft.Storage` for, então a conta é encriptada com teclas geridas pela Microsoft. Se o valor da propriedade **keySource** `Microsoft.Keyvault` for, então a conta é encriptada com chaves geridas pelo cliente.

---

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [Chaves geridas pelo cliente para encriptação de armazenamento Azure](customer-managed-keys-overview.md)
