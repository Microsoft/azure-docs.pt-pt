---
title: Criar uma conta que dê suporte a chaves gerenciadas pelo cliente para tabelas e filas
titleSuffix: Azure Storage
description: Saiba como criar uma conta de armazenamento que dá suporte à configuração de chaves gerenciadas pelo cliente para tabelas e filas. Use o CLI do Azure ou um modelo de Azure Resource Manager para criar uma conta de armazenamento que dependa da chave de criptografia da conta para a criptografia de armazenamento do Azure. Em seguida, você pode configurar chaves gerenciadas pelo cliente para a conta.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083568"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Criar uma conta que dê suporte a chaves gerenciadas pelo cliente para tabelas e filas

O armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, o armazenamento de filas e o armazenamento de tabelas usam uma chave que tem como escopo o serviço e é gerenciada pela Microsoft. Você também pode optar por usar chaves gerenciadas pelo cliente para criptografar dados de fila ou de tabela. Para usar chaves gerenciadas pelo cliente com filas e tabelas, você deve primeiro criar uma conta de armazenamento que usa uma chave de criptografia com escopo para a conta, e não para o serviço. Depois de criar uma conta que usa a chave de criptografia da conta para dados de fila e tabela, você pode configurar chaves gerenciadas pelo cliente com Azure Key Vault para essa conta de armazenamento.

Este artigo descreve como criar uma conta de armazenamento que se baseia em uma chave que tem como escopo a conta. Quando a conta é criada pela primeira vez, a Microsoft usa a chave de conta para criptografar os dados na conta e a Microsoft gerencia a chave. Posteriormente, você pode configurar chaves gerenciadas pelo cliente para a conta para aproveitar esses benefícios, incluindo a capacidade de fornecer suas próprias chaves, atualizar a versão da chave, girar as chaves e revogar controles de acesso.

## <a name="about-the-feature"></a>Sobre o recurso

Para criar uma conta de armazenamento que dependa da chave de criptografia da conta para armazenamento de fila e de tabela, primeiro você deve se registrar para usar esse recurso com o Azure. Devido à capacidade limitada, lembre-se de que pode levar vários meses antes que as solicitações de acesso sejam aprovadas.

Você pode criar uma conta de armazenamento que dependa da chave de criptografia da conta para armazenamento de fila e de tabela nas seguintes regiões:

- E.U.A. Leste
- E.U.A. Centro-Sul
- E.U.A.Oeste 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registre-se para usar a chave de criptografia da conta

Para se registar para utilizar a chave de encriptação da conta com armazenamento de fila ou mesa, utilize powerShell ou Azure CLI.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Para se registar na PowerShell, ligue para o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para se registar no Azure CLI, ligue para o comando de registo de [características az.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

N/D

---

### <a name="check-the-status-of-your-registration"></a>Verificar o status do seu registro

Para verificar o estado do seu registo para armazenamento de fila ou mesa, utilize powerShell ou Azure CLI.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Para verificar o estado do seu registo com a PowerShell, ligue para o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar o estado do seu registo com o Azure CLI, ligue para o comando de [recurso az.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

N/D

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Registrar novamente o provedor de recursos de armazenamento do Azure

Depois que o registro for aprovado, você deverá registrar novamente o provedor de recursos de armazenamento do Azure. Utilize o PowerShell ou o Azure CLI para voltar a registar o fornecedor de recursos.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Para voltar a registar o fornecedor de recursos com a PowerShell, ligue para o comando [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para voltar a registar o fornecedor de recursos com o Azure CLI, ligue para o comando do registo do [fornecedor az.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Criar uma conta que usa a chave de criptografia da conta

Você deve configurar uma nova conta de armazenamento para usar a chave de criptografia da conta para filas e tabelas no momento em que você cria a conta de armazenamento. O escopo da chave de criptografia não pode ser alterado depois que a conta é criada.

A conta de armazenamento deve ser do tipo de uso geral v2. Você pode criar a conta de armazenamento e configurá-la para contar com a chave de criptografia da conta usando o CLI do Azure ou um modelo de Azure Resource Manager.

> [!NOTE]
> Somente o armazenamento de fila e de tabela pode ser configurado opcionalmente para criptografar dados com a chave de criptografia da conta quando a conta de armazenamento é criada. O armazenamento de BLOBs e os arquivos do Azure sempre usam a chave de criptografia da conta para criptografar dados.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Para utilizar o PowerShell para criar uma conta de armazenamento que dependa da chave de encriptação da conta, certifique-se de que instalou o módulo PowerShell Azure, versão 3.4.0 ou mais tarde. Para mais informações, consulte [Instalar o módulo PowerShell Azure](/powershell/azure/install-az-ps).

Em seguida, crie uma conta de armazenamento v2 de uso geral, ligando para o comando [New-AzStorageAccount,](/powershell/module/az.storage/new-azstorageaccount) com os parâmetros adequados:

- Inclua a opção `-EncryptionKeyTypeForQueue` e detete o seu valor para `Account` usar a chave de encriptação da conta para encriptar dados no armazenamento da fila.
- Inclua a opção `-EncryptionKeyTypeForTable` e detete o seu valor para `Account` usar a chave de encriptação da conta para encriptar dados no armazenamento da Tabela.

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

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para usar CLI do Azure para criar uma conta de armazenamento que dependa da chave de criptografia da conta, verifique se você instalou CLI do Azure versão 2.0.80 ou posterior. Para mais informações, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

Em seguida, crie uma conta de armazenamento v2 de uso geral, chamando a conta de [armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando, com os parâmetros adequados:

- Inclua a opção `--encryption-key-type-for-queue` e detete o seu valor para `Account` usar a chave de encriptação da conta para encriptar dados no armazenamento da fila.
- Inclua a opção `--encryption-key-type-for-table` e detete o seu valor para `Account` usar a chave de encriptação da conta para encriptar dados no armazenamento da Tabela.

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

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

O exemplo jSON seguinte cria uma conta de armazenamento v2 de uso geral que está configurada para armazenamento geo-redundante de acesso de leitura (RA-GRS) e que utiliza a chave de encriptação da conta para encriptar dados tanto para o armazenamento de fila como para a tabela. Lembre-se de substituir os valores de espaço reservado entre colchetes angulares por seus próprios valores:

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

Depois de criar uma conta que dependa da chave de criptografia da conta, consulte um dos artigos a seguir para configurar as chaves gerenciadas pelo cliente com o Azure Key Vault:

- [Configure as chaves geridas pelo cliente com o Cofre chave Azure utilizando o portal Azure](storage-encryption-keys-portal.md)
- [Configure as chaves geridas pelo cliente com o Cofre de Chaves Azure utilizando a PowerShell](storage-encryption-keys-powershell.md)
- [Configure as chaves geridas pelo cliente com o Cofre de Chave Azure utilizando o Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Verificar a chave de criptografia da conta

Para verificar se um serviço numa conta de armazenamento está a usar a chave de encriptação da conta, ligue para o comando da conta de armazenamento Azure CLI [az.](/cli/azure/storage/account#az-storage-account-show) Esse comando retorna um conjunto de propriedades de conta de armazenamento e seus valores. Procure o campo `keyType` para cada serviço dentro da propriedade de encriptação e verifique se está definido para `Account`.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Para verificar se um serviço numa conta de armazenamento está a usar a chave de encriptação da conta, ligue para o comando [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Esse comando retorna um conjunto de propriedades de conta de armazenamento e seus valores. Procure o campo `KeyType` para cada serviço dentro da propriedade `Encryption` e verifique se está definido para `Account`.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar se um serviço numa conta de armazenamento está a usar a chave de encriptação da conta, ligue para o comando da conta de [armazenamento az.](/cli/azure/storage/account#az-storage-account-show) Esse comando retorna um conjunto de propriedades de conta de armazenamento e seus valores. Procure o campo `keyType` para cada serviço dentro da propriedade de encriptação e verifique se está definido para `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

N/D

---

## <a name="next-steps"></a>Passos seguintes

- [Encriptação azure storage para dados em repouso](storage-service-encryption.md) 
- [O que é o Cofre chave Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
