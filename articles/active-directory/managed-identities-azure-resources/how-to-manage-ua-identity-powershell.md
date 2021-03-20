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
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 636f40f0c425c25dfe7f41f1f404afc90ed5ba56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548227"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador utilizando a Azure PowerShell

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida no Azure Ative Directory. Pode utilizar esta identidade para autenticar serviços que suportem a autenticação Azure AD, sem precisar de credenciais no seu código. 

Neste artigo, aprende-se a criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Execute scripts localmente com Azure PowerShell, conforme descrito na secção seguinte.

### <a name="configure-azure-powershell-locally"></a>Configure Azure PowerShell localmente

Para utilizar a Azure PowerShell localmente para este artigo (em vez de utilizar a Cloud Shell), complete os seguintes passos:

1. Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

1. Inscreva-se em Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Instale a [versão mais recente do PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Poderá ter `Exit` de sair da sessão atual do PowerShell depois de executar este comando para o próximo passo.

1. Instale a versão `Az.ManagedServiceIdentity` pré-lançamento do módulo para executar as operações de identidade geridas atribuídas pelo utilizador neste artigo:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

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
