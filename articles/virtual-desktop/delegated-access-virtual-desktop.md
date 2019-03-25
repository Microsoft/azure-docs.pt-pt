---
title: Acesso delegado na pré-visualização de ambiente de trabalho virtuais Windows - Azure
description: Como delegar capacidades administrativas numa implementação de pré-visualização de ambiente de Trabalho Virtual do Windows, incluindo exemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 250aea52de63a6397ce00e9cadcadf3a8ba39858
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58399984"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Acesso delegado na pré-visualização de ambiente de Trabalho Virtual do Windows

Visualização de ambiente de Trabalho Virtual do Windows tem um modelo de acesso delegado que permite que defina a quantidade de acesso que um utilizador específico pode ter ao atribuir-lhes uma função. Uma atribuição de função tem três componentes: entidade de segurança, a definição de função e o âmbito. O modelo de acesso delegado de área de Trabalho Virtual do Windows é baseado no modelo de RBAC do Azure. Para obter mais informações sobre atribuições de função específica e os respetivos componentes, consulte [a visão geral de controlo de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

Área de Trabalho Virtual do Windows delegado acesso suporta os seguintes valores para cada elemento da atribuição de função:

* Principal de segurança
    * Utilizadores
    * Principais de serviço
* Definição de função
    * Funções incorporadas
* Âmbito
    * Grupos de inquilino
    * Inquilinos
    * Conjuntos de anfitrião
    * Grupos de aplicações

## <a name="built-in-roles"></a>Funções incorporadas

Acesso delegado na área de Trabalho Virtual do Windows tem várias definições de função incorporada que pode atribuir aos utilizadores e os principais de serviço.

* Um proprietário de RDS pode gerir tudo, incluindo o acesso aos recursos.
* Um contribuinte de RDS pode gerir tudo, mas o acesso aos recursos.
* Um leitor de RDS podem ver tudo, mas não é possível fazer alterações.
* Um operador de RDS pode ver as atividades de diagnóstico.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets do PowerShell para atribuições de funções

Pode executar os seguintes cmdlets para criar, ver e editar atribuições de funções:

* **Get-RdsRoleAssignment** apresenta uma lista de atribuições de funções.
* **Novo RdsRoleAssignment** cria uma nova atribuição de função.
* **Conjunto RdsRoleAssignment** edita atribuições de funções.

### <a name="accepted-parameters"></a>Parâmetros aceites

Pode modificar os três cmdlets básicos com os seguintes parâmetros:

* **AadTenantId**: Especifica o ID de inquilino do Azure Active Directory do qual o principal de serviço é um membro.
* **AppGroupName**: nome do grupo de aplicações de ambiente de trabalho remoto.
* **Diagnóstico**: indica o âmbito de diagnóstico. (Deve ser emparelhado com ambos os **infraestrutura** ou **inquilino** parâmetros.)
* **HostPoolName**: nome do conjunto de anfitrião de ambiente de trabalho remoto.
* **Infraestrutura**: indica o âmbito de infraestrutura.
* **RoleDefinitionName**: nome da função de controlo de acesso baseado em função dos serviços de ambiente de trabalho remoto atribuída para o utilizador, grupo ou aplicação. (Por exemplo, proprietário de serviços de ambiente de trabalho remoto, leitor de serviços de ambiente de trabalho remoto e assim por diante.)
* **ServerPrincipleName**: nome da aplicação do Azure Active Directory.
* **SignInName**: endereço de e-mail do utilizador ou nome principal de utilizador.
* **TenantName**: nome do inquilino de ambiente de trabalho remoto.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista mais completa de cada função pode utilizar cmdlets do PowerShell, consulte a [referência do PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter diretrizes sobre como configurar um ambiente de área de Trabalho Virtual do Windows, consulte [ambiente do Windows Virtual Desktop Preview](environment-setup.md).
