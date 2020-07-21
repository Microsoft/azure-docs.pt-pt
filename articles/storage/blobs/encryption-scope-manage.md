---
title: Criar e gerir os âmbitos de encriptação (pré-visualização)
description: ''
services: storage
author: tamram
ms.service: storage
ms.date: 07/13/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f589f0108cf21e77be5103afcaa0242c6f191ab3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532471"
---
# <a name="create-and-manage-encryption-scopes-preview"></a>Criar e gerir os âmbitos de encriptação (pré-visualização)

Os âmbitos de encriptação (pré-visualização) permitem-lhe gerir a encriptação ao nível de uma bolha ou recipiente individual. Um âmbito de encriptação isola dados blob num enclave seguro dentro de uma conta de armazenamento. Pode utilizar âmbitos de encriptação para criar limites seguros entre dados que residem na mesma conta de armazenamento, mas que pertencem a diferentes clientes. Para obter mais informações sobre os âmbitos de [encriptação, consulte os âmbitos de encriptação para armazenamento blob (pré-visualização)](../common/storage-service-encryption.md#encryption-scopes-for-blob-storage-preview).

Este artigo mostra como criar um âmbito de encriptação. Também mostra como especificar um âmbito de encriptação quando cria uma bolha ou recipiente.

## <a name="create-an-encryption-scope"></a>Criar um âmbito de encriptação

Para criar um âmbito de encriptação, tem primeiro de criar um cofre de chave Azure e adicionar a chave que pretende utilizar para o âmbito. O cofre-chave deve ter as propriedades de **Proteção** de **Eliminação Suave** e Purga ativadas e deve estar na mesma região que a conta de armazenamento. Para obter mais informações, consulte [as teclas geridas pelo cliente com o Azure Key Vault para gerir a encriptação do Armazenamento Azure](../common/encryption-customer-managed-keys.md).

Um âmbito de encriptação é automaticamente ativado quando o cria. Depois de criar o âmbito de encriptação, pode especipulá-lo quando criar uma bolha. Também pode especificar um âmbito de encriptação predefinido quando criar um recipiente, que se aplica automaticamente a todas as bolhas do recipiente.

# <a name="portal"></a>[Portal](#tab/portal)

Para criar um âmbito de encriptação no portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Selecione a **definição de encriptação.**
1. Selecione o **separador Âmbitos de Encriptação.**
1. Clique no botão **Adicionar** para adicionar um novo âmbito de encriptação.
1. No painel De **Âmbito de Encriptação** Create, insira um nome para o novo âmbito.
1. Selecione o tipo de encriptação, quer **as teclas geridas pela Microsoft** quer as **teclas geridas pelo Cliente**.
    - Se selecionar **teclas geridas pela Microsoft,** clique em **Criar** para criar o âmbito de encriptação.
    - Se selecionar **teclas geridas pelo Cliente,** especifique um cofre, chave e versão chave para utilizar para este âmbito de encriptação, como mostra a seguinte imagem.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Screenshot mostrando como criar o âmbito de encriptação no portal Azure":::

Para obter mais informações sobre a configuração das chaves geridas pelo cliente com o Cofre da Chave Azure para encriptação de armazenamento Azure, consulte [as chaves geridas pelo cliente com cofre de chaves Azure utilizando o portal Azure](../common/storage-encryption-keys-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar um âmbito de encriptação com o PowerShell, instale primeiro a versão do módulo de pré-visualização Az.Storage. A utilização da versão de pré-visualização mais recente é recomendada, mas os âmbitos de encriptação são suportados na versão 1.13.4-pré-visualização e posterior. Remova quaisquer outras versões do módulo Az.Storage.

O seguinte comando instala o módulo de [pré-visualização Az.Storage 2.1.1:1:](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview)

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Criar um âmbito de encriptação protegido por teclas geridas pela Microsoft

Para criar um novo âmbito de encriptação protegido pelas teclas geridas pela Microsoft, ligue para o comando **New-AzStorageEncryptionScope** com o `-StorageEncryption` parâmetro.

Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Criar um âmbito de encriptação protegido por chaves geridas pelo cliente

Para criar um novo âmbito de encriptação protegido por chaves geridas pelo cliente com Azure Key Vault, configurar primeiro as chaves geridas pelo cliente para a conta de armazenamento. Deve atribuir uma identidade gerida à conta de armazenamento e, em seguida, usar a identidade gerida para configurar a política de acesso para o cofre chave para que a conta de armazenamento tenha permissões para aceder a ela. Para obter mais informações, consulte [as chaves geridas pelo cliente com o Azure Key Vault utilizando o PowerShell](../common/storage-encryption-keys-powershell.md).

Para configurar as teclas geridas pelo cliente para utilização com um âmbito de encriptação, tanto as propriedades de **Proteção** de **Eliminação Suave** como de Purga devem ser ativadas no cofre da chave. O cofre-chave deve estar na mesma região que a conta de armazenamento. Para obter mais informações, consulte [as teclas geridas pelo cliente com o Azure Key Vault para gerir a encriptação do Armazenamento Azure](../common/encryption-customer-managed-keys.md).

Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri-with-version>"
$scopeName2 = "customer2scope"


# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Em seguida, ligue para o comando **New-AzStorageEncryptionScope** com o `-KeyvaultEncryption` parâmetro e especifique a chave URI. Certifique-se de incluir a versão chave na chave URI. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para criar um âmbito de encriptação com o Azure CLI, instale primeiro a versão 2.4.0 ou posterior do Azure CLI.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Criar um âmbito de encriptação protegido por teclas geridas pela Microsoft

Para criar um novo âmbito de encriptação protegido pelas teclas geridas pela Microsoft, ligue para o comando [de encriptação de encriptação da conta de armazenamento az,](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) especificando o `--key-source` parâmetro como `Microsoft.Storage` . Lembre-se de substituir os valores do espaço reservado pelos seus próprios valores:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Criar um âmbito de encriptação protegido por chaves geridas pelo cliente

Para criar um novo âmbito de encriptação protegido pelas teclas geridas pela Microsoft, ligue para o comando [de encriptação de encriptação da conta de armazenamento az,](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) especificando o `--key-source` parâmetro como `Microsoft.Storage` . Lembre-se de substituir os valores do espaço reservado pelos seus próprios valores:

Para criar um novo âmbito de encriptação protegido por chaves geridas pelo cliente com Azure Key Vault, configurar primeiro as chaves geridas pelo cliente para a conta de armazenamento. Deve atribuir uma identidade gerida à conta de armazenamento e, em seguida, usar a identidade gerida para configurar a política de acesso para o cofre chave para que a conta de armazenamento tenha permissões para aceder a ela. Para obter mais informações, consulte [as chaves geridas pelo cliente com o Azure Key Vault utilizando o Azure CLI](../common/storage-encryption-keys-cli.md).

Para configurar as teclas geridas pelo cliente para utilização com um âmbito de encriptação, tanto as propriedades de **Proteção** de **Eliminação Suave** como de Purga devem ser ativadas no cofre da chave. O cofre-chave deve estar na mesma região que a conta de armazenamento. Para obter mais informações, consulte [as teclas geridas pelo cliente com o Azure Key Vault para gerir a encriptação do Armazenamento Azure](../common/encryption-customer-managed-keys.md).

Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Em seguida, ligue para o **âmbito de encriptação da conta de armazenamento az criar** comando com o `--key-uri` parâmetro, e especificar o URI chave. Certifique-se de incluir a versão chave na chave URI. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="list-encryption-scopes-for-storage-account"></a>Listar os âmbitos de encriptação para conta de armazenamento

# <a name="portal"></a>[Portal](#tab/portal)

Para ver os âmbitos de encriptação de uma conta de armazenamento no portal Azure, navegue para a definição **de Âmbitos de Encriptação** para a conta de armazenamento. A partir deste painel, pode ativar ou desativar um âmbito de encriptação ou alterar a chave para um âmbito de encriptação.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Screenshot mostrando lista de âmbitos de encriptação no portal Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para listar os âmbitos de encriptação disponíveis para uma conta de armazenamento com o PowerShell, ligue para o comando Get-AzStorageEncryptionScope. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Para listar todos os âmbitos de encriptação de um grupo de recursos por conta de armazenamento, utilize a sintaxe do gasoduto da seguinte forma:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para listar os âmbitos de encriptação disponíveis para uma conta de armazenamento com o Azure CLI, ligue para o comando da [lista de encriptação da conta de armazenamento Az.](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Criar um recipiente com um âmbito de encriptação padrão

# <a name="portal"></a>[Portal](#tab/portal)

Para criar um recipiente com um âmbito de encriptação padrão no portal Azure, crie primeiro o âmbito de encriptação descrito na [Criar um âmbito de encriptação](#create-an-encryption-scope). Em seguida, siga estes passos para criar o recipiente:

1. Navegue na lista de recipientes na sua conta de armazenamento e selecione o botão **Adicionar** para criar um novo recipiente.
1. Expanda as definições **Avançadas** no painel do **Novo Recipiente.**
1. No desvio do âmbito de **encriptação,** selecione o âmbito de encriptação padrão para o recipiente.
1. Para exigir que todas as bolhas do recipiente utilizem o âmbito de encriptação predefinido, selecione a caixa de **verificação para Utilizar este âmbito de encriptação para todas as bolhas no recipiente**. Se esta caixa de verificação for selecionada, então uma bolha individual no recipiente não pode sobrepor-se ao âmbito de encriptação predefinido.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Screenshot mostrando recipiente com âmbito de encriptação padrão":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar um recipiente com um âmbito de encriptação padrão com o PowerShell, ligue para o comando [New-AzRmStorageContainer,](/powershell/module/az.storage/new-azrmstoragecontainer) especificando o âmbito para o `-DefaultEncryptionScope` parâmetro. O comando **New-AzRmStorageContainer** cria um recipiente utilizando o fornecedor de recursos de armazenamento Azure, que permite a configuração de âmbitos de encriptação e outras operações de gestão de recursos.

Uma bolha individual pode ser criada com o seu próprio âmbito de encriptação, a menos que o recipiente esteja configurado para exigir que todas as bolhas utilizem o seu âmbito padrão. Para forçar todas as bolhas num recipiente a utilizar o âmbito padrão do recipiente, deite o `-PreventEncryptionScopeOverride` parâmetro para `true` .

```powershell
$containerName1 = "container1"
$containerName2 = "container2"

# Create a container with a default encryption scope that cannot be overridden.
New-AzRmStorageContainer -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Name $containerName1 `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para criar um recipiente com um âmbito de encriptação padrão com O Azure CLI, ligue para o [recipiente de armazenamento az criar](/cli/azure/storage/container#az-storage-container-create) comando, especificando o âmbito para o `--default-encryption-scope` parâmetro. Uma bolha individual pode ser criada com o seu próprio âmbito de encriptação, a menos que o recipiente esteja configurado para exigir que todas as bolhas utilizem o seu âmbito padrão. Para forçar todas as bolhas num recipiente a utilizar o âmbito padrão do recipiente, deite o `--prevent-encryption-scope-override` parâmetro para `true` .

O exemplo a seguir utiliza a sua conta Azure AD para autorizar a operação de criação do recipiente. Também pode utilizar a chave de acesso à conta. Para obter mais informações, consulte [Autorizar o acesso aos dados de blob ou fila com o Azure CLI](../common/authorize-data-operations-cli.md).

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Se um cliente tentar especificar um âmbito ao carregar uma bolha para um recipiente que tenha um âmbito de encriptação padrão e o recipiente estiver configurado para evitar que as bolhas sobrepuem o âmbito padrão, então a operação falha com uma mensagem indicando que o pedido é proibido pela política de encriptação do recipiente.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Faça upload de uma bolha com um âmbito de encriptação

Quando fizer o upload de uma bolha, pode especificar um âmbito de encriptação para essa bolha, ou utilizar o âmbito de encriptação padrão para o recipiente, se tiver sido especificado. 

# <a name="portal"></a>[Portal](#tab/portal)

Para carregar uma bolha com um âmbito de encriptação especificado no portal Azure, crie primeiro o âmbito de encriptação descrito na [Criar um âmbito de encriptação](#create-an-encryption-scope). Em seguida, siga estes passos para criar a bolha:

1. Navegue para o recipiente para onde pretende carregar a bolha.
1. Selecione o botão **Upload** e localize a bolha para carregar.
1. Expanda as definições **Avançadas** no **painel de bolhas de upload.**
1. Localize a secção de redução do alcance da **encriptação.** Por predefinição, a bolha é criada com o âmbito de encriptação padrão para o recipiente, se um tiver sido especificado. Se o recipiente exigir que as bolhas utilizem o âmbito de encriptação predefinido, esta secção é desativada.
1. Para especificar um âmbito diferente para a bolha que está a carregar, **selecione Escolha um âmbito existente**e, em seguida, selecione o âmbito pretendido a partir do drop-down.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Screenshot mostrando como carregar uma bolha com um âmbito de encriptação":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para carregar uma bolha com um âmbito de encriptação especificado através da utilização do PowerShell, ligue para o comando [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) e forneça o âmbito de encriptação para a bolha.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = (Get-AzStorageAccount -ResourceGroupName $rgName -StorageAccountName $accountName).Context

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx
# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx -Container $containerName2 -File $localSrcFile -Blob "helloworld.txt" -BlobType Block -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para carregar uma bolha com um âmbito de encriptação especificado através da utilização do Azure CLI, ligue para o comando de upload de [blob de armazenamento az](/cli/azure/storage/blob#az-storage-blob-upload) e forneça o âmbito de encriptação para a bolha.

Se estiver a utilizar o Azure Cloud Shell, siga os passos descritos no [Upload uma bolha](storage-quickstart-blobs-cli.md#upload-a-blob) para criar um ficheiro no diretório de raiz. Em seguida, pode enviar este ficheiro para uma bolha utilizando a seguinte amostra.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Altere a chave de encriptação para um âmbito

# <a name="portal"></a>[Portal](#tab/portal)

Para alterar a chave que protege um âmbito no portal Azure, siga estes passos:

1. Navegue no **separador Âmbitos de Encriptação** para ver a lista de âmbitos de encriptação da conta de armazenamento.
1. Selecione o botão **Mais** ao lado do âmbito que pretende modificar.
1. No painel de **âmbito de encriptação Editar,** pode alterar o tipo de encriptação da chave gerida pela Microsoft para a chave gerida pelo cliente ou vice-versa.
1. Para selecionar uma nova chave gerida pelo cliente, selecione **Utilize uma nova chave** e especifique o cofre, a chave e a versão chave.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para alterar a chave que protege um âmbito de encriptação de uma chave gerida pelo cliente para uma chave gerida pela Microsoft com o PowerShell, ligue para o comando **Update-AzStorageEncryptionScope** e passe no `-StorageEncryption` parâmetro:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Para alterar a chave que protege um âmbito de encriptação de uma chave gerida pela Microsoft para uma chave gerida pelo cliente, certifique-se primeiro de que ativou chaves geridas pelo cliente com cofre de chave Azure para a conta de armazenamento. Para obter mais informações, consulte [as chaves geridas pelo cliente com o Azure Key Vault utilizando o PowerShell](../common/storage-encryption-keys-powershell.md). Em seguida, ligue para o comando **Update-AzStorageEncryptionScope** e passe nos `-KeyUri` parâmetros e `-KeyvaultEncryption` parâmetros:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para alterar a chave que protege um âmbito de encriptação de uma chave gerida pelo cliente para uma chave gerida pela Microsoft com O Azure CLI, ligue para o comando [de atualização de encriptação de encriptação de encriptação do az](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) e passe no `--key-source` parâmetro com o valor `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Para alterar a chave que protege um âmbito de encriptação de uma chave gerida pela Microsoft para uma chave gerida pelo cliente, certifique-se primeiro de que ativou chaves geridas pelo cliente com cofre de chave Azure para a conta de armazenamento. Para obter mais informações, consulte [as chaves geridas pelo cliente com o Azure Key Vault utilizando o Azure CLI](../common/storage-encryption-keys-cli.md). Em seguida, ligue para o comando **de encriptação de encriptação da conta de armazenamento az,** passe no `--key-uri` parâmetro e passe no parâmetro com o valor `--key-source` `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Desativar um âmbito de encriptação

# <a name="portal"></a>[Portal](#tab/portal)

Para desativar um âmbito de encriptação no portal Azure, navegue na definição **de Âmbitos de Encriptação** para a conta de armazenamento, selecione o âmbito de encriptação pretendido e selecione **Desativar**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para desativar um âmbito de encriptação com o PowerShell, ligue para o comando Update-AzStorageEncryptionScope e inclua o `-State` parâmetro com um valor de , como mostrado no exemplo `disabled` seguinte. Para reequipá-lo, ligue para o mesmo comando com o `-State` parâmetro definido para `enabled` . Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para desativar um âmbito de encriptação com O Azure CLI, ligue para o comando [de atualização de encriptação de encriptação da conta de armazenamento az](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) e inclua o `--state` parâmetro com um valor de , como mostrado no exemplo `Disabled` seguinte. Para reequipá-lo, ligue para o mesmo comando com o `--state` parâmetro definido para `Enabled` . Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

---

## <a name="next-steps"></a>Próximos passos

- [Azure Storage encryption for data at rest](../common/storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [Utilize chaves geridas pelo cliente com cofre de chaves Azure para gerir a encriptação de armazenamento Azure](../common/encryption-customer-managed-keys.md)
