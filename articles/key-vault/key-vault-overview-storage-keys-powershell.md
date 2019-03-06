---
title: Conta de armazenamento do Azure Key Vault geridos - versão do PowerShell
description: A funcionalidade da conta de armazenamento gerido fornece uma integração de seemless, entre o Azure Key Vault e uma conta de armazenamento do Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 9b6089aa828b5667f100c1a8cbff3e69345e4512
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405102"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>O Azure Key Vault geridos a conta de armazenamento - PowerShell

> [!NOTE]
> [Integração de armazenamento do Azure com o Azure Active Directory (Azure AD) está agora em pré-visualização](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Recomendamos a utilização do Azure AD para autenticação e autorização, que fornece acesso baseado em tokens do OAuth2 ao armazenamento do Azure, tal como o Azure Key Vault. Isto permite-lhe:
> - Autenticar a sua aplicação de cliente com uma identidade de utilizador ou aplicação, em vez das credenciais da conta de armazenamento. 
> - Utilize um [identidade gerida do Azure AD](/azure/active-directory/managed-identities-azure-resources/) quando em execução no Azure. Gerido identidades remove a necessidade de autenticação de cliente em conjunto e armazenar credenciais no ou com a sua aplicação.
> - Utilize o controlo de acesso baseado em ' (RBAC) da função para gerir a autorização, que também é suportada pelo Key Vault.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Uma [conta de armazenamento do Azure](/azure/storage/storage-create-storage-account) utiliza uma credencial que consiste num nome de conta e uma chave. A chave é gerado automaticamente e serve mais como uma "palavra-passe" em vez de uma chave criptográfica. Cofre de chaves pode gerir estas chaves de conta de armazenamento, armazenando-os como [segredos do Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Descrição geral

O Cofre de chaves geridas recurso executa várias funções de gerenciamento em seu nome de conta de armazenamento:

- Chaves de listas (sincronizações) com uma conta de armazenamento do Azure.
- Regenera (roda) as chaves periodicamente.
- Gere chaves para contas de armazenamento e as contas de armazenamento clássico.
- Valores de chaves nunca são retornados em resposta ao chamador.

Quando utiliza a funcionalidade de chave de conta de armazenamento gerido:

- **Permitir apenas o Key Vault para gerir as chaves de conta de armazenamento.** Não tente geri-los por conta própria, pois irá interferir com os processos de Key Vault.
- **Não permitir que as chaves de conta de armazenamento ser gerido por mais de um objeto do Cofre de chaves**.
- **Manualmente não voltar a gerar as chaves de conta de armazenamento**. Recomendamos que regenere-los através do Key Vault.

O exemplo seguinte mostra como permitir que o Key Vault para gerir as chaves de conta de armazenamento.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Autorizar o Key Vault para aceder à sua conta de armazenamento

> [!IMPORTANT]
> Um inquilino do Azure AD fornece cada aplicação registada com um  **[principal de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object)**, que serve como identidade da aplicação. ID da aplicação do principal de serviço é utilizado quando dando a ele autorização para aceder a outros recursos do Azure, através do controlo de acesso baseado em funções (RBAC). Uma vez que o Key Vault é um aplicativo da Microsoft, previamente está registado em todos os inquilinos do Azure AD sob o mesmo ID de aplicação, dentro de cada cloud do Azure:
> - Utilizam o Azure AD inquilinos na cloud do Azure government ID da aplicação `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - ID da aplicação de utilizar do Azure AD inquilinos na cloud pública do Azure e todos os outros `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

Antes de Key Vault pode aceder e gerir as chaves de conta de armazenamento, tem de autorizar o acesso da sua conta de armazenamento. A aplicação do Key Vault requer permissões para *lista* e *voltar a gerar* chaves para a sua conta de armazenamento. Estas permissões são habilitadas através da função RBAC incorporada [função de serviço de operador de chave de conta de armazenamento](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Atribua esta função para o Cofre de chave principal de serviço, limitar o âmbito para a sua conta de armazenamento, utilizando os passos seguintes. Certifique-se de que atualizar o `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey`, e `$keyVaultName` variáveis antes de executar o script:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Após a atribuição de função efetuada com êxito, deverá ver um resultado semelhante ao seguinte exemplo:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Se o Cofre de chaves já foi adicionado à função na sua conta de armazenamento, receberá um *"a atribuição de função já existe."* Erro. Também pode verificar a atribuição de função, usando a página de "Controlo de acesso (IAM)" da conta de armazenamento no portal do Azure.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Conceder permissão de conta de utilizador para contas de armazenamento gerido

>[!TIP] 
> Tal como o Azure AD fornece um **principal de serviço** para a identidade de um aplicativo, uma **principal de utilizador** é fornecido para a identidade do utilizador. O principal de utilizador, em seguida, pode receber autorização para aceder ao Cofre de chaves, através de permissões de política de acesso do Cofre de chaves.

Utilizar a mesma sessão do PowerShell, atualize a política de acesso do Cofre de chaves para contas de armazenamento gerido. Este passo aplica-se as permissões de conta de armazenamento à sua conta de utilizador, garantindo que pode acessar os recursos da conta de armazenamento gerido: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Tenha em atenção que as permissões para contas de armazenamento não estão disponíveis na página de "Políticas de acesso" da conta de armazenamento no portal do Azure.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Adicionar uma conta de armazenamento gerido para a sua instância do Key Vault

Utilizar a mesma sessão do PowerShell, crie uma conta de armazenamento gerido na sua instância do Key Vault. O `-DisableAutoRegenerateKey` comutador Especifica para não voltar a gerar as chaves de conta de armazenamento.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Após a adição concluída com êxito a conta de armazenamento com nenhuma nova geração de chaves, deverá ver um resultado semelhante ao seguinte exemplo:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Ativar a nova geração de chaves

Se pretender que o Cofre de chaves para regenerar as chaves de conta de armazenamento periodicamente, pode definir um período de regeneração. No exemplo a seguir, definimos um período de regeneração de três dias. Após três dias, o Cofre de chaves irá regenerar 'chave1' e trocar a chave ativa de 'chave2' para 'chave1'.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Após a adição concluída com êxito a conta de armazenamento com a nova geração de chaves, deverá ver um resultado semelhante ao seguinte exemplo:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>Passos Seguintes

- [Gerido exemplos de chave de conta de armazenamento](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- [Referência do PowerShell do Cofre de chaves](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
