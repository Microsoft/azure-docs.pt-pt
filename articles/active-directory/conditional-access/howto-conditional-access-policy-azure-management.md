---
title: Acesso Condicional - Exigir MFA para gestão Azure - Diretório Ativo Azure
description: Criar uma política personalizada de acesso condicional para exigir a autenticação de vários fatores para tarefas de gestão do Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d07717895c6faa506d692becb261e834e44890
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995365"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Acesso Condicional: Exigir MFA para gestão do Azure

As organizações utilizam uma variedade de serviços Azure e gerem-nos a partir de ferramentas baseadas em Gestor de Recursos Azure, como:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Estas ferramentas podem fornecer acesso altamente privilegiado a recursos, que podem alterar configurações de subscrição, configurações de serviço e faturação de subscrição. Para proteger estes recursos privilegiados, a Microsoft recomenda a necessidade de autenticação multifactor para qualquer utilizador que aceda a esses recursos.

## <a name="user-exclusions"></a>Exclusões de utilizadores

As políticas de Acesso Condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou contas **de vidro para** evitar o bloqueio da conta em todo o inquilino. No cenário improvável, todos os administradores estão bloqueados fora do seu inquilino, a sua conta administrativa de acesso de emergência pode ser usada para iniciar sessão no inquilino, tomando medidas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, Gerir contas de [acesso de emergência em Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas** de serviço e diretores de **serviço,** como a Conta De Sincronização De Ligação AD Azure. As contas de serviço são contas não interativas que não estão ligadas a nenhum utilizador em particular. São normalmente utilizados por serviços de back-end que permitem o acesso programático a aplicações, mas também são usados para iniciar sessão em sistemas para fins administrativos. Contas de serviço como estas devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente. As chamadas feitas pelos diretores de serviço não são bloqueadas pelo Acesso Condicional.
   * Se a sua organização tiver estas contas em uso em scripts ou código, considere substituí-las por [identidades geridas](../managed-identities-azure-resources/overview.md). Como uma sutição temporária, pode excluir estas contas específicas da política de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

Os seguintes passos ajudarão a criar uma política de Acesso Condicional para exigir que aqueles com acesso à aplicação [Microsoft Azure Management](concept-conditional-access-cloud-apps.md#microsoft-azure-management) realizem a autenticação de vários fatores.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até ao Acesso Condicional de Segurança **do Diretório Ativo do Azure.**  >  **Security**  >  **Conditional Access**
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir,** selecione **Todos os utilizadores**.
   1. Em **Excluir,** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou de vidro de emergência da sua organização. 
   1. Selecione **Done** (Concluído).
1. Em **aplicações ou ações cloud**  >  **Inclua**, **selecione aplicações, escolha**a Microsoft **Azure Management**, e selecione **Select** then **Done**.
1. Em **condições,**  >  **as aplicações do Cliente (Pré-visualização)**, definir **Configurar** para **Sim,** e selecionar **Feito**.
1. Sob **controlos de acesso**  >  **Grant**, selecione Acesso ao **Grant,** **exija a autenticação de vários fatores,** e selecione **Select**.
1. Confirme as suas definições e ajuste **a política de ativação** para **On**.
1. Selecione **Criar** para criar para ativar a sua política.

## <a name="next-steps"></a>Próximos passos

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determine o impacto utilizando o modo apenas de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)
