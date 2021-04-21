---
title: Configurar a encriptação com chaves geridas pelo cliente armazenadas no Azure Key Vault
titleSuffix: Azure Storage
description: Aprenda a configurar a encriptação de armazenamento Azure com chaves geridas pelo cliente armazenadas no Cofre da Chave Azure utilizando o portal Azure, PowerShell ou Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 77a01a270f47ddacb71962188e7fedd0a0a9f6d0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790442"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Configurar a encriptação com chaves geridas pelo cliente armazenadas no Azure Key Vault

O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, os dados são encriptados com as teclas geridas pela Microsoft. Para controlo adicional sobre as chaves de encriptação, pode gerir as suas próprias chaves. As chaves geridas pelo cliente devem ser armazenadas no Cofre da Chave Azure ou no Modelo de Segurança Gerida do Cofre de Chaves (HSM) (pré-visualização).

Este artigo mostra como configurar a encriptação com chaves geridas pelo cliente armazenadas num cofre de chaves utilizando o portal Azure, PowerShell ou Azure CLI. Para aprender a configurar a encriptação com chaves geridas pelo cliente armazenadas num HSM gerido, consulte [encriptação configure com teclas geridas pelo cliente armazenadas em Azure Key Vault Managed HSM (pré-visualização)](customer-managed-keys-configure-key-vault-hsm.md).

> [!NOTE]
> Azure Key Vault e Azure Key Vault Gerido HSM suportam as mesmas APIs e interfaces de gestão para configuração.

## <a name="configure-a-key-vault"></a>Configure um cofre chave

Pode utilizar um cofre novo ou existente para armazenar chaves geridas pelo cliente. A conta de armazenamento e o cofre-chave devem estar na mesma região, mas podem estar em diferentes subscrições.

A utilização de chaves geridas pelo cliente com encriptação de armazenamento Azure requer que a proteção de eliminação e purga suave seja ativada para o cofre da chave. A eliminação suave é ativada por padrão quando cria um novo cofre de teclas e não pode ser desativado. Pode ativar a proteção contra a purga quando criar o cofre da chave ou depois de ser criado.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para aprender a criar um cofre-chave com o portal Azure, consulte [Quickstart: Crie um cofre-chave utilizando o portal Azure](../../key-vault/general/quick-create-portal.md). Quando criar o cofre da chave, selecione **Ativar a proteção contra a purga,** como mostra a seguinte imagem.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Screenshot mostrando como permitir a proteção de purga ao criar um cofre chave":::

Para permitir a proteção de purga num cofre de chaves existente, siga estes passos:

1. Navegue até ao cofre chave no portal Azure.
1. Em **Definições,** escolha **Propriedades.**
1. Na secção **de proteção de purga,** escolha **Ativar a proteção contra a purga**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar um novo cofre-chave com PowerShell, instale a versão 2.0.0 ou posterior do módulo [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell. Então ligue para [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) para criar um novo cofre chave. Com a versão 2.0.0 e mais tarde do módulo Az.KeyVault, a eliminação suave é ativada por padrão quando cria um novo cofre de chave.

O exemplo a seguir cria um novo cofre de chave com proteção de eliminação e purga macia ativada. Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Para aprender a permitir a proteção de purga num cofre de chaves existente com o PowerShell, consulte [Como utilizar o soft-delete com PowerShell](../../key-vault/general/key-vault-recovery.md).

Em seguida, atribua uma identidade gerida atribuída ao sistema na sua conta de armazenamento. Usará esta identidade gerida para conceder permissão à conta de armazenamento para aceder ao cofre das chaves. Para obter mais informações sobre identidades geridas atribuídas pelo sistema, veja [quais são as identidades geridas para os recursos Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

Para atribuir uma identidade gerida utilizando o PowerShell, ligue para [o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount):

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Finalmente, configurar a política de acesso para o cofre chave para que a conta de armazenamento tenha permissões para aceder ao mesmo. Neste passo, utilizará a identidade gerida que atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, ligue para [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy):

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar um novo cofre de chaves utilizando o Azure CLI, chame [a az keyvault criar](/cli/azure/keyvault#az_keyvault_create). Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Para aprender a permitir a proteção de purga num cofre de chaves existente com O Azure CLI, consulte [Como utilizar o soft-delete com CLI](../../key-vault/general/key-vault-recovery.md).

Em seguida, atribua uma identidade gerida atribuída ao sistema para a conta de armazenamento. Usará esta identidade gerida para conceder permissão à conta de armazenamento para aceder ao cofre das chaves. Para obter mais informações sobre identidades geridas atribuídas pelo sistema, veja [quais são as identidades geridas para os recursos Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

Para atribuir uma identidade gerida utilizando o Azure CLI, ligue para [atualização da conta de armazenamento AZ](/cli/azure/storage/account#az_storage_account_update):

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Finalmente, configurar a política de acesso para o cofre chave para que a conta de armazenamento tenha permissões para aceder ao mesmo. Neste passo, utilizará a identidade gerida que atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, ligue para [a política de definição de keyvault az](/cli/azure/keyvault#az_keyvault_set_policy):

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

---

## <a name="add-a-key"></a>Adicione uma chave

Em seguida, adicione uma chave no cofre da chave.

A encriptação de armazenamento Azure suporta chaves RSA e RSA-HSM dos tamanhos 2048, 3072 e 4096. Para obter mais informações sobre as teclas, consulte [sobre as teclas.](../../key-vault/keys/about-keys.md)

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para aprender a adicionar uma chave com o portal Azure, consulte [Quickstart: set and retrieve a key from Azure Key Vault utilizando o portal Azure](../../key-vault/keys/quick-create-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para adicionar uma chave com PowerShell, ligue [para Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para adicionar uma chave com Azure CLI, chame [a chave az keyvault criar](/cli/azure/keyvault/key#az_keyvault_key_create). Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a encriptação com chaves geridas pelo cliente

Em seguida, configufique a sua conta de Armazenamento Azure para utilizar chaves geridas pelo cliente com o Azure Key Vault e, em seguida, especifique a chave para associar à conta de armazenamento.

Quando configurar a encriptação com as teclas geridas pelo cliente, pode optar por atualizar automaticamente a versão chave utilizada para encriptação do Azure Storage sempre que uma nova versão estiver disponível no cofre de chaves associado. Em alternativa, pode especificar explicitamente uma versão chave a ser utilizada para encriptação até que a versão chave seja atualizada manualmente.

> [!NOTE]
> Para rodar uma chave, crie uma nova versão da chave no Cofre da Chave Azure. O Azure Storage não lida com a rotação da chave no Cofre da Chave Azure, pelo que terá de rodar a chave manualmente ou criar uma função para a rodar num horário.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Encriptação configurada para atualização automática de versões-chave

O Azure Storage pode atualizar automaticamente a chave gerida pelo cliente que é usada para encriptação para usar a versão chave mais recente. Quando a tecla gerida pelo cliente for rodada no Cofre da Chave Azure, o Azure Storage começará automaticamente a utilizar a versão mais recente da chave para encriptação.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para configurar as teclas geridas pelo cliente com atualização automática da versão chave no portal Azure, siga estes passos:

1. Navegue até à sua conta de armazenamento.
1. Na lâmina **Definições** para a conta de armazenamento, clique em **Encriptação**. Por predefinição, a gestão das chaves é definida para **As Teclas Geridas** pela Microsoft , como mostra a seguinte imagem.

    ![Screenshot do portal mostrando opção de encriptação](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Selecione a opção **Chaves Geridas pelo Cliente.**
1. Escolha a **opção Selecionar a partir da** opção Key Vault.
1. **Selecione um cofre e uma chave de teclas**.
1. Selecione o cofre de chaves que contém a chave que pretende utilizar.
1. Selecione a chave do cofre da chave.

   ![Screenshot mostrando como selecionar cofre e chave chave](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Guarde as alterações.

Depois de ter especificado a chave, o portal Azure indica que a atualização automática da versão chave está ativada e exibe a versão chave atualmente em uso para encriptação.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Screenshot mostrando atualização automática da versão chave ativada":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para configurar as teclas geridas pelo cliente com a atualização automática da versão chave com o PowerShell, instale o módulo [Az.Storage,](https://www.powershellgallery.com/packages/Az.Storage) versão 2.0.0 ou posterior.

Para atualizar automaticamente a versão chave para uma chave gerida pelo cliente, omita a versão chave quando configurar a encriptação com as chaves geridas pelo cliente para a conta de armazenamento. Ligue [para o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as definições de encriptação da conta de armazenamento, como mostrado no exemplo seguinte, e inclua a opção **-KeyvaultEncryption** para ativar as chaves geridas pelo cliente para a conta de armazenamento.

Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar as teclas geridas pelo cliente com a atualização automática da versão chave com o Azure CLI, instale [a versão 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) ou posterior do Azure CLI. Para mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Para atualizar automaticamente a versão chave para uma chave gerida pelo cliente, omita a versão chave quando configurar a encriptação com as chaves geridas pelo cliente para a conta de armazenamento. Ligue para a atualização da [conta de armazenamento AZ](/cli/azure/storage/account#az_storage_account_update) para atualizar as definições de encriptação da conta de armazenamento, como mostrado no exemplo seguinte. Inclua o `--encryption-key-source` parâmetro e desa cose para `Microsoft.Keyvault` ativar as chaves geridas pelo cliente para a conta.

Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

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

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Encriptação configurada para atualização manual de versões-chave

Se preferir atualizar manualmente a versão chave, especifique explicitamente a versão no momento em que configurar a encriptação com as teclas geridas pelo cliente. Neste caso, o Azure Storage não atualizará automaticamente a versão chave quando uma nova versão for criada no cofre de chaves. Para utilizar uma nova versão chave, tem de atualizar manualmente a versão utilizada para encriptação do Azure Storage.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para configurar as teclas geridas pelo cliente com a atualização manual da versão chave no portal Azure, especifique a chave URI, incluindo a versão. Para especificar uma chave como URI, siga estes passos:

1. Para localizar a chave URI no portal Azure, navegue até ao cofre da chave e selecione a definição **de Chaves.** Selecione a tecla desejada e, em seguida, clique na chave para visualizar as suas versões. Selecione uma versão chave para visualizar as definições para esta versão.
1. Copie o valor do campo **identificador chave,** que fornece o URI.

    ![Screenshot mostrando chave de cofre chave URI](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. Nas definições da **chave de encriptação** para a sua conta de armazenamento, escolha a opção **URI da chave entrar.**
1. Cole o URI que copiou para o campo **Key URI.** Omita a versão chave do URI para permitir a atualização automática da versão chave.

   ![Screenshot mostrando como entrar na chave URI](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Especifique a subscrição que contém o cofre de chaves.
1. Guarde as alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para configurar as teclas geridas pelo cliente com a atualização manual da versão chave, forneça explicitamente a versão chave quando configurar a encriptação para a conta de armazenamento. Ligue [para o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as definições de encriptação da conta de armazenamento, como mostrado no exemplo seguinte, e inclua a opção **-KeyvaultEncryption** para ativar as chaves geridas pelo cliente para a conta de armazenamento.

Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Quando atualizar manualmente a versão chave, terá de atualizar as definições de encriptação da conta de armazenamento para utilizar a nova versão. Primeiro, ligue para [o Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) para obter a versão mais recente da chave. Em seguida, ligue para [o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as definições de encriptação da conta de armazenamento para utilizar a nova versão da chave, como mostrado no exemplo anterior.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar as teclas geridas pelo cliente com a atualização manual da versão chave, forneça explicitamente a versão chave quando configurar a encriptação para a conta de armazenamento. Ligue para a atualização da [conta de armazenamento AZ](/cli/azure/storage/account#az_storage_account_update) para atualizar as definições de encriptação da conta de armazenamento, como mostrado no exemplo seguinte. Inclua o `--encryption-key-source` parâmetro e desa cose para `Microsoft.Keyvault` ativar as chaves geridas pelo cliente para a conta.

Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

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

Quando atualizar manualmente a versão chave, terá de atualizar as definições de encriptação da conta de armazenamento para utilizar a nova versão. Em primeiro lugar, consulta para o cofre-chave URI chamando [az keyvault show](/cli/azure/keyvault#az_keyvault_show), e para a versão chave, chamando [az key-key-versions](/cli/azure/keyvault/key#az_keyvault_key_list-versions). Em [seguida,](/cli/azure/storage/account#az_storage_account_update) ligue para a atualização da conta de armazenamento AZ para atualizar as definições de encriptação da conta de armazenamento para usar a nova versão da chave, como mostrado no exemplo anterior.

---

## <a name="change-the-key"></a>Mude a chave

Pode alterar a chave que está a usar para encriptação do Armazenamento Azure a qualquer momento.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para alterar a chave com o portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento e apresente as definições **de Encriptação.**
1. Selecione o cofre de chaves e escolha uma nova chave.
1. Guarde as alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para alterar a tecla com o PowerShell, ligue para [o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) como mostrado na [encriptação Configure com as teclas geridas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão chave. Se a nova chave estiver num cofre diferente, também deve atualizar o cofre uri do cofre de chaves.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para alterar a tecla com o Azure CLI, ligue para [a atualização da conta de armazenamento AZ,](/cli/azure/storage/account#az_storage_account_update) como mostrado na [encriptação Configure com as teclas geridas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão chave. Se a nova chave estiver num cofre diferente, também deve atualizar o cofre uri do cofre de chaves.

---

## <a name="revoke-customer-managed-keys"></a>Revogar as chaves geridas pelo cliente

A revogação de uma chave gerida pelo cliente remove a associação entre a conta de armazenamento e o cofre chave.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para revogar as chaves geridas pelo cliente com o portal Azure, desative a chave descrita nas [teclas geridas pelo cliente desativada](#disable-customer-managed-keys).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pode revogar as chaves geridas pelo cliente removendo a política de acesso ao cofre. Para revogar uma chave gerida pelo cliente com o PowerShell, ligue para o comando [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) como mostra o exemplo seguinte. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode revogar as chaves geridas pelo cliente removendo a política de acesso ao cofre. Para revogar uma chave gerida pelo cliente com o Azure CLI, ligue para o comando [de exclusão de chave az,](/cli/azure/keyvault#az_keyvault_delete_policy) como mostra o exemplo seguinte. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Quando desativa as teclas geridas pelo cliente, a sua conta de armazenamento é novamente encriptada com as teclas geridas pela Microsoft.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para desativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento e apresente as definições **de Encriptação.**
1. Desmarcar a caixa de verificação ao lado da definição **de teclas.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para desativar as teclas geridas pelo cliente com o PowerShell, ligue para [o Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) com a `-StorageEncryption` opção, como mostra o exemplo seguinte. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para desativar as teclas geridas pelo cliente com o Azure CLI, ligue para a atualização da [conta de armazenamento Az](/cli/azure/storage/account#az_storage_account_update) e desative-as para , como mostra o exemplo `--encryption-key-source parameter` `Microsoft.Storage` seguinte. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores e utilizar as variáveis definidas nos exemplos anteriores.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [Chaves geridas pelo cliente para encriptação de armazenamento Azure](customer-managed-keys-overview.md)
- [Configure a encriptação com chaves geridas pelo cliente armazenadas no Azure Key Vault Managed HSM (pré-visualização)](customer-managed-keys-configure-key-vault-hsm.md)
