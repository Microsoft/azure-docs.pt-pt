---
title: Acesso delegado na área de trabalho virtual do Windows – Azure
description: Como delegar recursos administrativos em uma implantação de área de trabalho virtual do Windows, incluindo exemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ad8dc610d15742844b8bec630d7bc3e08fb46973
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606889"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Acesso delegado ao Windows Virtual Desktop

A área de trabalho virtual do Windows tem um modelo de acesso delegado que permite definir a quantidade de acesso que um usuário específico tem permissão para ter atribuindo a eles uma função. Uma atribuição de função tem três componentes: entidade de segurança, definição de função e escopo. O modelo de acesso delegado da área de trabalho virtual do Windows baseia-se no modelo RBAC do Azure. Para saber mais sobre atribuições de função específicas e seus componentes, consulte [a visão geral do controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

O acesso delegado da área de trabalho virtual do Windows oferece suporte aos seguintes valores para cada elemento da atribuição de função:

* Principal de segurança
    * Utilizadores
    * Entidades de serviço
* Definição de função
    * Funções incorporadas
* Âmbito
    * Grupos de locatários
    * Locatários
    * Pools de hosts
    * Grupos de aplicativos

## <a name="built-in-roles"></a>Funções incorporadas

O acesso delegado na área de trabalho virtual do Windows tem várias definições de função internas que você pode atribuir a usuários e entidades de serviço.

* Um proprietário de RDS pode gerenciar tudo, incluindo o acesso aos recursos.
* Um colaborador de RDS pode gerenciar tudo, exceto o acesso aos recursos.
* Um leitor de RDS pode exibir tudo, mas não pode fazer nenhuma alteração.
* Um operador RDS pode exibir atividades de diagnóstico.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets do PowerShell para atribuições de função

Você pode executar os seguintes cmdlets para criar, exibir e remover atribuições de função:

* **Get-RdsRoleAssignment** exibe uma lista de atribuições de função.
* **New-RdsRoleAssignment** cria uma nova atribuição de função.
* **Remove-RdsRoleAssignment** exclui atribuições de função.

### <a name="accepted-parameters"></a>Parâmetros aceitos

Você pode modificar os três cmdlets básicos com os seguintes parâmetros:

* **AadTenantId**: especifica a ID de locatário Azure Active Directory da qual a entidade de serviço é um membro.
* **AppGroupName**: nome do grupo de aplicativos área de trabalho remota.
* **Diagnóstico**: indica o escopo do diagnóstico. (Deve ser emparelhado com os parâmetros de **infraestrutura** ou de **locatário** .)
* **HostPoolName**: nome do pool de hosts área de trabalho remota.
* **Infraestrutura**: indica o escopo da infraestrutura.
* **RoleDefinitionName**: o nome do serviços de área de trabalho remota função de controle de acesso baseado em função atribuída ao usuário, ao grupo ou ao aplicativo. (Por exemplo, Serviços de Área de Trabalho Remota proprietário, leitor de Serviços de Área de Trabalho Remota e assim por diante.)
* **ServerPrincipleName**: nome do aplicativo Azure Active Directory.
* **SignInName**: o endereço de email do usuário ou o nome principal do usuário.
* **Tenantname**: nome do locatário de área de trabalho remota.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista mais completa de cmdlets do PowerShell que cada função pode usar, consulte a [referência do PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter diretrizes sobre como configurar um ambiente de área de trabalho virtual do Windows, consulte [ambiente de área de trabalho virtual do Windows](environment-setup.md).
