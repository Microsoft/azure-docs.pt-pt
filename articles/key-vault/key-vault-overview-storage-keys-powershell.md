---
title: Conta de armazenamento gerenciado Azure Key Vault-versão do PowerShell
description: O recurso de conta de armazenamento gerenciada fornece uma integração aparentemente desproporcionada, entre Azure Key Vault e uma conta de armazenamento do Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 708c34347966eee7817ca04e0552dcba233765cb
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934502"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Azure Key Vault conta de armazenamento gerenciado-PowerShell

> [!NOTE]
> [A integração do armazenamento do Azure com o Azure Active Directory (Azure AD) agora está em visualização](../storage/common/storage-auth-aad.md). É recomendável usar o Azure AD para autenticação e autorização, que fornece acesso baseado em token OAuth2 ao armazenamento do Azure, assim como Azure Key Vault. Isso permite que você:
> - Autentique seu aplicativo cliente usando uma identidade de aplicativo ou de usuário, em vez de credenciais de conta de armazenamento. 
> - Use uma [identidade gerenciada do Azure ad](/azure/active-directory/managed-identities-azure-resources/) ao executar no Azure. Identidades gerenciadas eliminam a necessidade de autenticação de cliente e armazenam credenciais em ou com seu aplicativo.
> - Use o RBAC (controle de acesso baseado em função) para gerenciar a autorização, que também tem suporte pelo Key Vault.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Uma [conta de armazenamento do Azure](/azure/storage/storage-create-storage-account) usa uma credencial que consiste em um nome de conta e uma chave. A chave é gerada automaticamente e serve mais como uma "senha" em vez de uma chave criptográfica. Key Vault pode gerenciar essas chaves de conta de armazenamento, armazenando-as como [segredos Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Descrição geral

O recurso Key Vault conta de armazenamento gerenciado executa várias funções de gerenciamento em seu nome:

- Lista (sincroniza) chaves com uma conta de armazenamento do Azure.
- Gera novamente (gira) as chaves periodicamente.
- Gerencia chaves para contas de armazenamento e contas de armazenamento clássico.
- Os valores de chave nunca são retornados em resposta ao chamador.

Quando você usa o recurso chave de conta de armazenamento gerenciado:

- **Permita apenas Key Vault gerenciar suas chaves de conta de armazenamento.** Não tente gerenciá-los por conta própria, pois você interferirá com os processos de Key Vault.
- **Não permita que as chaves da conta de armazenamento sejam gerenciadas por mais de um objeto Key Vault**.
- **Não regenere manualmente as chaves da conta de armazenamento**. Recomendamos que você os gere novamente por meio de Key Vault.

O exemplo a seguir mostra como permitir que Key Vault gerenciem suas chaves de conta de armazenamento.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Autorizar Key Vault a acessar sua conta de armazenamento

> [!IMPORTANT]
> Um locatário do Azure AD fornece a cada aplicativo registrado uma **[entidade de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object)** , que serve como a identidade do aplicativo. A ID do aplicativo da entidade de serviço é usada ao fornecer autorização de ti para acessar outros recursos do Azure, por meio do controle de acesso baseado em função (RBAC). Como Key Vault é um aplicativo da Microsoft, ele é previamente registrado em todos os locatários do Azure AD com a mesma ID de aplicativo em cada nuvem do Azure:
> - Locatários do Azure AD na nuvem do Azure governamental usam `7e7c393b-45d0-48b1-a35e-2905ddf8183c`a ID do aplicativo.
> - Locatários do Azure AD na nuvem pública do Azure e todos os outros `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`usam a ID do aplicativo.

Antes que Key Vault possa acessar e gerenciar suas chaves de conta de armazenamento, você deve autorizar seu acesso à sua conta de armazenamento. O aplicativo Key Vault requer permissões para *listar* e *regenerar* chaves para sua conta de armazenamento. Essas permissões são habilitadas por meio da [função de serviço do operador chave de conta de armazenamento](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)de função RBAC interna. 

Atribua essa função à entidade de serviço Key Vault, limitando o escopo à sua conta de armazenamento, usando as etapas a seguir. Certifique-se de atualizar `$resourceGroupName`as `$storageAccountName`variáveis `$storageAccountKey`,, `$keyVaultName` e antes de executar o script:

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

Após a atribuição de função bem-sucedida, você deverá ver uma saída semelhante ao exemplo a seguir:

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

Se Key Vault já tiver sido adicionada à função em sua conta de armazenamento, você receberá uma mensagem *"a atribuição de função já existe".* ao. Você também pode verificar a atribuição de função usando a página "controle de acesso (IAM)" da conta de armazenamento na portal do Azure.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Dê permissão à sua conta de usuário para contas de armazenamento gerenciadas

>[!TIP] 
> Assim como o Azure AD fornece uma **entidade de serviço** para a identidade de um aplicativo, uma entidade de **usuário** é fornecida para a identidade de um usuário. A entidade de usuário pode receber autorização para acessar Key Vault, por meio de permissões de política de acesso Key Vault.

Usando a mesma sessão do PowerShell, atualize a política de acesso de Key Vault para contas de armazenamento gerenciadas. Esta etapa aplica permissões de conta de armazenamento à sua conta de usuário, garantindo que você possa acessar os recursos da conta de armazenamento gerenciado: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Observe que as permissões para contas de armazenamento não estão disponíveis na página "políticas de acesso" da conta de armazenamento no portal do Azure.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Adicionar uma conta de armazenamento gerenciado à sua instância do Key Vault

Usando a mesma sessão do PowerShell, crie uma conta de armazenamento gerenciado em sua instância de Key Vault. A `-DisableAutoRegenerateKey` opção especifica não gerar novamente as chaves da conta de armazenamento.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Após a adição bem-sucedida da conta de armazenamento sem nova geração de chave, você deverá ver uma saída semelhante ao exemplo a seguir:

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

### <a name="enable-key-regeneration"></a>Habilitar regeneração de chave

Se desejar que Key Vault regenerar as chaves da conta de armazenamento periodicamente, você poderá definir um período de regeneração. No exemplo a seguir, definimos um período de regeneração de três dias. Após três dias, Key Vault regenerará ' key1 ' e permutará a chave ativa de ' Key2 ' para ' key1 '.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Após a adição bem-sucedida da conta de armazenamento com a regeneração de chave, você deverá ver uma saída semelhante ao exemplo a seguir:

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

## <a name="next-steps"></a>Passos seguintes

- [Exemplos de chave de conta de armazenamento gerenciado](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- [Referência do Key Vault PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
