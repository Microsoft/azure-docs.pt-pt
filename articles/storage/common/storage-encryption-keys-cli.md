---
title: Utilize o Azure CLI para configurar as chaves geridas pelo cliente
titleSuffix: Azure Storage
description: Aprenda a usar o Azure CLI para configurar as chaves geridas pelo cliente com o Cofre de Chaves Azure para encriptação de Armazenamento Azure. As chaves geridas pelo cliente permitem criar, rodar, desativar e revogar os controlos de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: fcb4636263843143e685de2e3d2a27bf87cc5a90
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137412"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Configure as chaves geridas pelo cliente com o Cofre de Chave Azure utilizando o Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Cofre de Chave Azure com chaves geridas pelo cliente usando o Azure CLI. Para aprender a criar um cofre chave usando o Azure CLI, consulte [Quickstart: set and retrieve a secret from Azure Key Vault utilizando o Azure CLI](../../key-vault/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Para ativar as chaves geridas pelo cliente para a sua conta de armazenamento, primeiro atribua uma identidade gerida atribuída pelo sistema à conta de armazenamento. Vaiusar esta identidade gerida para conceder permissões à conta de armazenamento para aceder ao cofre chave.

Para atribuir uma identidade gerida utilizando o Azure CLI, ligue para a atualização da conta de [armazenamento az](/cli/azure/storage/account#az-storage-account-update). Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Para obter mais informações sobre a configuração de identidades geridas atribuídas pelo sistema com o Azure CLI, consulte [as identidades geridas pela Configure para os recursos Azure num Azure VM utilizando o Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre chave

O cofre chave que utiliza para armazenar chaves geridas pelo cliente para encriptação de armazenamento azure deve ter duas definições de proteção chave ativadas, **Soft Delete** e **Não Purgar**. Para criar um novo cofre de chaves utilizando o PowerShell ou o Azure CLI com estas definições ativadas, execute os seguintes comandos. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios.

Para criar um novo cofre de chaves usando o Azure CLI, ligue para o [keyvault criar](/cli/azure/keyvault#az-keyvault-create). Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Para aprender a **ativar** o Soft Delete e **não purgar** num cofre de chaves existente com o Azure CLI, consulte as secções intituladas **Enableing soft-delete** e **Enableing Purge Protection** in How to use [soft-delete with CLI](../../key-vault/key-vault-soft-delete-cli.md).

## <a name="configure-the-key-vault-access-policy"></a>Configure a política de acesso ao cofre chave

Em seguida, configure a política de acesso para o cofre chave para que a conta de armazenamento tenha permissões para acessá-lo. Neste passo, utilizará a identidade gerida que anteriormente atribuiu à conta de armazenamento.

Para definir a política de acesso para o cofre chave, ligue para [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios.

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

Em seguida, crie uma chave no cofre da chave. Para criar uma chave, ligue para a [chave keyvault criar](/cli/azure/keyvault/key#az-keyvault-key-create). Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configure encriptação com chaves geridas pelo cliente

Por padrão, a encriptação do Armazenamento Azure utiliza chaves geridas pela Microsoft. Configure a sua conta de Armazenamento Azure para as chaves geridas pelo cliente e especifique a chave para associar à conta de armazenamento.

Para atualizar as definições de encriptação da conta de armazenamento, ligue para a atualização da conta de [armazenamento az](/cli/azure/storage/account#az-storage-account-update), como mostra o exemplo seguinte. Inclua o parâmetro `--encryption-key-source` e defina-o para `Microsoft.Keyvault` para ativar as chaves geridas pelo cliente para a conta de armazenamento. O exemplo também questiona o cofre-chave URI e a versão chave mais recente, ambos os valores necessários para associar a chave à conta de armazenamento. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios.

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

## <a name="update-the-key-version"></a>Atualizar a versão chave

Quando criar uma nova versão de uma chave, terá de atualizar a conta de armazenamento para utilizar a nova versão. Primeiro, consulta para o cofre-chave URI chamando [az keyvault show](/cli/azure/keyvault#az-keyvault-show), e para a versão chave, chamando [az keyvault keyvault lista-list .](/cli/azure/keyvault/key#az-keyvault-key-list-versions) Em [seguida,](/cli/azure/storage/account#az-storage-account-update) ligue para a atualização da conta de armazenamento az para atualizar as definições de encriptação da conta de armazenamento para usar a nova versão da chave, como mostrado na secção anterior.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para encriptação de Armazenamento Azure, ligue para a atualização da conta de [armazenamento az,](/cli/azure/storage/account#az-storage-account-update) como mostrado na [encriptação Configure com chaves geridas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão chave. Se a nova chave estiver num cofre diferente, também atualize o cofre de chaves URI.

## <a name="revoke-customer-managed-keys"></a>Revogar as chaves geridas pelo cliente

Se acredita que uma chave pode ter sido comprometida, pode revogar as chaves geridas pelo cliente removendo a política de acesso ao cofre chave. Para revogar uma chave gerida pelo cliente, ligue para o comando de [az keyvault delete-policy,](/cli/azure/keyvault#az-keyvault-delete-policy) como mostra o seguinte exemplo. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios e utilizar as variáveis definidas nos exemplos anteriores.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Quando desativa as chaves geridas pelo cliente, a sua conta de armazenamento é novamente encriptada com chaves geridas pela Microsoft. Para desativar as chaves geridas pelo cliente, ligue para a atualização da conta de [armazenamento az](/cli/azure/storage/account#az-storage-account-update) e desemque o `--encryption-key-source parameter` para `Microsoft.Storage`, como mostra o seguinte exemplo. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios e utilizar as variáveis definidas nos exemplos anteriores.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Passos Seguintes

- [Encriptação azure storage para dados em repouso](storage-service-encryption.md) 
- [O que é o Cofre chave Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
