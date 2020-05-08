---
title: Acesso delegado no Windows Virtual Desktop - Azure
description: Como delegar capacidades administrativas numa implementação do Windows Virtual Desktop, incluindo exemplos.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 899e42e3b592ce1bc92d7d47578e10355b1c6de7
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614437"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Acesso delegado ao Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure. Se está a tentar gerir os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows introduzidos na atualização da primavera de 2020, consulte [este artigo](../delegated-access-virtual-desktop.md).

O Windows Virtual Desktop tem um modelo de acesso delegado que permite definir a quantidade de acesso que um determinado utilizador pode ter atribuindo-lhes uma função. Uma atribuição de funções tem três componentes: diretor de segurança, definição de funções e âmbito. O modelo de acesso delegado do Windows Virtual Desktop baseia-se no modelo Azure RBAC. Para saber mais sobre atribuições específicas e seus componentes, consulte a visão geral [do controlo de acesso baseado em papéis azure.](../../role-based-access-control/built-in-roles.md)

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
* Um colaborador rds pode gerir tudo, mas não pode aceder a recursos.
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

Para obter diretrizes sobre como configurar um ambiente de ambiente de trabalho virtual windows, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup-2019.md).
