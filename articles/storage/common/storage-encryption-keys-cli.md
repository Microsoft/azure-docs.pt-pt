---
title: Utilize o CLI Azure para configurar chaves geridas pelo cliente
titleSuffix: Azure Storage
description: Aprenda a usar o Azure CLI para configurar chaves geridas pelo cliente com o Azure Key Vault para encriptação de armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 4d54a8bf9fb5a1f31a29e41ecea545b43dbe58cf
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87276691"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Configure as chaves geridas pelo cliente com o Azure Key Vault utilizando o Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Cofre de Chaves Azure com chaves geridas pelo cliente usando O Azure CLI. Para aprender a criar um cofre de chaves utilizando o Azure CLI, consulte [Quickstart: set and retrieve a secret from Azure Key Vault using Azure CLI](../../key-vault/secrets/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Para ativar as chaves geridas pelo cliente para a sua conta de armazenamento, atribua primeiro uma identidade gerida atribuída ao sistema para a conta de armazenamento. Usará esta identidade gerida para conceder permissão à conta de armazenamento para aceder ao cofre das chaves.

Para atribuir uma identidade gerida utilizando o Azure CLI, ligue para [a atualização da conta de armazenamento AZ](/cli/azure/storage/account#az-storage-account-update). Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Para obter mais informações sobre a configuração de identidades geridas atribuídas pelo sistema com o Azure CLI, consulte [as identidades geridas para os recursos Azure num VM Azure utilizando o Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre-chave

O cofre-chave que utiliza para armazenar chaves geridas pelo cliente para encriptação de armazenamento Azure deve ter duas definições de proteção de chaves ativadas, **Soft Delete** e **Não Purgar**. Para criar um novo cofre de chaves utilizando o PowerShell ou o Azure CLI com estas definições ativadas, execute os seguintes comandos. Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

Para criar um novo cofre de chaves utilizando o Azure CLI, chame [a az keyvault criar](/cli/azure/keyvault#az-keyvault-create). Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Para aprender a ativar a **eliminação suave** e **não purgar** num cofre de chaves existente com O Azure CLI, consulte as secções intituladas **Permitir a eliminação suave** e permitir a **proteção contra a purga** em como utilizar o [soft-delete com O LÍI](../../key-vault/general/soft-delete-cli.md).

## <a name="configure-the-key-vault-access-policy"></a>Configure a política de acesso ao cofre chave

Em seguida, configurar a política de acesso para o cofre chave para que a conta de armazenamento tenha permissões para aceder a ele. Neste passo, utilizará a identidade gerida que atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, ligue para [a política de definição de keyvault az](/cli/azure/keyvault#az-keyvault-set-policy). Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

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
    --key-permissions get unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma chave no cofre da chave. Para criar uma chave, chame [a chave az keyvault criar](/cli/azure/keyvault/key#az-keyvault-key-create). Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

A encriptação de armazenamento Azure suporta chaves RSA e RSA-HSM dos tamanhos 2048, 3072 e 4096. Para obter mais informações sobre as chaves, consulte **as chaves do Cofre chave** em chaves [Azure Key Vault, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar encriptação com chaves geridas pelo cliente

Por predefinição, a encriptação do Azure Storage utiliza as teclas geridas pela Microsoft. Neste passo, configufique a sua conta de Armazenamento Azure para utilizar chaves geridas pelo cliente com o Azure Key Vault e, em seguida, especifique a chave para associar à conta de armazenamento.

Ao configurar a encriptação com as teclas geridas pelo cliente, pode optar por rodar automaticamente a chave utilizada para encriptação quando a versão muda no cofre de teclas associado. Em alternativa, pode especificar explicitamente uma versão chave a ser utilizada para encriptação até que a versão chave seja atualizada manualmente.

### <a name="configure-encryption-for-automatic-rotation-of-customer-managed-keys"></a>Encriptação configurada para rotação automática de chaves geridas pelo cliente

Para configurar a encriptação para a rotação automática das teclas geridas pelo cliente, instale [a versão 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) ou posterior do Azure CLI. Para mais informações, consulte [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Para rodar automaticamente as teclas geridas pelo cliente, omita a versão chave quando configurar as chaves geridas pelo cliente para a conta de armazenamento. Ligue para a atualização da [conta de armazenamento AZ](/cli/azure/storage/account#az-storage-account-update) para atualizar as definições de encriptação da conta de armazenamento, como mostrado no exemplo seguinte. Inclua o `--encryption-key-source` parâmetro e desa cose para `Microsoft.Keyvault` ativar as chaves geridas pelo cliente para a conta. Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

### <a name="configure-encryption-for-manual-rotation-of-key-versions"></a>Encriptação configurada para rotação manual de versões-chave

Para especificar explicitamente uma versão chave a utilizar para encriptação, forneça a versão chave quando configurar a encriptação com as chaves geridas pelo cliente para a conta de armazenamento. Ligue para a atualização da [conta de armazenamento AZ](/cli/azure/storage/account#az-storage-account-update) para atualizar as definições de encriptação da conta de armazenamento, como mostrado no exemplo seguinte. Inclua o `--encryption-key-source` parâmetro e desa cose para `Microsoft.Keyvault` ativar as chaves geridas pelo cliente para a conta. Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

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

Quando rodar manualmente a versão chave, terá de atualizar as definições de encriptação da conta de armazenamento para utilizar a nova versão. Em primeiro lugar, consulta para o cofre-chave URI chamando [az keyvault show](/cli/azure/keyvault#az-keyvault-show), e para a versão chave, chamando [az key-key-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Em [seguida,](/cli/azure/storage/account#az-storage-account-update) ligue para a atualização da conta de armazenamento AZ para atualizar as definições de encriptação da conta de armazenamento para usar a nova versão da chave, como mostrado no exemplo anterior.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para encriptação de armazenamento Azure, ligue para [a atualização da conta de armazenamento Az,](/cli/azure/storage/account#az-storage-account-update) como mostrado na [encriptação Configure com as teclas geridas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão chave. Se a nova chave estiver num cofre diferente, também atualize o cofre uri de porta-chaves.

## <a name="revoke-customer-managed-keys"></a>Revogar as chaves geridas pelo cliente

Pode revogar as chaves geridas pelo cliente removendo a política de acesso ao cofre. Para revogar uma chave gerida pelo cliente, ligue para o comando de [política de eliminação de chave-chave az,](/cli/azure/keyvault#az-keyvault-delete-policy) como mostra o exemplo seguinte. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Quando desativa as teclas geridas pelo cliente, a sua conta de armazenamento é novamente encriptada com as teclas geridas pela Microsoft. Para desativar as teclas geridas pelo cliente, ligue para [a atualização da conta de armazenamento AZ](/cli/azure/storage/account#az-storage-account-update) e desative-as `--encryption-key-source parameter` para , como mostra o exemplo `Microsoft.Storage` seguinte. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos) 
- [O que é Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
