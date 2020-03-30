---
title: Criar, lista & eliminar identidade gerida atribuída pelo utilizador utilizando o Azure PowerShell - Azure AD
description: Instruções passo a passo sobre como criar, listar e eliminar identidade gerida atribuída pelo utilizador utilizando o Azure PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c512a867685b4480c7b31ac582e2cee069ee2447
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547406"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador utilizando o Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar serviços que suportam a autenticação Azure AD, sem precisar de credenciais no seu código. 

Neste artigo, aprende-se a criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.
- Se estiver a executar o PowerShell localmente, também irá precisar de: 
    - Execute `Connect-AzAccount` para criar uma ligação com o Azure.
    - Instale a [versão mais recente do PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Execute `Install-Module -Name PowerShellGet -AllowPrerelease` para obter a versão de pré-lançamento do módulo `PowerShellGet` (poderá precisar de `Exit` da sessão atual do PowerShell depois de executar este comando para instalar o módulo `Az.ManagedServiceIdentity`).
    - Execute `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` a instalação da `Az.ManagedServiceIdentity` versão pré-lançamento do módulo para realizar as operações de identidade geridas atribuídas pelo utilizador neste artigo.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Para criar uma identidade gerida atribuída ao utilizador, a sua conta necessita da atribuição de função de [Colaborador de Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Para criar uma identidade gerida atribuída ao `New-AzUserAssignedIdentity` utilizador, utilize o comando. O `ResourceGroupName` parâmetro especifica o grupo de recursos onde criar a identidade `-Name` gerida atribuída pelo utilizador, e o parâmetro especifica o seu nome. Substitua `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` os valores e parâmetros dos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuídas pelo utilizador

Para listar/ler uma identidade gerida atribuída pelo utilizador, a sua conta necessita da função de [Operador de Identidade Gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou de Contribuição de Identidade [Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Para listar as identidades geridas atribuídas pelo utilizador, utilize o comando [Get-AzUserAssigned].  O `-ResourceGroupName` parâmetro especifica o grupo de recursos onde foi criada a identidade gerida atribuída pelo utilizador. Substitua `<RESOURCE GROUP>` o com o seu próprio valor:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Na resposta, as identidades geridas `"Microsoft.ManagedIdentity/userAssignedIdentities"` atribuídas pelo `Type`utilizador têm valor devolvido para a chave, .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuída pelo utilizador

Para eliminar uma identidade gerida atribuída pelo utilizador, a sua conta necessita da atribuição de função de [Colaborador de Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Para eliminar uma identidade gerida atribuída ao `Remove-AzUserAssignedIdentity` utilizador, utilize o comando.  O `-ResourceGroupName` parâmetro especifica o grupo de recursos onde foi criada `-Name` a identidade atribuída ao utilizador e o parâmetro especifica o seu nome. Substitua `<RESOURCE GROUP>` os `<USER ASSIGNED IDENTITY NAME>` valores e os parâmetros com os seus próprios valores:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> A eliminação de uma identidade gerida atribuída ao utilizador não removerá a referência, de qualquer recurso a que tenha sido atribuído. As atribuições de identidade têm de ser removidas separadamente.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista completa e mais detalhes sobre as identidades geridas pelo Azure PowerShell para comandos de recursos Azure, consulte [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
