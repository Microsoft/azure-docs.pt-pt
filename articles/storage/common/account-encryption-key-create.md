---
title: Criar uma conta que suporte chaves geridas pelo cliente para mesas e filas
titleSuffix: Azure Storage
description: Saiba como criar uma conta de armazenamento que suporte a configurar chaves geridas pelo cliente para tabelas e filas. Utilize o modelo Azure CLI ou um modelo de Gestor de Recursos Azure para criar uma conta de armazenamento que se baseie na chave de encriptação da conta para encriptação de Armazenamento Azure. Em seguida, pode configurar as chaves geridas pelo cliente para a conta.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083568"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Criar uma conta que suporte chaves geridas pelo cliente para mesas e filas

O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, o armazenamento de fila e o armazenamento de mesa utilizam uma chave que é traçada para o serviço e gerida pela Microsoft. Também pode optar por utilizar chaves geridas pelo cliente para encriptar dados de fila ou mesa. Para utilizar chaves geridas pelo cliente com filas e tabelas, tem primeiro de criar uma conta de armazenamento que utilize uma chave de encriptação que seja orientada para a conta, em vez de para o serviço. Depois de ter criado uma conta que utiliza a chave de encriptação da conta para dados de fila e mesa, pode configurar chaves geridas pelo cliente com o Azure Key Vault para essa conta de armazenamento.

Este artigo descreve como criar uma conta de armazenamento que se baseia numa chave que é traçada na conta. Quando a conta é criada pela primeira vez, a Microsoft utiliza a chave da conta para encriptar os dados na conta, e a Microsoft gere a chave. Posteriormente, pode configurar as chaves geridas pelo cliente para que a conta tire partido desses benefícios, incluindo a capacidade de fornecer as suas próprias chaves, atualizar a versão chave, rodar as teclas e revogar os controlos de acesso.

## <a name="about-the-feature"></a>Sobre a funcionalidade

Para criar uma conta de armazenamento que dependa da chave de encriptação da conta para o armazenamento de fila e mesa, tem primeiro de se registar para utilizar esta funcionalidade com o Azure. Devido à capacidade limitada, esteja ciente de que pode demorar vários meses até que os pedidos de acesso sejam aprovados.

Pode criar uma conta de armazenamento que se baseie na chave de encriptação da conta para o armazenamento de filas e mesas nas seguintes regiões:

- E.U.A. Leste
- E.U.A. Centro-Sul
- E.U.A.Oeste 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registe-se para utilizar a chave de encriptação da conta

Para se registar para utilizar a chave de encriptação da conta com armazenamento de fila ou mesa, utilize powerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para se registar na PowerShell, ligue para o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para se registar no Azure CLI, ligue para o comando de registo de [características az.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

### <a name="check-the-status-of-your-registration"></a>Verifique o estado do seu registo

Para verificar o estado do seu registo para armazenamento de fila ou mesa, utilize powerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o estado do seu registo com a PowerShell, ligue para o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para verificar o estado do seu registo com o Azure CLI, ligue para o comando de [recurso az.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Re-registe o fornecedor de recursos de armazenamento Azure

Após a aprovação do seu registo, deve voltar a registar o fornecedor de recursos de Armazenamento Azure. Utilize o PowerShell ou o Azure CLI para voltar a registar o fornecedor de recursos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para voltar a registar o fornecedor de recursos com a PowerShell, ligue para o comando [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para voltar a registar o fornecedor de recursos com o Azure CLI, ligue para o comando do registo do [fornecedor az.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Criar uma conta que usa a chave de encriptação da conta

É necessário configurar uma nova conta de armazenamento para utilizar a chave de encriptação da conta para filas e tabelas no momento em que criar a conta de armazenamento. O âmbito da chave de encriptação não pode ser alterado após a criação da conta.

A conta de armazenamento deve ser de tipo v2 de uso geral. Pode criar a conta de armazenamento e configurá-la para confiar na chave de encriptação da conta utilizando o Azure CLI ou um modelo de Gestor de Recursos Azure.

> [!NOTE]
> Apenas o armazenamento de fila e mesa pode ser configurado opcionalmente para encriptar dados com a chave de encriptação da conta quando a conta de armazenamento é criada. O armazenamento blob e os Ficheiros Azure usam sempre a chave de encriptação da conta para encriptar dados.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para utilizar o PowerShell para criar uma conta de armazenamento que dependa da chave de encriptação da conta, certifique-se de que instalou o módulo PowerShell Azure, versão 3.4.0 ou mais tarde. Para mais informações, consulte [Instalar o módulo PowerShell Azure](/powershell/azure/install-az-ps).

Em seguida, crie uma conta de armazenamento v2 de uso geral, ligando para o comando [New-AzStorageAccount,](/powershell/module/az.storage/new-azstorageaccount) com os parâmetros adequados:

- Inclua `-EncryptionKeyTypeForQueue` a opção `Account` e detete o seu valor para usar a chave de encriptação da conta para encriptar dados no armazenamento da fila.
- Inclua `-EncryptionKeyTypeForTable` a opção `Account` e detete o seu valor para usar a chave de encriptação da conta para encriptar dados no armazenamento da Tabela.

O exemplo seguinte mostra como criar uma conta de armazenamento v2 de uso geral que está configurada para armazenamento geo-redundante de acesso de leitura (RA-GRS) e que utiliza a chave de encriptação da conta para encriptar dados tanto para o armazenamento de fila como para a tabela. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para utilizar o Azure CLI para criar uma conta de armazenamento que se baseie na chave de encriptação da conta, certifique-se de que instalou a versão 2.0.80 do Azure CLI ou posterior. Para mais informações, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

Em seguida, crie uma conta de armazenamento v2 de uso geral, chamando a conta de [armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando, com os parâmetros adequados:

- Inclua `--encryption-key-type-for-queue` a opção `Account` e detete o seu valor para usar a chave de encriptação da conta para encriptar dados no armazenamento da fila.
- Inclua `--encryption-key-type-for-table` a opção `Account` e detete o seu valor para usar a chave de encriptação da conta para encriptar dados no armazenamento da Tabela.

O exemplo seguinte mostra como criar uma conta de armazenamento v2 de uso geral que está configurada para armazenamento geo-redundante de acesso de leitura (RA-GRS) e que utiliza a chave de encriptação da conta para encriptar dados tanto para o armazenamento de fila como para a tabela. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios:

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

O exemplo jSON seguinte cria uma conta de armazenamento v2 de uso geral que está configurada para armazenamento geo-redundante de acesso de leitura (RA-GRS) e que utiliza a chave de encriptação da conta para encriptar dados tanto para o armazenamento de fila como para a tabela. Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

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

Depois de ter criado uma conta que se baseia na chave de encriptação da conta, consulte um dos seguintes artigos para configurar as chaves geridas pelo cliente com o Cofre de Chaves Azure:

- [Configure as chaves geridas pelo cliente com o Cofre chave Azure utilizando o portal Azure](storage-encryption-keys-portal.md)
- [Configure as chaves geridas pelo cliente com o Cofre de Chaves Azure utilizando a PowerShell](storage-encryption-keys-powershell.md)
- [Configure as chaves geridas pelo cliente com o Cofre de Chave Azure utilizando o Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Verifique a chave de encriptação da conta

Para verificar se um serviço numa conta de armazenamento está a usar a chave de encriptação da conta, ligue para o comando da conta de armazenamento Azure CLI [az.](/cli/azure/storage/account#az-storage-account-show) Este comando devolve um conjunto de propriedades da conta de armazenamento e seus valores. Procure o `keyType` campo para cada serviço dentro da propriedade de `Account`encriptação e verifique se está definido para .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar se um serviço numa conta de armazenamento está a usar a chave de encriptação da conta, ligue para o comando [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Este comando devolve um conjunto de propriedades da conta de armazenamento e seus valores. Procure o `KeyType` campo para cada `Encryption` serviço dentro da propriedade `Account`e verifique se está definido para .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para verificar se um serviço numa conta de armazenamento está a usar a chave de encriptação da conta, ligue para o comando da conta de [armazenamento az.](/cli/azure/storage/account#az-storage-account-show) Este comando devolve um conjunto de propriedades da conta de armazenamento e seus valores. Procure o `keyType` campo para cada serviço dentro da propriedade de `Account`encriptação e verifique se está definido para .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="next-steps"></a>Passos seguintes

- [Encriptação azure storage para dados em repouso](storage-service-encryption.md) 
- [O que é o Cofre chave Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
