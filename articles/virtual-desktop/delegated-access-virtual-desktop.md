---
title: Acesso delegado no Windows Virtual Desktop - Azure
description: Como delegar capacidades administrativas numa implementação virtual do Windows Desktop, incluindo exemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 16b4fca475f91a8cb5b7f9a20ea5aa74b6b674a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612865"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Acesso delegado ao Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização primavera 2020 com objetos de desktop virtual do Windows Manager do Azure. Se estiver a utilizar o desbloqueio virtual do Windows Desktop Fall 2019 sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).
>
> A atualização Virtual Desktop Spring 2020 do Windows está atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

O Windows Virtual Desktop utiliza o controlo de acesso baseado em funções (RBAC) ao publicar grupos de aplicações para utilizadores ou grupos de utilizadores. A função de Utilizador de Virtualização desktop é atribuída ao utilizador ou grupo de utilizadores e o âmbito é o grupo de aplicações. Esta função dá ao utilizador um acesso especial aos dados no grupo de aplicações.  

Executar o seguinte cmdlet para adicionar utilizadores do Azure Ative Directory a um grupo de aplicações:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'  
```

Executar o seguinte cmdlet para adicionar o grupo de utilizadores do Azure Ative Directory a um grupo de aplicações:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista mais completa de cmdlets PowerShell que cada função pode utilizar, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter uma lista completa de papéis apoiados no Azure RBAC, consulte [as funções incorporadas do Azure.](../role-based-access-control/built-in-roles.md)

Para obter orientações sobre como configurar um ambiente de ambiente de trabalho virtual do Windows, consulte [o ambiente de trabalho virtual do Windows](environment-setup.md).
