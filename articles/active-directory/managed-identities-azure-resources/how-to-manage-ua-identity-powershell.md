---
title: Criar, listar & Excluir identidade gerenciada atribuída pelo usuário usando Azure PowerShell-Azure AD
description: Instruções passo a passo sobre como criar, listar e excluir a identidade gerenciada atribuída pelo usuário usando o Azure PowerShell.
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
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547406"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário usando Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em serviços que dão suporte à autenticação do Azure AD, sem precisar de credenciais em seu código. 

Neste artigo, você aprenderá a criar, listar e excluir uma identidade gerenciada atribuída pelo usuário usando Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md). **Certifique-se de examinar a [diferença entre uma identidade gerenciada atribuída pelo sistema e](overview.md#how-does-the-managed-identities-for-azure-resources-work)** atribuída pelo usuário.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não tiver feito isso.
- Se estiver a executar o PowerShell localmente, também irá precisar de: 
    - Execute `Connect-AzAccount` para criar uma ligação com o Azure.
    - Instale a [versão mais recente do PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Execute `Install-Module -Name PowerShellGet -AllowPrerelease` para obter a versão de pré-lançamento do módulo `PowerShellGet` (poderá precisar de `Exit` da sessão atual do PowerShell depois de executar este comando para instalar o módulo `Az.ManagedServiceIdentity`).
    - Execute `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` para instalar a versão de pré-lançamento do módulo `Az.ManagedServiceIdentity` para executar as operações de identidade gerenciadas atribuídas pelo usuário neste artigo.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Para criar uma identidade gerenciada atribuída pelo usuário, use o comando `New-AzUserAssignedIdentity`. O parâmetro `ResourceGroupName` especifica o grupo de recursos onde criar a identidade gerenciada atribuída pelo usuário e o parâmetro `-Name` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas pelo usuário

Para listar/ler uma identidade gerenciada atribuída pelo usuário, sua conta precisa do [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Para listar identidades gerenciadas atribuídas pelo usuário, use o comando [Get-AzUserAssigned].  O parâmetro `-ResourceGroupName` especifica o grupo de recursos em que a identidade gerenciada atribuída pelo usuário foi criada. Substitua o `<RESOURCE GROUP>` pelo seu próprio valor:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Na resposta, as identidades gerenciadas atribuídas pelo usuário têm `"Microsoft.ManagedIdentity/userAssignedIdentities"` valor retornado para a chave, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída pelo usuário

Para excluir uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Para excluir uma identidade gerenciada atribuída pelo usuário, use o comando `Remove-AzUserAssignedIdentity`.  O parâmetro `-ResourceGroupName` especifica o grupo de recursos em que a identidade atribuída pelo usuário foi criada e o parâmetro `-Name` especifica seu nome. Substitua os valores de parâmetros `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> A exclusão de uma identidade gerenciada atribuída pelo usuário não removerá a referência, de qualquer recurso ao qual ela foi atribuída. As atribuições de identidade precisam ser removidas separadamente.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista completa e mais detalhes sobre as Azure PowerShell identidades gerenciadas para comandos de recursos do Azure, consulte [AZ. ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
