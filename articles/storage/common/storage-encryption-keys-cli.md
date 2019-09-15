---
title: Configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure de CLI do Azure
description: Saiba como usar CLI do Azure para configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure. As chaves gerenciadas pelo cliente permitem criar, girar, desabilitar e revogar controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/24/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ca155763109dbf4292738354244dc397272d06f4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002244"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure de CLI do Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando CLI do Azure.

> [!IMPORTANT]
> O uso de chaves gerenciadas pelo cliente com a criptografia de armazenamento do Azure exige que o cofre de chaves tenha duas propriedades obrigatórias configuradas, **exclusão reversível** e **não limpar**. Essas propriedades são habilitadas por padrão quando você cria um novo cofre de chaves no portal do Azure. No entanto, se você precisar habilitar essas propriedades em um cofre de chaves existente, deverá usar o PowerShell ou CLI do Azure.
> Somente chaves RSA e tamanho de chave 2048 têm suporte.

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Para habilitar chaves gerenciadas pelo cliente para sua conta de armazenamento, primeiro atribua uma identidade gerenciada atribuída pelo sistema à conta de armazenamento. Você usará essa identidade gerenciada para conceder permissões de conta de armazenamento para acessar o cofre de chaves.

Para atribuir uma identidade gerenciada usando CLI do Azure, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Para obter mais informações sobre como configurar identidades gerenciadas atribuídas pelo sistema com CLI do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre de chaves

O cofre de chaves que você usa para armazenar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure deve ter duas configurações de proteção de chave habilitadas, **exclusão reversível** e **não limpar**. Para criar um novo cofre de chaves usando o PowerShell ou CLI do Azure com essas configurações habilitadas, execute os comandos a seguir. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores. 

Para criar um novo cofre de chaves usando CLI do Azure, chame [AZ keyvault Create](/cli/azure/keyvault#az-keyvault-create). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurar a política de acesso do cofre de chaves

Em seguida, configure a política de acesso para o cofre de chaves para que a conta de armazenamento tenha permissões para acessá-la. Nesta etapa, você usará a identidade gerenciada que você atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, chame [AZ keyvault Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Criar uma chave nova

Em seguida, crie uma chave no cofre de chaves. Para criar uma chave, chame a [chave AZ keyvault Key Create](/cli/azure/keyvault/key#az-keyvault-key-create). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a criptografia com chaves gerenciadas pelo cliente

Por padrão, a criptografia de armazenamento do Azure usa chaves gerenciadas pela Microsoft. Configure sua conta de armazenamento do Azure para chaves gerenciadas pelo cliente e especifique a chave a ser associada à conta de armazenamento.

Para atualizar as configurações de criptografia da conta de armazenamento, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update). Este exemplo também consulta o URI do cofre de chaves e a versão de chave, ambos os valores necessários para associar a chave à conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Atualizar a versão de chave

Ao criar uma nova versão de uma chave, você precisará atualizar a conta de armazenamento para usar a nova versão. Primeiro, consulte o URI do cofre de chaves chamando [AZ keyvault show](/cli/azure/keyvault#az-keyvault-show)e para a versão de chave chamando [AZ keyvault Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Em seguida, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) para atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão da chave, conforme mostrado na seção anterior.

## <a name="next-steps"></a>Passos Seguintes

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md) 
- [O que é Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
