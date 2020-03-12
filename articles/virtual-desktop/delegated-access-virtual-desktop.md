---
title: Acesso delegado no Windows Virtual Desktop - Azure
description: Como delegar capacidades administrativas numa implementação do Windows Virtual Desktop, incluindo exemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3e27550ecc5b42c2bf0d947690da09e13d88ea4f
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128032"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Acesso delegado ao Windows Virtual Desktop

O Windows Virtual Desktop tem um modelo de acesso delegado que permite definir a quantidade de acesso que um determinado utilizador pode ter atribuindo-lhes uma função. Uma atribuição de funções tem três componentes: diretor de segurança, definição de funções e âmbito. O modelo de acesso delegado do Windows Virtual Desktop baseia-se no modelo Azure RBAC. Para saber mais sobre atribuições específicas e seus componentes, consulte a visão geral [do controlo de acesso baseado em papéis azure.](../role-based-access-control/built-in-roles.md)

O acesso delegado do Windows Virtual Desktop suporta os seguintes valores para cada elemento da atribuição de funções:

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

O acesso delegado no Windows Virtual Desktop tem várias definições de funções incorporadas que pode atribuir aos utilizadores e aos diretores de serviço.

* Um Proprietário RDS pode gerir tudo, incluindo o acesso aos recursos.
* Um Colaborador RDS pode gerir tudo menos o acesso aos recursos.
* Um leitor rds pode ver tudo, mas não pode fazer alterações.
* Um operador RDS pode ver atividades de diagnóstico.

## <a name="powershell-cmdlets-for-role-assignments"></a>PowerShell cmdlets para atribuições de papéis

Pode executar os seguintes cmdlets para criar, visualizar e remover atribuições de funções:

* **Get-RdsRoleAssignment** apresenta uma lista de atribuições de papéis.
* **A New-RdsRoleAssignment** cria uma nova atribuição de papéis.
* **Remover-RdsRoleAssignment** elimina atribuições de funções.

### <a name="accepted-parameters"></a>Parâmetros aceites

Pode modificar os três cmdlets básicos com os seguintes parâmetros:

* **AadTenantId**: especifica o ID do inquilino do Diretório Ativo Azure a partir do qual o diretor de serviço é membro.
* **Nome do AppGroup**: nome do grupo de aplicações Remote Desktop.
* **Diagnósticos:** indica o âmbito de diagnóstico. (Deve ser emparelhado com os parâmetros **infraestrutura** ou **inquilino.)**
* **HostPoolName**: nome da piscina de anfitriões do Ambiente de Trabalho Remoto.
* **Infraestrutura**: indica o âmbito da infraestrutura.
* **RoleDefinitionName**: nome da função de controlo de acesso baseado em funções de Serviços de Secretária Remoto atribuído ao utilizador, grupo ou aplicação. (Por exemplo, Proprietário de Serviços de Ambiente de Trabalho Remoto, Leitor de Serviços de Ambiente de Trabalho Remoto, e assim por diante.)
* **Nome do ServidorPrincipleName**: nome da aplicação Azure Ative Directory.
* **SignInName**: o endereço de e-mail do utilizador ou o nome principal do utilizador.
* **Nome do inquilino**: nome do inquilino do Ambiente de Trabalho Remoto.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista mais completa de cmdlets PowerShell cada função pode ser utilizada, consulte a [referência PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter diretrizes sobre como configurar um ambiente de ambiente de trabalho virtual windows, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup.md).
