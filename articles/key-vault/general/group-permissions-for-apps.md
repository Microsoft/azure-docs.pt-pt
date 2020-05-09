---
title: Autorize os pedidos de acesso a um cofre de chaves Azure - Cofre chave Azure [ Cofre chave ] Microsoft Docs
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
ms.openlocfilehash: db6b8b2ff199b7b26d0c641ded31a5c1417468b9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901266"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Forneça a autenticação do Cofre chave com uma política de controlo de acesso

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A forma mais simples de autenticar uma aplicação baseada na nuvem no Key Vault é com uma identidade gerida; ver Utilize um Serviço de [Aplicações gerido identidade para aceder ao Cofre de Chaves Azure](managed-identity.md) para obter mais detalhes.  Se estiver a criar uma aplicação on-prem, a fazer desenvolvimento local ou a não conseguir utilizar uma identidade gerida, pode, em vez disso, registar manualmente um serviço principal e fornecer acesso ao seu cofre chave utilizando uma política de controlo de acesso.  

O cofre de chaves suporta até 1024 entradas de política de acesso, com cada entrada a conceder um conjunto distinto de permissões a um "principal": Por exemplo, é assim que a aplicação de consola na biblioteca de [clientes Azure Key Vault para .NET quickstart](../secrets/quick-create-net.md) acessa o cofre chave.

Para mais detalhes sobre o controlo de acesso ao Cofre chave, consulte a [segurança do Cofre chave Azure: Identidade e gestão de acesso](overview-security.md#identity-and-access-management). Para mais detalhes sobre o controlo de acesso, consulte: 

- [Chaves](../keys/index.yml)
- [Controlo de acesso a segredos](../secrets/index.yml)
- [Controlo de acesso de certificados](../certificates/index.yml)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Um cofre chave. Você pode usar um cofre chave existente, ou criar um novo seguindo os passos em um destes quickstarts:
   - [Crie um cofre chave com o Azure CLI](../secrets/quick-create-cli.md)
   - [Crie um cofre chave com Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Crie um cofre chave com o portal Azure.](../secrets/quick-create-portal.md)
- O [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [O Azure PowerShell](/powershell/azure/overview). Em alternativa, pode utilizar o [portal Azure.](https://portal.azure.com)

## <a name="grant-access-to-your-key-vault"></a>Conceda acesso ao seu cofre chave

Cada entrada na política de acesso ao cofre chave concede um conjunto distinto de permissões a um diretor:

- **Uma aplicação** Se a aplicação for baseada em nuvem, deve [utilizar uma identidade gerida para aceder ao Cofre chave Azure,](managed-identity.md)se possível
- **Um grupo De AD Azure** Embora o cofre de chaves suporte apenas 1024 entradas de política de acesso, você pode adicionar múltiplas aplicações e utilizadores a um único grupo Azure AD, e, em seguida, adicionar esse grupo como uma única entrada na sua política de controlo de acesso.
- **Um Utilizador** Dar aos utilizadores acesso direto a um cofre chave é **desencorajado.** Idealmente, os utilizadores devem ser adicionados a um grupo Azure AD, que por sua vez é dado acesso ao cofre chave. Ver Segurança do [Cofre chave Azure: Identidade e gestão de acesso.](overview-security.md#identity-and-access-management)


### <a name="get-the-objectid"></a>Obtenha o objectID

Para dar uma aplicação, grupo Azure AD ou acesso ao utilizador ao seu cofre chave, tem primeiro de obter o seu objectid.

#### <a name="applications"></a>Aplicações

O objectid para aplicações corresponde ao seu diretor de serviço associado. Para mais detalhes sobre os diretores de serviço. ver [Aplicação e serviço principais objetos no Diretório Ativo Azure](../../active-directory/develop/app-objects-and-service-principals.md). 

Existem duas formas de obter um objectId para uma aplicação.  A primeira é registar a sua candidatura no Azure Ative Directory. Para tal, siga os passos no quickstart [Registe uma aplicação com a plataforma de identidade Microsoft](../../active-directory/develop/quickstart-register-app.md). Quando o registo estiver concluído, o objectid será listado como o "Id de aplicação (cliente) ".

A segunda é criar um diretor de serviço numa janela terminal. Com o Azure CLI, utilize o comando [az ad sp create-for-rbac,](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) e forneça um nome de princípio de serviço único à bandeira -n no formato "http://<my-unique-service-principle-principle>".

```azurecli-interactive
az ad sp create-for-rbac -n "http://<my-unique-service-principle-name"
```

O objectid será listado na `clientID`saída como .

Com a Azure PowerShell, utilize o cmdlet [New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0)


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName <my-unique-service-principle-name>
```

O objectid será listado na `Id` saída `ApplicationId`como (não ).

#### <a name="azure-ad-groups"></a>Grupos AD Azure

Pode adicionar várias aplicações e utilizadores a um grupo Azure AD e, em seguida, dar ao grupo acesso ao seu cofre chave.  Para mais detalhes, consulte a Criação e adição de membros a uma secção de [grupo Azure AD,](#creating-and-adding-members-to-an-azure-ad-group) abaixo.

Para encontrar o objectid de um grupo Azure AD com o Azure CLI, utilize o comando da [lista de grupos az ad.](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) Devido ao grande número de grupos que podem estar na sua `--display-name` organização, você também deve fornecer uma cadeia de pesquisa para o parâmetro.

```azurecli-interactive
az ad group list --display-name <search-string>
```
O objectid será devolvido no JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Para encontrar o objectid de um grupo Azure AD com a Azure PowerShell, utilize o cmdlet [Get-AzADGroup.](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) Devido ao grande número de grupos que podem estar na sua organização, `-SearchString` provavelmente também desejará fornecer uma cadeia de pesquisa ao parâmetro.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

Na saída, o objectid `Id`é listado como:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Utilizadores

Também pode adicionar um utilizador individual à política de controlo de acesso de um cofre chave. **Não recomendamos isto.** Em vez disso, encorajamo-lo a adicionar utilizadores a um grupo De AD Azure e a adicionar o grupo sobre as políticas.

Se, no entanto, desejar encontrar um utilizador com o Azure CLI, utilize o `--id` comando de show de utilizadores [az az,](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) passando o endereço de e-mail dos utilizadores para o parâmetro.


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

Para encontrar um utilizador com o Azure PowerShell, utilize o cmdlet [Get-AzADUser,](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) passando o endereço de e-mail dos utilizadores para o `-UserPrincipalName` parâmetro.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

O objectid do utilizador será devolvido `Id`na saída como .

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Dê ao principal acesso ao seu cofre chave

Agora que tem um objectid do seu diretor, pode criar uma política de acesso para o seu cofre chave que lhe dê obter, lista, definir e eliminar permissões tanto para chaves como segredos, além de quaisquer permissões adicionais que desejar.

Com o Azure CLI, isto é feito passando o objectId para o comando [de definição de teclado az.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Com o Azure PowerShell, isto é feito passando o objectid para o [cmdlet Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Criação e adição de membros a um grupo Azure AD

Pode criar um grupo Azure AD, adicionar aplicações e utilizadores ao grupo e dar ao grupo acesso ao seu cofre chave.  Isto permite-lhe adicionar uma série de aplicações a um cofre chave como uma única entrada de política de acesso, e elimina a necessidade de dar aos utilizadores acesso direto ao seu cofre chave (o que desencorajamos). Para mais detalhes, consulte Gerir app e acesso a [recursos utilizando grupos de Diretórios Ativos Do Azure](../../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Pré-requisitos adicionais

Além dos [pré-requisitos acima referidos,](#prerequisites)necessitará de permissões para criar/editar grupos no seu inquilino do Diretório Ativo Azure. Se não tiver permissões, poderá ter de contactar o administrador do Diretório Ativo Azure.

Se pretende utilizar o PowerShell, também necessitará do [módulo PowerShell Azure AD](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Criar um grupo de Diretório Ativo Azure

Crie um novo grupo azure Ative Directory utilizando o grupo de anúncios Azure CLI [az criar](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) comando, ou o Cmdlet Azure PowerShell [New-AzureADGroup.](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0)


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

Em qualquer dos casos, tome nota nos grupos recém-criados GroupId, uma vez que vai precisar dele para os passos abaixo.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Encontre os objetoIds das suas aplicações e utilizadores

Pode encontrar os objectIds das suas aplicações utilizando o Azure CLI com o `--show-mine` comando da [lista az ad,](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) com o parâmetro.

```azurecli-interactive
az ad sp list --show-mine
```

Encontre os objectIds das suas aplicações utilizando o Azure PowerShell com o [cmdlet Get-AzADServicePrincipal,](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) passando uma cadeia de pesquisa para o `-SearchString` parâmetro.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Para encontrar os objectIds dos seus Utilizadores, siga os passos na secção [Utilizadores,](#users) acima.

### <a name="add-your-applications-and-users-to-the-group"></a>Adicione as suas aplicações e utilizadores ao grupo

Agora, adicione os objectIds ao seu recém-criado grupo Azure AD.

Com o Azure CLI, utilize o [add az ad group,](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)passando o objectId para o `--member-id` parâmetro.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Com o Azure PowerShell, utilize o cmdlet [Add-AzADGroupMember,](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) passando o objectid para o `-MemberObjectId` parâmetro.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Dê ao grupo AD acesso ao seu cofre chave

Por último, dê permissões ao grupo AD para o seu cofre chave utilizando o comando de definição de definição de teclado Azure CLI [az,](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) ou o cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) Por exemplo, consulte a [aplicação Give, o grupo Azure AD ou o acesso do utilizador à sua](#give-the-principal-access-to-your-key-vault) secção de cofre chave, acima.

A aplicação também necessita de pelo menos uma função de Gestão de Identidade e Acesso (IAM) atribuída ao cofre chave. Caso contrário, não poderá entrar em sessão e falhará com direitos insuficientes de acesso à subscrição.

## <a name="next-steps"></a>Passos seguintes

- [Segurança do Cofre chave Azure: Gestão de identidade e acesso](overview-security.md#identity-and-access-management)
- [Forneça a autenticação chave vault com uma identidade gerida pelo Serviço de Aplicações](managed-identity.md)
- [Proteja o cofre da chave).](secure-your-key-vault.md)
- [Guia de desenvolvedor do Cofre de Chaves Azure](developers-guide.md)
- Rever [as melhores práticas do Cofre de Chaves Azure](best-practices.md)
