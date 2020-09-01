---
title: Crie, liste & eliminar identidade gerida atribuída ao utilizador usando Azure PowerShell - Azure AD
description: Instruções passo a passo sobre como criar, listar e eliminar identidade gerida atribuída pelo utilizador utilizando a Azure PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1211245786bbb734e0338be1b79030f5f9552793
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266379"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador utilizando a Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida no Azure Ative Directory. Pode utilizar esta identidade para autenticar serviços que suportem a autenticação Azure AD, sem precisar de credenciais no seu código. 

Neste artigo, aprende-se a criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.
- Se estiver a executar o PowerShell localmente, também irá precisar de: 
    - Execute `Connect-AzAccount` para criar uma ligação com o Azure.
    - Instale a [versão mais recente do PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Execute `Install-Module -Name PowerShellGet -AllowPrerelease` para obter a versão de pré-lançamento do módulo `PowerShellGet` (poderá precisar de `Exit` da sessão atual do PowerShell depois de executar este comando para instalar o módulo `Az.ManagedServiceIdentity`).
    - Corra `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` para instalar a versão pré-lançamento do módulo para executar as `Az.ManagedServiceIdentity` operações de identidade geridas pelo utilizador neste artigo.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Para criar uma identidade gerida atribuída ao utilizador, a sua conta necessita da atribuição [de função de Contribuinte de Identidade Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

Para criar uma identidade gerida atribuída ao utilizador, utilize o `New-AzUserAssignedIdentity` comando. O `ResourceGroupName` parâmetro especifica o grupo de recursos onde criar a identidade gerida atribuída pelo utilizador, e o parâmetro especifica o `-Name` seu nome. Substitua os `<RESOURCE GROUP>` valores e `<USER ASSIGNED IDENTITY NAME>` parâmetros pelos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuídas pelo utilizador

Para listar/ler uma identidade gerida atribuída pelo utilizador, a sua conta precisa da atribuição de funções de [Colaborador de Identidade Gerida](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou De Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

Para listar identidades geridas atribuídas pelo utilizador, utilize o comando [Get-AzUserAssigned].  O `-ResourceGroupName` parâmetro especifica o grupo de recursos onde foi criada a identidade gerida atribuída pelo utilizador. Substitua-a `<RESOURCE GROUP>` pelo seu próprio valor:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Na resposta, as identidades geridas atribuídas pelo utilizador têm `"Microsoft.ManagedIdentity/userAssignedIdentities"` valor devolvido para chave, `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuída pelo utilizador

Para eliminar uma identidade gerida atribuída pelo utilizador, a sua conta necessita da atribuição [de função de Contribuinte de Identidade Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

Para eliminar uma identidade gerida atribuída pelo utilizador, utilize o `Remove-AzUserAssignedIdentity` comando.  O `-ResourceGroupName` parâmetro especifica o grupo de recursos onde foi criada a identidade atribuída ao utilizador e o parâmetro especifica o `-Name` seu nome. Substitua os `<RESOURCE GROUP>` valores e os `<USER ASSIGNED IDENTITY NAME>` parâmetros pelos seus próprios valores:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> A eliminação de uma identidade gerida atribuída pelo utilizador não removerá a referência de qualquer recurso a que tenha sido atribuído. As atribuições de identidade têm de ser removidas separadamente.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista completa e mais detalhes sobre as identidades geridas pela Azure PowerShell para comandos de recursos Azure, consulte [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).