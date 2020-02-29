---
title: Conta de armazenamento gerida pela Azure Key Vault - versão PowerShell
description: A funcionalidade de conta de armazenamento gerida proporciona uma integração perfeita, entre o Azure Key Vault e uma conta de armazenamento Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 833f78d89a1a9033e62c10c3b16c5adfc65e1da4
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195129"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Gerir as chaves da conta de armazenamento com key vault e Azure PowerShell

Uma conta de armazenamento Azure usa credenciais que incluem um nome de conta e uma chave. A chave é autogerada e serve como uma senha, em vez de uma chave criptográfica. Key Vault gere as chaves da conta de armazenamento armazenando-as como [segredos do Cofre chave.](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets) 

Pode utilizar a função de chave de armazenamento gerida pelo Key Vault para listar (sincronizar) chaves com uma conta de armazenamento Azure e regenerar (rodar) as teclas periodicamente. Você pode gerir chaves para contas de armazenamento e contas de armazenamento clássicas.

Quando utilizar a função chave da conta de armazenamento gerida, considere os seguintes pontos:

- Os valores-chave nunca são devolvidos em resposta a um ouvinte.
- Só o Cofre chave deve gerir as chaves da sua conta de armazenamento. Não gereas as chaves sozinho e evita interferir nos processos do Cofre chave.
- Apenas um único objeto key vault deve gerir as chaves da conta de armazenamento. Não permita a gestão da chave a partir de vários objetos.
- Pode solicitar ao Key Vault que gere a sua conta de armazenamento com um diretor de utilizador, mas não com um diretor de serviço.
- Regenerar as teclas usando apenas o Cofre chave. Não regenerar manualmente as chaves da sua conta de armazenamento.

Recomendamos a utilização da integração do Armazenamento Azure com o Azure Ative Directory (Azure AD), o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. A integração da Azure AD está disponível para [blobs e filas Azure,](../storage/common/storage-auth-aad.md)e fornece acesso baseado em token OAuth2 ao Armazenamento Azure (tal como o Azure Key Vault).

A Azure AD permite-lhe autenticar a sua aplicação cliente utilizando uma aplicação ou identidade de utilizador, em vez de credenciais de conta de armazenamento. Pode usar uma identidade gerida pelo [Azure](/azure/active-directory/managed-identities-azure-resources/) Quando correr em Azure. As identidades geridas removem a necessidade de autenticação do cliente e armazenam credenciais dentro ou com a sua aplicação.

A Azure AD utiliza o controlo de acesso baseado em funções (RBAC) para gerir a autorização, que também é apoiada pela Key Vault.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>ID de aplicação principal de serviço

Um inquilino da AD Azure fornece cada pedido registado com um [diretor de serviço.](/azure/active-directory/develop/developer-glossary#service-principal-object) O diretor de serviço serve como id de aplicação, que é usado durante a configuração de autorização para acesso a outros recursos Azure via RBAC.

Key Vault é uma aplicação da Microsoft que está pré-registada em todos os inquilinos da AD Azure. O Cofre chave está registado sob o mesmo ID de aplicação em cada nuvem Azure.

| Inquilinos | Cloud | ID da aplicação |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Público de Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outros  | Qualquer | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, primeiro deve fazer o seguinte:

- [Instale o módulo PowerShell Azure](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Criar um cofre chave](quick-create-powershell.md)
- [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-powershell). O nome da conta de armazenamento deve utilizar apenas letras minúsculas e números. O comprimento do nome deve ser entre 3 e 24 caracteres.
      

## <a name="manage-storage-account-keys"></a>Gerir as chaves da conta de armazenamento

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Autenticar a sua sessão PowerShell utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 

```azurepowershell-interactive
Connect-AzAccount
```
Se tiver várias subscrições do Azure, pode enumerar utilizando o cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) e especificar a subscrição que pretende utilizar com o cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Definir variáveis

Em primeiro lugar, detete as variáveis a utilizar pelos cmdlets PowerShell nos seguintes passos. Certifique-se de atualizar os <YourResourceGroupName>, <YourStorageAccountName>e <YourKeyVaultName> espaços reservados, e definir $keyVaultSpAppId para `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (conforme especificado no ID de [aplicação principal](#service-principal-application-id)do Serviço , acima).

Também usaremos os cmdlets Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) e [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) para obter o seu ID de utilizador e o contexto da sua conta de armazenamento Azure.

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

### <a name="give-key-vault-access-to-your-storage-account"></a>Dê acesso ao Cofre chave à sua conta de armazenamento

Antes que o Key Vault possa aceder e gerir as chaves da sua conta de armazenamento, deve autorizar o seu acesso à sua conta de armazenamento. O pedido key vault requer permissões para *listar* e *regenerar* chaves para a sua conta de armazenamento. Estas permissões são ativadas através da [função](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)de serviço de serviço de serviço de serviço de serviço de serviço de conta de armazenamento rBAC incorporada. 

Atribuir esta função ao principal de serviço key vault, limitando o âmbito à sua conta de armazenamento, utilizando o cmdlet Azure PowerShell [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0)

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Após uma atribuição de funções bem sucedida, deve ver a saída semelhante ao seguinte exemplo:

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

Se o Key Vault já tiver sido adicionado ao papel na sua conta de armazenamento, receberá um "A atribuição de *funções já existe".* um erro. Também pode verificar a atribuição de funções, utilizando a página da conta de armazenamento "Controle de Acesso (IAM)" no portal Azure.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Dê permissão à sua conta de utilizador para gerir contas de armazenamento

Utilize o Cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) para atualizar a política de acesso ao Cofre chave e conceder permissões de conta de armazenamento na sua conta de utilizador.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Note que as permissões para contas de armazenamento não estão disponíveis na página da conta de armazenamento "Políticas de acesso" no portal Azure.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Adicione uma conta de armazenamento gerida à sua instância Key Vault

Utilize o Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet para criar uma conta de armazenamento gerida na sua instância Key Vault. O interruptor de `-DisableAutoRegenerateKey` especifica NÃO regenerar as chaves da conta de armazenamento.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Após a adição bem sucedida da conta de armazenamento sem regeneração de chaves, deve ver a saída semelhante ao seguinte exemplo:

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

### <a name="enable-key-regeneration"></a>Permitir a regeneração da chave

Se pretender que o Key Vault regenerar periodicamente as chaves da sua conta de armazenamento, pode utilizar o cmdlet Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) para definir um período de regeneração. Neste exemplo, estabelecemos um período de regeneração de três dias. Após três dias, o Key Vault regenerará o 'key2' e trocará a tecla ativa de 'key2' para 'key1'.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Após a adição bem sucedida da conta de armazenamento com regeneração de chaves, deve ver a saída semelhante ao seguinte exemplo:

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

## <a name="shared-access-signature-tokens"></a>Fichas de assinatura de acesso partilhado

Também pode pedir ao Key Vault que gere fichas de assinatura de acesso partilhado. Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Pode conceder aos clientes acesso aos recursos na sua conta de armazenamento sem partilhar as chaves da sua conta. Uma assinatura de acesso partilhado fornece-lhe uma forma segura de partilhar os seus recursos de armazenamento sem comprometer as chaves da sua conta.

Os comandos desta secção completam as seguintes ações:

- Definir uma definição de assinatura de acesso partilhado de conta. 
- Crie um símbolo de assinatura de acesso partilhado de conta para os serviços Blob, File, Table e Queue. O símbolo é criado para tipos de recursos Serviço, Contentor e Objeto. O símbolo é criado com todas as permissões, em https, e com as datas de início e fim especificadas.
- Delineie uma definição de assinatura de acesso compartilhado de armazenamento no cofre. A definição tem o modelo URI do símbolo de assinatura de acesso partilhado que foi criado. A definição tem o tipo de assinatura de acesso partilhado `account` e é válida para os dias N.
- Verifique se a assinatura de acesso partilhado foi guardada no seu cofre como segredo.
- 
### <a name="set-variables"></a>Definir variáveis

Em primeiro lugar, detete as variáveis a utilizar pelos cmdlets PowerShell nos seguintes passos. Certifique-se de atualizar os <YourStorageAccountName> e <YourKeyVaultName> espaços reservados.

Também usaremos os cmdlets Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) para obter o contexto da sua conta de armazenamento Azure.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Criar um símbolo de assinatura de acesso partilhado

Crie uma definição de assinatura de acesso partilhado utilizando os cmdlets Azure PowerShell [New-AzStorageAccountSASToken.](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0)
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
O valor da $sasToken será semelhante a este.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Gerar uma definição de assinatura de acesso partilhado

Utilize o Conjunto Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) cmdlet para criar uma definição de assinatura de acesso partilhado.  Pode fornecer o nome da sua escolha ao parâmetro `-Name`.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifique a definição de assinatura de acesso partilhado

Pode verificar se a definição de assinatura de acesso partilhado foi armazenada no seu cofre de chaves utilizando o cmdlet Azure PowerShell [Get-AzKeyVaultSecret.](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0)

Primeiro, encontre a definição de assinatura de acesso partilhado no seu cofre chave.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

O segredo correspondente à sua definição SAS terá estas propriedades:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Agora pode usar o cmdlet [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) e a propriedade secreta `Name` para ver o conteúdo desse segredo.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

A saída deste comando mostrará a sua cadeia de definição SAS.


## <a name="next-steps"></a>Passos seguintes

- [Amostras-chave da conta de armazenamento gerida](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- [Referência de Key Vault PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
