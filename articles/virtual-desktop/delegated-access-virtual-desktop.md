---
title: Acesso delegado no Windows Virtual Desktop - Azure
description: Como delegar capacidades administrativas numa implementação do Windows Virtual Desktop, incluindo exemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 16b4fca475f91a8cb5b7f9a20ea5aa74b6b674a3
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612865"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Acesso delegado ao Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O Windows Virtual Desktop tem um modelo de acesso delegado que permite definir a quantidade de acesso que um determinado utilizador pode ter atribuindo-lhes uma função. Uma atribuição de funções tem três componentes: diretor de segurança, definição de funções e âmbito. O modelo de acesso delegado do Windows Virtual Desktop baseia-se no modelo Azure RBAC. Para saber mais sobre atribuições específicas e seus componentes, consulte a visão geral [do controlo de acesso baseado em papéis azure.](../role-based-access-control/built-in-roles.md)

O acesso delegado do Windows Virtual Desktop suporta os seguintes valores para cada elemento da atribuição de funções:

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

## <a name="powershell-cmdlets-for-role-assignments"></a>PowerShell cmdlets para atribuições de papéis

Antes de começar, certifique-se de seguir as instruções no [conjunto do módulo PowerShell](powershell-module.md) para configurar o módulo PowerShell virtual do Windows se ainda não o fez.

O Windows Virtual Desktop utiliza o controlo de acesso baseado em funções do Azure (RBAC) enquanto publica grupos de aplicações para utilizadores ou grupos de utilizadores. A função de Utilizador de Virtualização do Ambiente de Trabalho é atribuída ao utilizador ou grupo de utilizadores e o âmbito é o grupo de aplicações. Esta função dá ao utilizador acesso especial de dados no grupo de aplicações.  

Executar o seguinte cmdlet para adicionar utilizadores de Diretório Ativo Azure a um grupo de aplicações:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'  
```

Execute o seguinte cmdlet para adicionar o grupo de utilizadores Azure Ative Directory a um grupo de aplicações:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista mais completa de cmdlets PowerShell cada função pode ser utilizada, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter uma lista completa de funções suportadas no Azure RBAC, consulte [as funções azure incorporadas.](../role-based-access-control/built-in-roles.md)

Para obter diretrizes sobre como configurar um ambiente de ambiente de trabalho virtual windows, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup.md).
