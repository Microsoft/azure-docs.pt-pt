---
title: Conta de armazenamento gerenciado Azure Key Vault-versão do PowerShell
description: O recurso de conta de armazenamento gerenciado fornece uma integração direta, entre Azure Key Vault e uma conta de armazenamento do Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 225d9b715c56e4813a8e26d881c876e7bd498155
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204212"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Gerenciar chaves de conta de armazenamento com Key Vault e Azure PowerShell

Uma conta de armazenamento do Azure usa credenciais que compreendem um nome de conta e uma chave. A chave é gerada automaticamente e serve como uma senha, em vez de uma como uma chave de criptografia. Key Vault gerencia chaves de conta de armazenamento armazenando-as como [segredos Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

Você pode usar o Key Vault recurso chave de conta de armazenamento gerenciado para listar (sincronizar) chaves com uma conta de armazenamento do Azure e regenerar (girar) as chaves periodicamente. Você pode gerenciar chaves para contas de armazenamento e contas de armazenamento clássicas.

Ao usar o recurso chave de conta de armazenamento gerenciado, considere os seguintes pontos:

- Os valores de chave nunca são retornados em resposta a um chamador.
- Somente Key Vault deve gerenciar suas chaves de conta de armazenamento. Não gerencie as chaves por conta própria e evite interferir nos processos de Key Vault.
- Apenas um único objeto Key Vault deve gerenciar chaves de conta de armazenamento. Não permita o gerenciamento de chaves de vários objetos.
- Você pode solicitar Key Vault para gerenciar sua conta de armazenamento com uma entidade de usuário, mas não com uma entidade de serviço.
- Regenerar chaves usando apenas Key Vault. Não regenere manualmente as chaves da conta de armazenamento.

É recomendável usar a integração do armazenamento do Azure com o Azure Active Directory (Azure AD), o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft. A integração do Azure AD está disponível para [BLOBs e filas do Azure](../storage/common/storage-auth-aad.md)e fornece acesso baseado em token OAuth2 ao armazenamento do Azure (assim como Azure Key Vault).

O Azure AD permite que você autentique seu aplicativo cliente usando uma identidade de aplicativo ou de usuário, em vez de credenciais de conta de armazenamento. Você pode usar uma [identidade gerenciada do Azure ad](/azure/active-directory/managed-identities-azure-resources/) ao executar o no Azure. Identidades gerenciadas eliminam a necessidade de autenticação de cliente e armazenamento de credenciais no ou com seu aplicativo.

O Azure AD usa o RBAC (controle de acesso baseado em função) para gerenciar a autorização, que também tem suporte pelo Key Vault.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>ID do aplicativo da entidade de serviço

Um locatário do Azure AD fornece a cada aplicativo registrado uma [entidade de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object). A entidade de serviço serve como a ID do aplicativo, que é usada durante a configuração de autorização para acesso a outros recursos do Azure via RBAC.

Key Vault é um aplicativo da Microsoft que é previamente registrado em todos os locatários do Azure AD. Key Vault é registrado sob a mesma ID de aplicativo em cada nuvem do Azure.

| locatários | Nuvem | ID da aplicação |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure público | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outros  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia, primeiro você deve fazer o seguinte:

- [Instale o módulo Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Criar um cofre de chaves](quick-create-powershell.md)
- [Criar uma conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-powershell). O nome da conta de armazenamento deve usar apenas letras minúsculas e números. O comprimento do nome deve ter entre 3 e 24 caracteres.
      

## <a name="manage-storage-account-keys"></a>Gerenciar chaves de conta de armazenamento

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Autentique a sessão do PowerShell usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) . 

```azurepowershell-interactive
Connect-AzAccount
```
Se você tiver várias assinaturas do Azure, poderá listá-las usando o cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) e especificar a assinatura que deseja usar com o cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) . 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Definir variáveis

Primeiro, defina as variáveis a serem usadas pelos cmdlets do PowerShell nas etapas a seguir. Certifique-se de atualizar <YourResourceGroupName>os <YourStorageAccountName>espaços reservados <YourKeyVaultName> ,, e, e defina $keyVaultSpAppId `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` como (conforme especificado na [ID do aplicativo da entidade de serviço](#service-principal-application-id), acima).

Também usaremos os cmdlets Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) e [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) para obter sua ID de usuário e o contexto de sua conta de armazenamento do Azure.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>Conceder Key Vault acesso à sua conta de armazenamento

Antes que Key Vault possa acessar e gerenciar suas chaves de conta de armazenamento, você deve autorizar seu acesso à sua conta de armazenamento. O aplicativo Key Vault requer permissões para *listar* e *regenerar* chaves para sua conta de armazenamento. Essas permissões são habilitadas por meio da [função de serviço do operador chave de conta de armazenamento](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)de função RBAC interna. 

Atribua essa função à entidade de serviço Key Vault, limitando o escopo à sua conta de armazenamento, usando o cmdlet Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) .

```azurepowershell-interactive
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

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Dê permissão à sua conta de usuário para contas de armazenamento gerenciadas

Use o cmdlet Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) para atualizar a política de acesso Key Vault e conceder permissões de conta de armazenamento para sua conta de usuário.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Observe que as permissões para contas de armazenamento não estão disponíveis na página "políticas de acesso" da conta de armazenamento no portal do Azure.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Adicionar uma conta de armazenamento gerenciado à sua instância do Key Vault

Use o cmdlet Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) para criar uma conta de armazenamento gerenciado na instância do Key Vault. A `-DisableAutoRegenerateKey` opção especifica não gerar novamente as chaves da conta de armazenamento.

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

Se desejar que Key Vault regenerar as chaves da conta de armazenamento periodicamente, você poderá usar o cmdlet Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) para definir um período de regeneração. Neste exemplo, definimos um período de regeneração de três dias. Após três dias, Key Vault regenerará ' Key2 ' e alternará a chave ativa de ' Key2 ' para ' key1 '.

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

## <a name="shared-access-signature-tokens"></a>Tokens de assinatura de acesso compartilhado

Você também pode solicitar Key Vault para gerar tokens de assinatura de acesso compartilhado. Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Você pode conceder aos clientes acesso aos recursos em sua conta de armazenamento sem compartilhar suas chaves de conta. Uma assinatura de acesso compartilhado fornece uma maneira segura de compartilhar seus recursos de armazenamento sem comprometer suas chaves de conta.

Os comandos nesta seção concluem as seguintes ações:

- Defina uma definição de assinatura de acesso compartilhado de conta. 
- Crie um token de assinatura de acesso compartilhado de conta para serviços BLOB, arquivo, tabela e fila. O token é criado para os tipos de recursos Service, container e Object. O token é criado com todas as permissões, via HTTPS e com as datas de início e término especificadas.
- Defina uma definição de assinatura de acesso compartilhado de armazenamento gerenciado Key Vault no cofre. A definição tem o URI do modelo do token de assinatura de acesso compartilhado que foi criado. A definição tem o tipo `account` de assinatura de acesso compartilhado e é válida por N dias.
- Verifique se a assinatura de acesso compartilhado foi salva em seu cofre de chaves como um segredo.
- 
### <a name="set-variables"></a>Definir variáveis

Primeiro, defina as variáveis a serem usadas pelos cmdlets do PowerShell nas etapas a seguir. Certifique-se de atualizar <YourStorageAccountName> os <YourKeyVaultName> espaços reservados e.

Também usaremos os cmdlets do Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) para obter o contexto da sua conta de armazenamento do Azure.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Criar um token de assinatura de acesso compartilhado

Crie uma definição de assinatura de acesso compartilhado usando os cmdlets do Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) .
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
O valor de $sasToken será semelhante a este.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Gerar uma definição de assinatura de acesso compartilhado

Use o cmdlet Azure PowerShell [set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) para criar uma definição de assinatura de acesso compartilhado.  Você pode fornecer o nome de sua escolha para o `-Name` parâmetro.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Verificar a definição de assinatura de acesso compartilhado

Você pode verificar se a definição da assinatura de acesso compartilhado foi armazenada no cofre de chaves usando o cmdlet Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) .

Primeiro, localize a definição de assinatura de acesso compartilhado no cofre de chaves.

```azurepowershell-interactive
Get-AzKeyVaultSecret -vault-name <YourKeyVaultName>
```

O segredo correspondente à sua definição de SAS terá essas propriedades:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Agora você pode usar o cmdlet [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) e a propriedade `Name` Secret para exibir o conteúdo desse segredo.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

A saída desse comando mostrará a cadeia de caracteres de definição de SAS.


## <a name="next-steps"></a>Passos seguintes

- [Exemplos de chave de conta de armazenamento gerenciado](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- [Referência do Key Vault PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
