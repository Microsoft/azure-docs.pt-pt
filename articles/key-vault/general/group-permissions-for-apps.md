---
title: Conceder permissão para candidaturas para aceder a um cofre de chaves Azure - Azure Key Vault / Microsoft Docs
description: Saiba como conceder permissão a muitas aplicações para aceder a um cofre chave
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 28765d3a4a0812f6f3631427432105fdc4650808
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126234"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Fornecer autenticação key vault com uma política de controlo de acesso

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A forma mais simples de autenticar uma aplicação baseada em nuvem para o Key Vault é com uma identidade gerida; ver [Utilizar uma identidade gerida pelo Serviço de Aplicações para aceder ao Cofre da Chave Azure](managed-identity.md) para obter mais detalhes.  Se estiver a criar uma aplicação on-prem, a fazer desenvolvimento local ou de outra forma incapaz de utilizar uma identidade gerida, pode, em vez disso, registar manualmente um serviço principal e fornecer acesso ao cofre-chave utilizando uma política de controlo de acesso.  

O cofre de chaves suporta até 1024 entradas de política de acesso, com cada entrada a conceder um conjunto distinto de permissões a um "principal": Por exemplo, é assim que a aplicação de consola na [biblioteca de clientes Azure Key Vault para .NET quickstart](../secrets/quick-create-net.md) acede ao cofre de chaves.

Para obter todos os detalhes sobre o controlo de acesso ao Cofre chave, consulte [a segurança do Cofre da Chave Azure: Gestão de identidade e acesso](overview-security.md#identity-and-access-management). Para obter todos os detalhes sobre o controlo de acessos, consulte: 

- [Chaves](../keys/index.yml)
- [Controlo de acesso a segredos](../secrets/index.yml)
- [Controlo de acesso a certificados](../certificates/index.yml)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Um cofre. Você pode usar um cofre de chaves existente, ou criar um novo seguindo os passos em um destes arranques rápidos:
   - [Crie um cofre-chave com o Azure CLI](../secrets/quick-create-cli.md)
   - [Crie um cofre com Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Crie um cofre-chave com o portal Azure](../secrets/quick-create-portal.md).
- [A Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview). Em alternativa, pode utilizar o [portal Azure](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao seu cofre chave

Cada entrada na política de acesso ao cofre garante um conjunto distinto de permissões a um principal:

- **Uma aplicação** Se a aplicação for baseada em nuvem, em vez disso deve [utilizar uma identidade gerida para aceder ao Cofre da Chave Azure,](managed-identity.md)se possível
- **Um grupo AD AZure** Embora o cofre-chave suporte apenas 1024 entradas de política de acesso, pode adicionar várias aplicações e utilizadores a um único grupo AZure AD e, em seguida, adicionar esse grupo como uma única entrada na sua política de controlo de acesso.
- **Um Utilizador** Dar aos utilizadores acesso direto a um cofre de chaves é **desencorajado.** Idealmente, os utilizadores devem ser adicionados a um grupo AZure AD, que por sua vez tem acesso ao cofre de chaves. Consulte a segurança do [Cofre da Chave Azure: Gestão de identidade e acesso](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Obtenha o objectID

Para dar uma aplicação, grupo AD Azure ou acesso do utilizador ao seu cofre de chaves, tem primeiro de obter o seu objectId.

#### <a name="applications"></a>Aplicações

O objectId para uma aplicação corresponde ao seu principal de serviço associado. Para mais detalhes sobre os principais serviços. ver [Aplicações e serviços principais objetos no Azure Ative Directory](../../active-directory/develop/app-objects-and-service-principals.md). 

Há duas maneiras de obter um objectId para uma aplicação.  A primeira é registar a sua candidatura no Azure Ative Directory. Para tal, siga os passos no quickstart [Register uma aplicação com a plataforma de identidade microsoft](../../active-directory/develop/quickstart-register-app.md). Quando o registo estiver concluído, o objectID será listado como o "ID de aplicação (cliente)".

A segunda é criar um diretor de serviço numa janela terminal. Com o CLI Azure, use o comando [ad sp create-for-rbac,](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) e forneça um nome principal de serviço único para a bandeira -n no formato "http:// &lt; meu-único serviço-nome principal &gt; ".

```azurecli-interactive
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name"
```

O objectId será listado na saída como `clientID` .

Com a Azure PowerShell, utilize o [cmdlet New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0)


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName <my-unique-service-principal-name>
```

O objectId será listado na saída como `Id` `ApplicationId` (não).

#### <a name="azure-ad-groups"></a>Grupos Azure AD

Pode adicionar várias aplicações e utilizadores a um grupo AZure AD e, em seguida, dar ao grupo acesso ao seu cofre chave.  Para mais detalhes, consulte a [Criação e adicionar membros a uma secção do grupo AD AZure,](#creating-and-adding-members-to-an-azure-ad-group) abaixo.

Para encontrar o objectId de um grupo Azure AD com o Azure CLI, use o comando [da lista de anúncios az.](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) Devido ao grande número de grupos que podem estar na sua organização, também deve fornecer uma cadeia de pesquisa para o `--display-name` parâmetro.

```azurecli-interactive
az ad group list --display-name <search-string>
```
O objectId será devolvido no JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Para encontrar o objectId de um grupo AD Azure com Azure PowerShell, utilize o cmdlet [Get-AzADGroup.](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) Devido ao grande número de grupos que podem estar na sua organização, provavelmente também desejará fornecer uma cadeia de pesquisa ao `-SearchString` parâmetro.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

Na saída, o objectId é listado `Id` como:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Utilizadores

Também pode adicionar um utilizador individual à política de controlo de acesso de um cofre de chaves. **Não recomendamos isto.** Em vez disso, encorajamo-lo a adicionar utilizadores a um grupo AZure AD e adicionar o grupo sobre as políticas.

Se desejar encontrar um utilizador com o Azure CLI, utilize o comando de exibição do [utilizador az,](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) passando o endereço de e-mail dos utilizadores para o `--id` parâmetro.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

O objectid do utilizador será devolvido na saída:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Para encontrar um utilizador com a Azure PowerShell, utilize o cmdlet [Get-AzADUser,](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) passando o endereço de e-mail dos utilizadores para o `-UserPrincipalName` parâmetro.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

O objectId do utilizador será devolvido na saída como `Id` .

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Dê ao principal acesso ao seu cofre de chaves

Agora que tem um objectID do seu principal, pode criar uma política de acesso para o seu cofre chave que lhe dá obter, listar, definir e eliminar permissões para ambas as chaves e segredos, além de quaisquer permissões adicionais que deseje.

Com o Azure CLI, isto é feito passando o objectId para o comando [de definição de keyvault az.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Com a Azure PowerShell, isto é feito passando o objectId para o [cmdlet Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Criar e adicionar membros a um grupo AZure AD

Pode criar um grupo AZure AD, adicionar aplicações e utilizadores ao grupo e dar ao grupo acesso ao cofre de chaves.  Isto permite-lhe adicionar uma série de aplicações a um cofre chave como uma entrada de política de acesso único, e elimina a necessidade de dar aos utilizadores acesso direto ao seu cofre chave (o que desencorajamos). Para obter mais detalhes, consulte [a aplicação Manage e o acesso a recursos utilizando grupos Azure Ative Directory](../../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Pré-requisitos adicionais

Além dos [pré-requisitos acima,](#prerequisites)você precisará de permissões para criar/editar grupos no seu inquilino Azure Ative Directory. Se não tiver permissões, poderá ter de contactar o administrador do Azure Ative Directory.

Se pretender utilizar o PowerShell, também necessitará do [módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Criar um grupo de Diretório Ativo Azure

Crie um novo grupo de diretório Azure Ative utilizando o grupo ad Azure CLI [az,](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) ou o cmdlet Azure PowerShell [New-AzureADGroup.](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0)


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

Em qualquer dos casos, tome nota sobre os grupos recém-criados GroupId, pois você precisará dele para os passos abaixo.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Encontre os objectIds das suas aplicações e utilizadores

Pode encontrar os objectIds das suas aplicações utilizando o CLI Azure com o comando [da lista ad sp az,](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) com o `--show-mine` parâmetro.

```azurecli-interactive
az ad sp list --show-mine
```

Encontre os objectIds das suas aplicações utilizando o Azure PowerShell com o cmdlet [Get-AzADServicePrincipal,](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) passando uma cadeia de pesquisa para o `-SearchString` parâmetro.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Para encontrar os objectIds dos seus Utilizadores, siga os passos na secção [Utilizadores,](#users) acima.

### <a name="add-your-applications-and-users-to-the-group"></a>Adicione as suas aplicações e utilizadores ao grupo

Agora, adicione os objectIds ao seu grupo AZure AD recém-criado.

Com o Azure CLI, use o [az ad membro do grupo adicionar](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add), passando o objectId para o `--member-id` parâmetro.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Com a Azure PowerShell, utilize o [cmdlet Add-AzADGroupMember,](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) passando o objectId para o `-MemberObjectId` parâmetro.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Dê ao grupo AD acesso ao seu cofre de chaves

Por último, dê permissões ao grupo AD para o seu cofre de chaves utilizando o comando [de definição de chave Azure](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) CLI az, ou o cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) Por exemplo, consulte a [aplicação Give, o grupo AD Azure ou o acesso do utilizador à secção do cofre de chaves,](#give-the-principal-access-to-your-key-vault) acima.

A aplicação também precisa de pelo menos uma função de Gestão de Identidade e Acesso (IAM) atribuída ao cofre-chave. Caso contrário, não poderá iniciar sessão e falhará com direitos insuficientes de acesso à subscrição.

> [!WARNING]
> Os grupos AD Azure com Identidades Geridas podem exigir até 8 horas para refrescar o token e tornar-se eficazes.

## <a name="next-steps"></a>Passos seguintes

- [Segurança do Cofre Azure Key: Gestão de identidade e acesso](overview-security.md#identity-and-access-management)
- [Fornecer autenticação key vault com uma identidade gerida do Serviço de Aplicações](managed-identity.md)
- [Fixe o cofre da chave).](secure-your-key-vault.md)
- [Guia de desenvolvedores do Azure Key Vault](developers-guide.md)
- Rever [as melhores práticas do Azure Key Vault](best-practices.md)
