---
title: Como configurar a política de risco do usuário no Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como configurar a política de risco de usuário do Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7ea05497d69a7ca833cc783e7a2bc6bf1a8b07
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335450"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Como: Configurar a política de utilizador de risco

Com o risco do usuário, o Azure AD detecta a probabilidade de que uma conta de usuário tenha sido comprometida. Como administrador, você pode configurar uma política de acesso condicional de risco do usuário para responder automaticamente a um nível de risco do usuário específico.
 
Este artigo fornece as informações necessárias para configurar uma política de risco do usuário.

## <a name="what-is-a-user-risk-policy"></a>O que é uma política de risco de usuário?

O Azure AD analisa cada entrada de um usuário. O objetivo da análise é detectar ações suspeitas que venham junto com a entrada. No Azure AD, as ações suspeitas que o sistema pode detectar também são conhecidas como eventos de risco. Embora alguns eventos de risco possam ser detectados em tempo real, também há eventos de risco que exigem mais tempo. Por exemplo, para detectar uma viagem impossível para locais atípicoss, o sistema requer um período de aprendizado inicial de 14 dias para aprender sobre o comportamento regular de um usuário. Há várias opções para resolver os eventos de risco detectados. Por exemplo, você pode resolver manualmente os eventos de risco individuais ou pode obtê-los resolvidos usando um risco de entrada ou uma política de acesso condicional de risco do usuário.

Todos os eventos de risco que foram detectados para um usuário e que não foram resolvidos são conhecidos como eventos de risco ativos. Os eventos de risco ativos associados a um usuário são conhecidos como risco do usuário. Com base no risco do usuário, o Azure AD calcula uma probabilidade (baixa, média, alta) de que um usuário foi comprometido. A probabilidade é chamada de nível de risco do usuário.

![Riscos do usuário](./media/howto-user-risk-policy/1031.png)

A política de risco do usuário é uma resposta automatizada que você pode configurar para um nível de risco de usuário específico. Com uma política de risco do usuário, você pode bloquear o acesso aos seus recursos ou exigir uma alteração de senha para que uma conta de usuário volte a um estado limpo.

## <a name="how-do-i-access-the-user-risk-policy"></a>Como fazer acessar a política de risco do usuário?
   
A política de risco de entrada está na seção **Configurar** na [página Azure ad Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de risco de utilizador](./media/howto-user-risk-policy/1014.png)

## <a name="policy-settings"></a>Definições de política

Ao configurar a política de risco de entrada, você precisa definir:

- Os usuários e grupos aos quais a política se aplica:

    ![Utilizadores e grupos](./media/howto-user-risk-policy/11.png)

- O nível de risco de entrada que dispara a política:

    ![O utilizador tem um nível de risco elevado](./media/howto-user-risk-policy/12.png)

- O tipo de acesso que você deseja impor quando o nível de risco de entrada tiver sido atendido:  

    ![Aceder](./media/howto-user-risk-policy/13.png)

- O estado da sua política:

    ![Impor política](./media/howto-user-risk-policy/14.png)

A caixa de diálogo configuração de política fornece uma opção para estimar o impacto da sua configuração.

![Impacto estimado](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>O que deve saber

Você pode definir uma política de segurança de risco do usuário para bloquear os usuários após a entrada, dependendo do nível de risco.

![Bloqueio](./media/howto-user-risk-policy/16.png)

Bloqueando uma entrada:

* Impede a geração de novos eventos de risco do usuário para o usuário afetado
* Permite aos administradores corrigir manualmente os eventos de risco que afetam a identidade do usuário e restaurá-los para um estado seguro

## <a name="best-practices"></a>Melhores práticas

Escolher um limite **alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários.
No entanto, ele exclui usuários **baixos** e **médios** sinalizados para risco da política, o que pode não proteger identidades ou dispositivos que foram suspeitos ou comprometidos anteriormente.

Ao definir a política,

* Exclua os usuários que provavelmente gerarão muitos falsos positivos (desenvolvedores, analistas de segurança)
* Excluir usuários em localidades em que a habilitação da política não é prática (por exemplo, sem acesso ao helpdesk)
* Use um limite **alto** durante a distribuição da política inicial ou se você precisar minimizar os desafios vistos pelos usuários finais.
* Use um limite **baixo** se sua organização exigir maior segurança. A seleção de um limite **baixo** apresenta desafios de entrada de usuário adicionais, mas aumentou a segurança.

O padrão recomendado para a maioria das organizações é configurar uma regra para um limite **médio** para um equilíbrio entre a usabilidade e a segurança.

Para obter uma visão geral da experiência do usuário relacionada, consulte:

* [Fluxo de recuperação da conta comprometida](flows.md#compromised-account-recovery).  
* [Fluxo bloqueado da conta comprometida](flows.md#compromised-account-blocked).  

**Para abrir a caixa de diálogo de configuração relacionada**:

- Na folha **Azure ad Identity Protection** , na seção **Configurar** , clique em **política de risco do usuário**.

    ![Política de risco do usuário](./media/howto-user-risk-policy/1009.png "Política de risco do usuário")

## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [visão geral Azure ad Identity Protection](overview.md).
