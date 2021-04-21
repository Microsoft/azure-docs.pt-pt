---
title: Conta de armazenamento gerida Azure Key Vault - Versão PowerShell
description: A funcionalidade de conta de armazenamento gerida proporciona uma integração perfeita, entre o Azure Key Vault e uma conta de armazenamento Azure.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa4daf7c8d951a7b42479533a3a5a50c06c5c144
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748489"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Gerir chaves de conta de armazenamento com Key Vault e Azure PowerShell
> [!IMPORTANT]
> Recomendamos a utilização da integração do Azure Storage com o Azure Ative Directory (Azure AD), o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. A integração AD AD está disponível para [bolhas e filas Azure,](../../storage/common/storage-auth-aad.md)e fornece acesso baseado em símbolos OAuth2 ao Azure Storage (tal como o Azure Key Vault).
> O Azure AD permite-lhe autenticar a sua aplicação de cliente utilizando uma aplicação ou identidade de utilizador, em vez de credenciais de conta de armazenamento. Você pode usar uma [identidade gerida Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) quando você correr em Azure. Identidades geridas removem a necessidade de autenticação do cliente e armazenam credenciais dentro ou com a sua aplicação. Utilize a solução abaixo apenas quando a autenticação AZure AD não for possível.

Uma conta de armazenamento Azure utiliza credenciais que incluem um nome de conta e uma chave. A chave é autogerada e serve como senha, em vez de uma chave criptográfica. Key Vault gere as chaves da conta de armazenamento regenerando-as periodicamente na conta de armazenamento e fornece fichas de assinatura de acesso partilhado para acesso delegado a recursos na sua conta de armazenamento.

Pode utilizar a função de conta de armazenamento gerida do Key Vault para listar as teclas (sincronização) com uma conta de armazenamento Azure e regenerar (rodar) as teclas periodicamente. Pode gerir as chaves tanto para contas de armazenamento como para contas de armazenamento clássicas.

Quando utilizar a função chave da conta de armazenamento gerida, considere os seguintes pontos:

- Os valores-chave nunca são devolvidos em resposta a um chamador.
- Só o Key Vault deve gerir as chaves da sua conta de armazenamento. Não maneie as chaves sozinho e evite interferir com os processos do Key Vault.
- Apenas um único objeto do Key Vault deve gerir as chaves da conta de armazenamento. Não permita a gestão de chaves a partir de vários objetos.
- Regenerar as chaves usando apenas o Cofre de Chaves. Não regenerar manualmente as chaves da conta de armazenamento.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>ID de aplicação do principal de serviço

Um inquilino da AD Azure fornece a cada pedido registado um [principal de serviço.](../../active-directory/develop/developer-glossary.md#service-principal-object) O diretor de serviço serve como iD de aplicação, que é usado durante a instalação de autorização para acesso a outros recursos Azure via Azure RBAC.

Key Vault é uma aplicação da Microsoft pré-registada em todos os inquilinos da AD Azure. O Key Vault está registado sob o mesmo ID de aplicação em cada nuvem Azure.

| Inquilinos | Cloud | ID da aplicação |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure público | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outro  | Qualquer | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, deve primeiro fazer o seguinte:

- [Instale o módulo Azure PowerShell](/powershell/azure/install-az-ps).
- [Criar um cofre de chaves](quick-create-powershell.md)
- [Criar uma conta de armazenamento Azure](../../storage/common/storage-account-create.md?tabs=azure-powershell). O nome da conta de armazenamento deve utilizar apenas letras e números minúsculos. O comprimento do nome deve ter entre 3 e 24 caracteres.


## <a name="manage-storage-account-keys"></a>Gerir chaves de conta de armazenamento

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Autenticar a sua sessão PowerShell utilizando o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```azurepowershell-interactive
Connect-AzAccount
```
Se tiver várias subscrições Azure, pode listá-las usando o cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) e especificar a subscrição que pretende utilizar com o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Definir variáveis

Em primeiro lugar, desa estale as variáveis a utilizar pelos cmdlets PowerShell nos passos seguintes. Certifique-se de atualizar os espaços reservados "YourResourceGroupName", "YourStorageAccountName" e "YourKeyVaultName" e definir $keyVaultSpAppId `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (conforme especificado no [ID da aplicação principal de serviço,](#service-principal-application-id)acima).

Também utilizaremos os cmdlets Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext) e [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) para obter o seu ID do utilizador e o contexto da sua conta de armazenamento Azure.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> Para a conta de armazenamento clássico, utilize "primário" e "secundário" para $storageAccountKey <br>
> Utilize 'Get-AzResource -Nome "ClassicStorageAccountName" -ResourceGroupName $resourceGroupName' em vez de 'Get-AzStorageAccount' para conta de armazenamento clássico

### <a name="give-key-vault-access-to-your-storage-account"></a>Dê ao Key Vault acesso à sua conta de armazenamento

Antes de o Key Vault poder aceder e gerir as chaves da sua conta de armazenamento, tem de autorizar o seu acesso à sua conta de armazenamento. A aplicação Key Vault requer permissões para *listar* e *regenerar* chaves para a sua conta de armazenamento. Estas permissões são ativadas através da [função](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role)incorporada Azure Fun Storage Account Key Operator Service Role Role Role .

Atribua esta função ao principal do serviço Key Vault, limitando o âmbito à sua conta de armazenamento, utilizando o cmdlet Azure PowerShell [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment)

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Após uma atribuição de funções bem sucedida, deverá ver uma saída semelhante ao seguinte exemplo:

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

Se o Key Vault já tiver sido adicionado ao papel na sua conta de armazenamento, receberá um *"A atribuição de funções já existe".* . Também pode verificar a atribuição de funções, utilizando a página "Controlo de acesso (IAM)" na página Azure.

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Dê permissão à sua conta de utilizador para contas de armazenamento geridas

Utilize o cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para atualizar a política de acesso ao Cofre-Chave e conceder permissões de conta de armazenamento na sua conta de utilizador.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Note que as permissões para contas de armazenamento não estão disponíveis na página "Políticas de acesso" da conta de armazenamento no portal Azure.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Adicione uma conta de armazenamento gerida à sua instância Key Vault

Utilize o cmdlet Azure PowerShell [Add-AzKeyVaagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) para criar uma conta de armazenamento gerida na sua instância Key Vault. O  `-DisableAutoRegenerateKey` interruptor especifica NÃO para regenerar as chaves da conta de armazenamento.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Após a adição bem sucedida da conta de armazenamento sem regeneração chave, deverá ver uma saída semelhante ao seguinte exemplo:

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

### <a name="enable-key-regeneration"></a>Permitir a regeneração de chaves

Se quiser que o Key Vault regenerar periodicamente as chaves da sua conta de armazenamento, pode utilizar o cmdlet Azure PowerShell [Add-AzKeyVaagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) para definir um período de regeneração. Neste exemplo, estabelecemos um período de regeneração de três dias. Quando é hora de rodar, o Key Vault regenera a chave que não está ativa e, em seguida, define a chave recém-criada como ativa. Apenas uma das chaves é usada para emitir fichas SAS a qualquer momento. Esta é a chave ativa.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Após a adição bem sucedida da conta de armazenamento com regeneração chave, deve ver uma saída semelhante ao seguinte exemplo:

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

Também pode pedir ao Key Vault para gerar fichas de assinatura de acesso partilhado. Uma assinatura de acesso partilhado proporciona acesso delegado aos recursos na sua conta de armazenamento. Pode conceder aos clientes acesso a recursos na sua conta de armazenamento sem partilhar as chaves da sua conta. Uma assinatura de acesso partilhado proporciona-lhe uma forma segura de partilhar os seus recursos de armazenamento sem comprometer as chaves da sua conta.

Os comandos nesta secção completam as seguintes ações:

- Desacione uma definição de assinatura de acesso partilhado por conta.
- Crie um token de assinatura de acesso partilhado de conta para os serviços Blob, File, Table e Queue. O token é criado para tipos de recursos Serviço, Contentor e Objeto. O token é criado com todas as permissões, mais https, e com as datas de início e fim especificadas.
- Desafine uma definição de assinatura de acesso partilhado de armazenamento no cofre. A definição tem o modelo URI do token de assinatura de acesso partilhado que foi criado. A definição tem o tipo de assinatura de acesso partilhado `account` e é válida para os dias N.
- Verifique se a assinatura de acesso partilhado foi guardada no cofre como segredo.
-
### <a name="set-variables"></a>Definir variáveis

Em primeiro lugar, desa estale as variáveis a utilizar pelos cmdlets PowerShell nos passos seguintes. Certifique-se de atualizar os <YourStorageAccountName> <YourKeyVaultName> espaços reservados.

Também usaremos os cmdlets Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) para obter o contexto da sua conta de armazenamento Azure.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Criar um token de assinatura de acesso partilhado

Crie uma definição de assinatura de acesso partilhado utilizando os cmdlets Azure [PowerShell New-AzStorageAccountSASToken.](/powershell/module/az.storage/new-azstorageaccountsastoken)

```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
O valor de $sasToken será semelhante a este.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Gerar uma definição de assinatura de acesso partilhado

Utilize o cmdlet de assinatura Azure PowerShell [Set-AzKeyVaagedStorageStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) para criar uma definição de assinatura de acesso partilhado.  Pode fornecer o nome da sua escolha ao `-Name` parâmetro.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifique a definição de assinatura de acesso partilhado

Pode verificar se a definição de assinatura de acesso partilhado foi armazenada no seu cofre de chaves utilizando o cmdlet Azure PowerShell [Get-AzKeyVaultSecret.](/powershell/module/az.keyvault/get-azkeyvaultsecret)

Primeiro, encontre a definição de assinatura de acesso partilhado no cofre da chave.

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

Agora pode utilizar o [cmdlet Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) com os `VaultName` parâmetros e `Name` parâmetros para visualizar o conteúdo desse segredo.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

A saída deste comando mostrará a sua cadeia de definição SAS.

## <a name="next-steps"></a>Passos seguintes

- [Amostras-chave da conta de armazenamento gerida](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Referência PowerShell do cofre de chaves](/powershell/module/az.keyvault/#key_vault)
