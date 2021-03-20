---
title: Acesso delegado no Windows Virtual Desktop - Azure
description: Como delegar capacidades administrativas numa implementação virtual do Windows Desktop, incluindo exemplos.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2aa2c74704cf89c082d2837b39e82902efa0a62
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88010060"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Acesso delegado ao Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).

O Windows Virtual Desktop tem um modelo de acesso delegado que permite definir a quantidade de acesso que um determinado utilizador pode ter atribuindo-lhes uma função. Uma atribuição de funções tem três componentes: principal de segurança, definição de função e âmbito. O modelo de acesso delegado do Windows Desktop baseia-se no modelo Azure RBAC. Para saber mais sobre atribuições específicas de funções e seus componentes, consulte [a visão geral do controlo de acesso baseada em funções Azure](../role-based-access-control/built-in-roles.md).

O acesso delegado ao Windows Virtual Desktop suporta os seguintes valores para cada elemento da atribuição de funções:

* Principal de segurança
    * Utilizadores
    * Grupos de utilizadores
    * Principais de serviço
* Definição de função
    * Funções incorporadas
    * Funções personalizadas
* Âmbito
    * Piscinas de acolhimento
    * Grupos de aplicações
    * Áreas de Trabalho

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets PowerShell para atribuições de funções

Antes de iniciar, certifique-se de seguir as instruções no [Conjunto do módulo PowerShell](powershell-module.md) para configurar o módulo PowerShell virtual do Windows Desktop se ainda não o fez.

O Windows Virtual Desktop utiliza o controlo de acesso baseado em funções Azure (Azure RBAC) enquanto publica grupos de aplicações para utilizadores ou grupos de utilizadores. A função de Utilizador de Virtualização desktop é atribuída ao utilizador ou grupo de utilizadores e o âmbito é o grupo de aplicações. Esta função dá ao utilizador um acesso especial aos dados no grupo de aplicações.

Executar o seguinte cmdlet para adicionar utilizadores do Azure Ative Directory a um grupo de aplicações:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Executar o seguinte cmdlet para adicionar o grupo de utilizadores do Azure Ative Directory a um grupo de aplicações:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista mais completa de cmdlets PowerShell que cada função pode utilizar, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter uma lista completa de papéis apoiados no Azure RBAC, consulte [as funções incorporadas do Azure.](../role-based-access-control/built-in-roles.md)

Para obter orientações sobre como configurar um ambiente de ambiente de trabalho virtual do Windows, consulte [o ambiente de trabalho virtual do Windows](environment-setup.md).
