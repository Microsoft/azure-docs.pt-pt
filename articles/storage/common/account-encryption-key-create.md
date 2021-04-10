---
title: Crie uma conta que suporte chaves geridas pelo cliente para mesas e filas
titleSuffix: Azure Storage
description: Saiba como criar uma conta de armazenamento que suporte a configuração de chaves geridas pelo cliente para mesas e filas. Utilize o modelo Azure CLI ou um gestor de recursos Azure para criar uma conta de armazenamento que se baseie na chave de encriptação da conta para encriptação de armazenamento Azure. Em seguida, pode configurar as chaves geridas pelo cliente para a conta.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f2bc71100a92d1811d69af31a7a3085af36f60a8
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121936"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Crie uma conta que suporte chaves geridas pelo cliente para mesas e filas

O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, o armazenamento de filas e o armazenamento da mesa utilizam uma chave que é telescópio para o serviço e gerida pela Microsoft. Também pode optar por utilizar as chaves geridas pelo cliente para encriptar os dados da fila ou da tabela. Para utilizar as chaves geridas pelo cliente com filas e tabelas, tem primeiro de criar uma conta de armazenamento que utilize uma chave de encriptação que é procurada na conta e não no serviço. Depois de ter criado uma conta que utiliza a chave de encriptação da conta para dados de fila e tabela, pode configurar chaves geridas pelo cliente para essa conta de armazenamento.

Este artigo descreve como criar uma conta de armazenamento que se baseia numa chave que é traçada na conta. Quando a conta é criada pela primeira vez, a Microsoft utiliza a chave de conta para encriptar os dados na conta e a Microsoft gere a chave. Pode configurar posteriormente as chaves geridas pelo cliente para que a conta tire partido desses benefícios, incluindo a capacidade de fornecer as suas próprias chaves, atualizar a versão chave, rodar as teclas e revogar os controlos de acesso.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Criar uma conta que usa a chave de encriptação da conta

Tem de configurar uma nova conta de armazenamento para utilizar a chave de encriptação da conta para filas e tabelas no momento em que criar a conta de armazenamento. O âmbito da chave de encriptação não pode ser alterado após a criação da conta.

A conta de armazenamento deve ser do tipo v2. Pode criar a conta de armazenamento e configurá-la para confiar na chave de encriptação da conta utilizando o Azure CLI ou um modelo de Gestor de Recursos Azure.

> [!NOTE]
> Apenas o armazenamento de fila e mesa pode ser configurado opcionalmente para encriptar dados com a chave de encriptação da conta quando a conta de armazenamento é criada. O armazenamento de blob e os Ficheiros Azure usam sempre a chave de encriptação da conta para encriptar dados.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para utilizar o PowerShell para criar uma conta de armazenamento que se baseie na chave de encriptação da conta, certifique-se de que instalou o módulo Azure PowerShell, versão 3.4.0 ou posterior. Para obter mais informações, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Em seguida, crie uma conta de armazenamento v2 para fins gerais, chamando o comando [New-AzStorageAccount,](/powershell/module/az.storage/new-azstorageaccount) com os parâmetros apropriados:

- Inclua a `-EncryptionKeyTypeForQueue` opção e desa cimente o seu valor `Account` para usar a chave de encriptação da conta para encriptar dados no armazenamento da fila.
- Inclua a `-EncryptionKeyTypeForTable` opção e desa cimente o seu valor `Account` para usar a chave de encriptação da conta para encriptar dados no armazenamento de tabela.

O exemplo a seguir mostra como criar uma conta de armazenamento v2 de uso geral configurada para armazenamento geo-redundante de acesso à leitura (RA-GRS) e que utiliza a chave de encriptação da conta para encriptar dados tanto para o armazenamento de fila como para a mesa. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para utilizar o Azure CLI para criar uma conta de armazenamento que se baseie na chave de encriptação da conta, certifique-se de que instalou a versão 2.0.80 do Azure CLI ou mais tarde. Para mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Em seguida, crie uma conta de armazenamento v2 para fins gerais, chamando a [conta de armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando, com os parâmetros apropriados:

- Inclua a `--encryption-key-type-for-queue` opção e desa cimente o seu valor `Account` para usar a chave de encriptação da conta para encriptar dados no armazenamento da fila.
- Inclua a `--encryption-key-type-for-table` opção e desa cimente o seu valor `Account` para usar a chave de encriptação da conta para encriptar dados no armazenamento de tabela.

O exemplo a seguir mostra como criar uma conta de armazenamento v2 de uso geral configurada para armazenamento geo-redundante de acesso à leitura (RA-GRS) e que utiliza a chave de encriptação da conta para encriptar dados tanto para o armazenamento de fila como para a mesa. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Modelo](#tab/template)

O exemplo JSON a seguir cria uma conta de armazenamento v2 para fins gerais que está configurada para armazenamento geo-redundante de acesso à leitura (RA-GRS) e que utiliza a chave de encriptação da conta para encriptar dados tanto para o armazenamento de fila como para a mesa. Lembre-se de substituir os valores do espaço reservado nos suportes angulares pelos seus próprios valores:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Depois de ter criado uma conta que se baseia na chave de encriptação da conta, pode configurar chaves geridas pelo cliente que são armazenadas no Cofre da Chave Azure ou no Key Vault Managed Hardware Security Model (HSM) (pré-visualização). Para aprender a armazenar chaves geridas pelo cliente num cofre de chaves, consulte [a encriptação Configure com chaves geridas pelo cliente armazenadas no Cofre da Chave Azure](customer-managed-keys-configure-key-vault.md). Para aprender a armazenar chaves geridas pelo cliente num HSM gerido, consulte [encriptação configure com teclas geridas pelo cliente armazenadas em Azure Key Vault Managed HSM (pré-visualização)](customer-managed-keys-configure-key-vault-hsm.md).

## <a name="verify-the-account-encryption-key"></a>Verifique a chave de encriptação da conta

Para verificar se um serviço numa conta de armazenamento está a usar a chave de encriptação da conta, ligue para o comando da [conta de armazenamento](/cli/azure/storage/account#az-storage-account-show) Azure CLI. Este comando devolve um conjunto de propriedades de conta de armazenamento e seus valores. Procure o `keyType` campo para cada serviço dentro da propriedade de encriptação e verifique se está definido para `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar se um serviço numa conta de armazenamento está a usar a chave de encriptação da conta, ligue para o comando [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Este comando devolve um conjunto de propriedades de conta de armazenamento e seus valores. Procure o `KeyType` campo para cada serviço dentro da propriedade e verifique se está definido para `Encryption` `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar se um serviço numa conta de armazenamento está a usar a chave de encriptação da conta, ligue para o comando [do show da conta de armazenamento AZ.](/cli/azure/storage/account#az-storage-account-show) Este comando devolve um conjunto de propriedades de conta de armazenamento e seus valores. Procure o `keyType` campo para cada serviço dentro da propriedade de encriptação e verifique se está definido para `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="pricing-and-billing"></a>Preços e faturação

Uma conta de armazenamento criada para utilizar uma chave de encriptação scoped para a conta é faturada para capacidade de armazenamento de mesa e transações a uma taxa diferente de uma conta que utiliza a chave de âmbito de serviço padrão. Para mais detalhes, consulte [os preços de armazenamento da mesa Azure.](https://azure.microsoft.com/pricing/details/storage/tables/)

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [Chaves geridas pelo cliente para encriptação de armazenamento Azure](customer-managed-keys-overview.md)
- [O que é Azure Key Vault?](../../key-vault/general/overview.md)