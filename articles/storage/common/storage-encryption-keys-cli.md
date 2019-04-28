---
title: Configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure a partir da CLI do Azure
description: Saiba como utilizar a CLI do Azure para configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure. Chaves geridas pelo cliente permitem-lhe criar, girar, desativar e revogar os controlos de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dc9f660ddf7cd003ac113725a9bf7b66189807b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593596"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure a partir da CLI do Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um cofre de chaves com chaves geridas pelo cliente através da CLI do Azure.

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade para a conta de armazenamento

Para ativar as chaves geridas pelo cliente para a sua conta de armazenamento, primeiro de atribuir uma identidade gerida sistema atribuído à conta de armazenamento. Irá utilizar esta identidade gerida para conceder as permissões de conta de armazenamento para aceder ao Cofre de chaves.

Para atribuir uma identidade gerida com a CLI do Azure, chame [atualização da conta de armazenamento az](/cli/azure/storage/account#az-storage-account-update). Lembre-se substituir os valores de marcador de posição entre parênteses Retos pelos seus próprios valores.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Para obter mais informações sobre como configurar atribuído de sistema de identidades geridas com a CLI do Azure, consulte [configurar geridos identidades para recursos do Azure na VM do Azure com a CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre de chaves

O Cofre de chaves que utiliza para armazenar as chaves geridas pelo cliente para encriptação de armazenamento do Azure tem de ter duas definições de proteção por chave ativadas, **eliminação de forma recuperável** e **fazer não remover**. Para criar um novo cofre de chaves com o PowerShell ou da CLI do Azure com estas definições ativadas, execute os seguintes comandos. Lembre-se substituir os valores de marcador de posição entre parênteses Retos pelos seus próprios valores. 

Para criar um novo cofre de chaves com a CLI do Azure, chame [az keyvault criar](/cli/azure/keyvault#az-keyvault-create). Lembre-se substituir os valores de marcador de posição entre parênteses Retos pelos seus próprios valores.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurar a política de acesso do Cofre de chaves

Em seguida, configure a política de acesso para o Cofre de chaves para que a conta de armazenamento tem permissões para aceder ao mesmo. Neste passo, vai utilizar a identidade gerida que tiver atribuído à conta de armazenamento.

Para definir a política de acesso do Cofre de chaves, chame [az keyvault conjunto-policy](/cli/azure/keyvault#az-keyvault-set-policy). Lembre-se substituir os valores de marcador de posição entre parênteses Retos pelos seus próprios valores.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    -name <storage-account> \
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

Em seguida, crie uma chave no Cofre de chaves. Para criar uma chave, chame [criar chave de Cofre de chaves de az](/cli/azure/keyvault/key#az-keyvault-key-create). Lembre-se substituir os valores de marcador de posição entre parênteses Retos pelos seus próprios valores.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a encriptação com chaves geridas pelo cliente

Por predefinição, a encriptação de armazenamento do Azure utiliza as chaves geridas pela Microsoft. Configurar a sua conta de armazenamento do Azure para chaves geridas pelo cliente e especifique a chave para associar a conta de armazenamento.

Para atualizar as definições de encriptação da conta de armazenamento, chame [atualização da conta de armazenamento az](/cli/azure/storage/account#az-storage-account-update). Este exemplo também consulta o para o Cofre de chaves URI e a versão da chave, ambos os valores que são necessárias para associar a chave de conta de armazenamento. Lembre-se substituir os valores de marcador de posição entre parênteses Retos pelos seus próprios valores.

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

## <a name="update-the-key-version"></a>Atualizar a versão da chave

Quando cria uma nova versão de uma chave, terá de atualizar a conta de armazenamento a utilizar a nova versão. Em primeiro lugar, para o URI do Cofre de chaves de consulta ao chamar [show de keyvault de az](/cli/azure/keyvault#az-keyvault-show)e para a versão da chave ao chamar [az keyvault chave lista as versões de](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Em seguida, chame [atualização da conta de armazenamento az](/cli/azure/storage/account#az-storage-account-update) para atualizar as definições de encriptação da conta de armazenamento para utilizar a versão nova da chave, como mostrado na secção anterior.

## <a name="next-steps"></a>Passos Seguintes

- [Encriptação de armazenamento do Azure para dados Inativos](storage-service-encryption.md) 
- [O que é o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
