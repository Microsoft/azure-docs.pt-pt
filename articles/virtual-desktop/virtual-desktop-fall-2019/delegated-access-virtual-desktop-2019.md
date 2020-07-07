---
title: Acesso delegado no Windows Virtual Desktop - Azure
description: Como delegar capacidades administrativas numa implementação virtual do Windows Desktop, incluindo exemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 899e42e3b592ce1bc92d7d47578e10355b1c6de7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82614437"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Acesso delegado ao Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure. Se está a tentar gerir os objetos virtuais do Azure Resource Manager Windows, introduzidos na atualização da primavera de 2020, consulte [este artigo](../delegated-access-virtual-desktop.md).

O Windows Virtual Desktop tem um modelo de acesso delegado que permite definir a quantidade de acesso que um determinado utilizador pode ter atribuindo-lhes uma função. Uma atribuição de funções tem três componentes: principal de segurança, definição de função e âmbito. O modelo de acesso delegado do Windows Desktop baseia-se no modelo Azure RBAC. Para saber mais sobre atribuições específicas de funções e seus componentes, consulte [a visão geral do controlo de acesso baseada em funções Azure](../../role-based-access-control/built-in-roles.md).

O acesso delegado ao Windows Virtual Desktop suporta os seguintes valores para cada elemento da atribuição de funções:

* Principal de segurança
    * Utilizadores
    * Principais de serviço
* Definição de função
    * Funções incorporadas
* Âmbito
    * Grupos de inquilinos
    * Inquilinos
    * Piscinas de acolhimento
    * Grupos de aplicações

## <a name="built-in-roles"></a>Funções incorporadas

O acesso delegado no Windows Virtual Desktop tem várias definições de funções incorporadas que pode atribuir aos utilizadores e aos principais de serviço.

* Um Proprietário RDS pode gerir tudo, incluindo acesso a recursos.
* Um contribuinte RDS pode gerir tudo, mas não consegue aceder aos recursos.
* Um leitor de RDS pode ver tudo, mas não pode fazer alterações.
* Um operador RDS pode visualizar as atividades de diagnóstico.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets PowerShell para atribuições de funções

Pode executar os seguintes cmdlets para criar, visualizar e remover atribuições de funções:

* **A Get-RdsRoleAssignment** apresenta uma lista de atribuições de funções.
* **New-RdsRoleAssignment** cria uma nova atribuição de papel.
* **Remove-RdsRoleAssignment** elimina atribuições de funções.

### <a name="accepted-parameters"></a>Parâmetros aceites

Pode modificar os três cmdlets básicos com os seguintes parâmetros:

* **AadTenantId**: especifica o ID do inquilino do Azure Ative Directory a partir do qual o diretor de serviço é membro.
* **AppGroupName**: nome do grupo de aplicações Remote Desktop.
* **Diagnósticos**: indica o âmbito de diagnóstico. (Deve ser emparelhado com os parâmetros **infraestrutura** ou **inquilino.)**
* **HostPoolName**: nome da piscina de anfitriões remote desktop.
* **Infraestrutura**: indica o âmbito da infraestrutura.
* **FunDefinitionName**: nome da função de controlo de acesso baseado em funções dos Serviços de Ambiente de Trabalho remoto atribuída ao utilizador, grupo ou app. (Por exemplo, Proprietário de Serviços de Desktop Remoto, Leitor de Serviços de Secretária Remoto, e assim por diante.)
* **ServerPrincipleName**: nome da aplicação Azure Ative Directory.
* **SignInName**: o endereço de e-mail do utilizador ou o nome principal do utilizador.
* **Nome do Inquilino**: nome do inquilino de ambiente de trabalho remoto.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista mais completa de cmdlets PowerShell que cada função pode utilizar, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter orientações sobre como configurar um ambiente de ambiente de trabalho virtual do Windows, consulte [o ambiente de trabalho virtual do Windows](environment-setup-2019.md).
