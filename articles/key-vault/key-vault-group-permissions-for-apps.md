---
title: Conceder permissão a aplicativos para acessar um cofre de chaves do Azure-Azure Key Vault | Microsoft Docs
description: Saiba como conceder permissão a muitos aplicativos para acessar um cofre de chaves
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 7c31c04137a8d36adfe41a18cbc276a45483b05b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467171"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Fornecer Key Vault autenticação com uma política de controle de acesso

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A maneira mais simples de autenticar um aplicativo baseado em nuvem para Key Vault é com uma identidade gerenciada; consulte [usar uma identidade gerenciada do serviço de aplicativo para acessar Azure Key Vault](managed-identity.md) para obter detalhes.  Se você estiver criando um aplicativo local, fazendo o desenvolvimento ou não puder usar uma identidade gerenciada, poderá registrar uma entidade de serviço manualmente e fornecer acesso ao seu cofre de chaves usando uma política de controle de acesso.  

O Key Vault dá suporte a até 1024 entradas de política de acesso, sendo que cada entrada concede um conjunto distinto de permissões a uma "entidade de segurança": por exemplo, é assim que o aplicativo de console no [início rápido da biblioteca de cliente Azure Key Vault para .net](quick-create-net.md) acessa o cofre de chaves.

Para obter detalhes completos sobre Key Vault controle de acesso, consulte [segurança de Azure Key Vault: gerenciamento de identidade e acesso](overview-security.md#identity-and-access-management). Para obter detalhes completos sobre [chaves, segredos e](about-keys-secrets-and-certificates.md) controle de acesso de certificados, consulte: 

- [Controle de acesso de chaves](about-keys-secrets-and-certificates.md#key-access-control)
- [Controle de acesso de segredos](about-keys-secrets-and-certificates.md#secret-access-control)
- [Controle de acesso de certificados](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Um cofre de chaves. Você pode usar um cofre de chaves existente ou criar um novo seguindo as etapas em um destes guias de início rápido:
   - [Criar um cofre de chaves com o CLI do Azure](quick-create-cli.md)
   - [Criar um cofre de chaves com Azure PowerShell](quick-create-powershell.md)
   - [Crie um cofre de chaves com o portal do Azure](quick-create-portal.md).
- O [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview). Como alternativa, você pode usar o [portal do Azure](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao cofre de chaves

Cada entrada de política de acesso do cofre de chaves concede um conjunto distinto de permissões a uma entidade de segurança:

- **Um aplicativo** Se o aplicativo for baseado em nuvem, você deverá [usar uma identidade gerenciada para acessar Azure Key Vault](managed-identity.md), se possível
- **Um grupo do Azure ad** Embora o Key Vault dê suporte apenas a entradas de política de acesso 1024, você pode adicionar vários aplicativos e usuários a um único grupo do Azure AD e, em seguida, adicionar esse grupo como uma única entrada à sua política de controle de acesso.
- **Um usuário** Não é **recomendável**fornecer aos usuários acesso direto a um cofre de chaves. O ideal é que os usuários sejam adicionados a um grupo do Azure AD, que, por sua vez, recebe acesso ao cofre de chaves. Consulte [segurança de Azure Key Vault: gerenciamento de identidade e acesso](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Obter o objectID

Para dar a um aplicativo, grupo do Azure AD ou acesso de usuário ao cofre de chaves, primeiro você deve obter seu objectId.

#### <a name="applications"></a>Aplicações

O objectId de um aplicativo corresponde à sua entidade de serviço associada. Para obter detalhes completos sobre entidades de serviço. consulte [objetos de aplicativo e entidade de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Há duas maneiras de obter um objectId para um aplicativo.  A primeira é registrar seu aplicativo com Azure Active Directory. Para fazer isso, siga as etapas no início rápido [registrar um aplicativo com a plataforma de identidade da Microsoft](../active-directory/develop/quickstart-register-app.md). Quando o registro for concluído, o objectID será listado como "ID do aplicativo (cliente)".

A segunda é criar uma entidade de serviço em uma janela de terminal. Com o CLI do Azure, use o comando [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) .

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

O objectId será listado na saída como `clientID`.

Com Azure PowerShell, use o cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) .


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

O objectId será listado na saída como `Id` (não `ApplicationId`).

#### <a name="azure-ad-groups"></a>Grupos do Azure AD

Você pode adicionar vários aplicativos e usuários a um grupo do Azure AD e, em seguida, conceder ao grupo acesso ao seu cofre de chaves.  Para obter mais detalhes, consulte a seção [criando e adicionando membros a um grupo do Azure ad](#creating-and-adding-members-to-an-azure-ad-group) , abaixo.

Para localizar o objectId de um grupo do Azure AD com o CLI do Azure, use o comando [AZ ad Group List](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) . Devido ao grande número de grupos que podem estar em sua organização, você também deve fornecer uma cadeia de caracteres de pesquisa para o parâmetro `--display-name`.

```azurecli-interactive
az ad group list --display-name <search-string>
```
O objectId será retornado no JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Para localizar o objectId de um grupo do Azure AD com Azure PowerShell, use o cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) . Devido ao grande número de grupos que podem estar em sua organização, você provavelmente também desejará fornecer uma cadeia de caracteres de pesquisa para o parâmetro `-SearchString`.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

Na saída, o objectId é listado como `Id`:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Utilizadores

Você também pode adicionar um usuário individual à política de controle de acesso de um cofre de chaves. **Não recomendamos isso.** Em vez disso, incentivamos você a adicionar usuários a um grupo do Azure AD e adicionar o grupo nas políticas.

Se, mesmo assim, você quiser encontrar um usuário com o CLI do Azure, use o comando [AZ ad User show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) , passando o endereço de email dos usuários para o parâmetro `--id`.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

O objectId do usuário será retornado na saída:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Para localizar um usuário com Azure PowerShell, use o cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) , passando o endereço de email dos usuários para o parâmetro `-UserPrincipalName`.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

O objectId do usuário será retornado na saída como `Id`.

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Fornecer acesso principal ao cofre de chaves

Agora que você tem um objectID de sua entidade de segurança, você pode criar uma política de acesso para o cofre de chaves que fornece permissões de Get, lista, definição e exclusão para chaves e segredos, além de quaisquer permissões adicionais que desejar.

Com o CLI do Azure, isso é feito passando o objectId para o comando [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Com Azure PowerShell, isso é feito passando o objectId para o cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) . 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ApplicationId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Criando e adicionando membros a um grupo do Azure AD

Você pode criar um grupo do Azure AD, adicionar aplicativos e usuários ao grupo e conceder ao grupo acesso ao seu cofre de chaves.  Isso permite que você adicione vários aplicativos a um cofre de chaves como uma entrada de política de acesso único e elimina a necessidade de fornecer aos usuários acesso direto ao cofre de chaves (o que desencorajamos). Para obter mais detalhes, consulte [gerenciar o acesso a aplicativos e recursos usando grupos de Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Pré-requisitos adicionais

Além dos [pré-requisitos acima](#prerequisites), você precisará de permissões para criar/editar grupos em seu locatário Azure Active Directory. Se você não tiver permissões, talvez seja necessário entrar em contato com o administrador do Azure Active Directory.

Se você pretende usar o PowerShell, também precisará do [módulo do PowerShell do Azure ad](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Criar um grupo de Azure Active Directory

Crie um novo grupo de Azure Active Directory usando o comando CLI do Azure [AZ ad Group Create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) ou o cmdlet Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) .


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

Em ambos os casos, anote os grupos recém-criados GroupId, pois será necessário para as etapas abaixo.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Localize os objectIds de seus aplicativos e usuários

Você pode encontrar as objectIds de seus aplicativos usando o CLI do Azure com o comando [AZ ad SP List](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) , com o parâmetro `--show-mine`.

```azurecli-interactive
az ad sp list --show-mine
```

Localize os objectIds de seus aplicativos usando Azure PowerShell com o cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) , passando uma cadeia de caracteres de pesquisa para o parâmetro `-SearchString`.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Para localizar as objectIds dos usuários, siga as etapas na seção [usuários](#users) , acima.

### <a name="add-your-applications-and-users-to-the-group"></a>Adicionar seus aplicativos e usuários ao grupo

Agora, adicione objectIds a seu grupo do Azure AD recém-criado.

Com o CLI do Azure, use o [membro do grupo AZ ad Add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add), passando o ObjectID para o parâmetro `--member-id`.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Com Azure PowerShell, use o cmdlet [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) , passando o ObjectID para o parâmetro `-MemberObjectId`.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Conceder ao grupo do AD acesso ao cofre de chaves

Por fim, conceda ao grupo do AD permissões para seu cofre de chaves usando o comando CLI do Azure [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) ou o cmdlet Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) . Para obter exemplos, consulte a seção [fornecer o aplicativo, o grupo do Azure ad ou o acesso do usuário ao seu cofre de chaves](#give-the-principal-access-to-your-key-vault) , acima.


## <a name="next-steps"></a>Passos seguintes

- [Segurança de Azure Key Vault: gerenciamento de acesso e identidade](overview-security.md#identity-and-access-management)
- [Fornecer autenticação de Key Vault com uma identidade gerenciada do serviço de aplicativo](managed-identity.md)
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- [Proteja seu cofre de chaves](key-vault-secure-your-key-vault.md).
- [Guia do desenvolvedor de Azure Key Vault](key-vault-developers-guide.md)
- Examinar [Azure Key Vault práticas recomendadas](key-vault-best-practices.md)
