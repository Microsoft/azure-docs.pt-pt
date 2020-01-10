---
title: Usar CLI do Azure para configurar chaves gerenciadas pelo cliente
titleSuffix: Azure Storage
description: Saiba como usar CLI do Azure para configurar chaves gerenciadas pelo cliente com Azure Key Vault para a criptografia de armazenamento do Azure. As chaves gerenciadas pelo cliente permitem criar, girar, desabilitar e revogar controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: db0b5db98f654c140a640f10df2c22c22c85c848
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665301"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Configurar chaves gerenciadas pelo cliente com Azure Key Vault usando CLI do Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Azure Key Vault com chaves gerenciadas pelo cliente usando CLI do Azure. Para saber como criar um cofre de chaves usando CLI do Azure, consulte [início rápido: definir e recuperar um segredo de Azure Key Vault usando CLI do Azure](../../key-vault/quick-create-cli.md).

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

Para saber como habilitar a **exclusão reversível** e **não limpar** em um cofre de chaves existente com CLI do Azure, consulte as seções intituladas **habilitar a exclusão reversível** e **habilitar a proteção de limpeza** em [como usar a exclusão reversível com a CLI](../../key-vault/key-vault-soft-delete-cli.md).

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

Para atualizar as configurações de criptografia da conta de armazenamento, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update), conforme mostrado no exemplo a seguir. Inclua o parâmetro `--encryption-key-source` e defina-o como `Microsoft.Keyvault` para habilitar chaves gerenciadas pelo cliente para a conta de armazenamento. O exemplo também consulta o URI do Key Vault e a versão mais recente da chave, ambos os valores necessários para associar a chave à conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
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

## <a name="use-a-different-key"></a>Usar uma chave diferente

Para alterar a chave usada para a criptografia de armazenamento do Azure, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) , conforme mostrado em [Configurar a criptografia com chaves gerenciadas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e a versão da chave. Se a nova chave estiver em um cofre de chaves diferente, atualize também o URI do cofre de chaves.

## <a name="disable-customer-managed-keys"></a>Desabilitar chaves gerenciadas pelo cliente

Quando você desabilita chaves gerenciadas pelo cliente, sua conta de armazenamento é criptografada com chaves gerenciadas pela Microsoft. Para desabilitar as chaves gerenciadas pelo cliente, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) e defina o `--encryption-key-source parameter` como `Microsoft.Storage`, conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Passos seguintes

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md) 
- [O que é Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
