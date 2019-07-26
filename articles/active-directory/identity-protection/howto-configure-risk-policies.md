---
title: Como configurar políticas de risco no Azure Active Directory a proteção de identidade (atualizada) | Microsoft Docs
description: Como configurar políticas de risco no Azure Active Directory a proteção de identidade (atualizada).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce4e2958978de9339f4340755e3740730025a5f
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334022"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Como: Configurar políticas de risco no Azure Active Directory a proteção de identidade (atualizada)

O Azure AD detecta eventos de risco que são indicadores para identidades potencialmente comprometidas. Ao configurar as políticas de risco, você pode definir respostas automatizadas para os resultados da detecção:

- Com a política de risco de entrada, você pode configurar uma resposta a eventos de risco em tempo real que foram detectados durante a entrada de um usuário. 
- Com a política de risco do usuário, você pode configurar uma resposta a todos os riscos de usuário ativos que foram detectados para um usuário ao longo do tempo.  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="what-is-the-sign-in-risk-policy"></a>O que é a política de risco de entrada?

O Azure AD analisa cada entrada de um usuário. O objetivo da análise é detectar ações suspeitas que venham junto com a entrada. Por exemplo, a entrada é feita usando um endereço IP anônimo ou a entrada foi iniciada a partir de um local desconhecido? No Azure AD, as ações suspeitas que o sistema pode detectar também são conhecidas como eventos de risco. Com base nos eventos de risco que foram detectados durante uma entrada, o Azure AD calcula um valor. O valor representa a probabilidade (baixa, média, alta) que a entrada não é executada pelo usuário legítimo. A probabilidade é chamada de **nível de risco de entrada**.

A política de risco de entrada é uma resposta automatizada que você pode configurar para um nível de risco de entrada específico. Em sua resposta, você pode bloquear o acesso aos seus recursos ou exigir a passagem de um desafio de MFA (autenticação multifator) para obter acesso.

Quando um usuário conclui com êxito um prompt do MFA disparado pela política de risco de entrada, ele fornece comentários para a proteção de identidade que a entrada originou do usuário legítimo. Assim, o evento de risco de entrada que disparou o prompt do MFA será fechado automaticamente e a proteção de identidade impedirá que esse evento contribua para a elevação do risco do usuário. A habilitação da política de risco de entrada pode reduzir o noisiness na exibição de entradas arriscadas, permitindo que os usuários sejam corrigidos automaticamente quando solicitados a MFA e, subsequentemente, fechando a entrada arriscada associada.

## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Como fazer acessar a política de risco de entrada?
   
A política de risco de entrada está na seção **Configurar** na [página Azure ad Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de risco de entrada](./media/howto-configure-risk-policies/1014.png "Política de risco de entrada")

## <a name="sign-in-risk-policy-settings"></a>Configurações de política de risco de entrada

Ao configurar a política de risco de entrada, você precisa definir:

- Os usuários e grupos aos quais a política se aplica:

   ![Utilizadores e grupos](./media/howto-configure-risk-policies/11.png)

- O nível de risco de entrada que dispara a política:

   ![Nível de risco do início de sessão](./media/howto-configure-risk-policies/12.png)

- O tipo de acesso que você deseja impor quando o nível de risco de entrada tiver sido atendido:  

   ![Aceder](./media/howto-configure-risk-policies/13.png)

- O estado da sua política:

   ![Impor política](./media/howto-configure-risk-policies/14.png)

A caixa de diálogo configuração de política fornece uma opção para estimar o impacto da reconfiguração.

![Impacto estimado](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>O que você deve saber sobre as políticas de risco de entrada

Você pode configurar uma política de segurança de risco de entrada para exigir MFA:

![Requerer MFA](./media/howto-configure-risk-policies/16.png)

No entanto, por motivos de segurança, essa configuração só funciona para usuários que já foram registrados para MFA. A proteção de identidade bloqueia os usuários com um requisito de MFA se eles ainda não estiverem registrados para MFA.

Se você quiser exigir MFA para entradas arriscadas, deverá:

1. Habilite a política de registro da autenticação multifator para os usuários afetados.
2. Exigir que os usuários afetados façam logon em uma sessão não arriscada para executar um registro de MFA.

A conclusão dessas etapas garante que a autenticação multifator seja necessária para uma entrada arriscada.

A política de risco de entrada é:

- Aplicado a todo o tráfego e entradas do navegador usando a autenticação moderna.
- Não aplicado a aplicativos que usam protocolos de segurança mais antigos desabilitando o ponto de extremidade WS-Trust no IDP federado, como o ADFS.

Para obter uma visão geral da experiência do usuário relacionada, consulte:

* [Recuperação de entrada arriscada](flows.md#risky-sign-in-recovery)
* [Entrada arriscada bloqueada](flows.md#risky-sign-in-blocked)  
* [Experiências de entrada com Azure AD Identity Protection](flows.md)  

## <a name="what-is-a-user-risk-policy"></a>O que é uma política de risco de usuário?

O Azure AD analisa cada entrada de um usuário. O objetivo da análise é detectar ações suspeitas que venham junto com a entrada. No Azure AD, as ações suspeitas que o sistema pode detectar também são conhecidas como eventos de risco. Embora alguns eventos de risco possam ser detectados em tempo real, também há eventos de risco que exigem mais tempo. Por exemplo, para detectar uma viagem impossível para locais atípicoss, o sistema requer um período de aprendizado inicial de 14 dias para aprender sobre o comportamento regular de um usuário. Há várias opções para resolver os eventos de risco detectados. Por exemplo, você pode resolver manualmente os eventos de risco individuais ou pode obtê-los resolvidos usando um risco de entrada ou uma política de acesso condicional de risco do usuário.

Todos os eventos de risco que foram detectados para um usuário e que não foram resolvidos são conhecidos como eventos de risco ativos. Os eventos de risco ativos associados a um usuário são conhecidos como risco do usuário. Com base no risco do usuário, o Azure AD calcula uma probabilidade (baixa, média, alta) de que um usuário foi comprometido. A probabilidade é chamada de nível de risco do usuário.

![Riscos do usuário](./media/howto-configure-risk-policies/11031.png)

A política de risco do usuário é uma resposta automatizada que você pode configurar para um nível de risco de usuário específico. Com uma política de risco do usuário, você pode bloquear o acesso aos seus recursos ou exigir uma alteração de senha para que uma conta de usuário volte a um estado limpo.

## <a name="how-do-i-access-the-user-risk-policy"></a>Como fazer acessar a política de risco do usuário?
   
A política de risco do usuário está na seção **Configurar** na [página Azure ad Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de risco de utilizador](./media/howto-configure-risk-policies/11014.png)

## <a name="user-risk-policy-settings"></a>Configurações de política de risco do usuário

Ao configurar a política de risco do usuário, você precisa definir:

- Os usuários e grupos aos quais a política se aplica:

   ![Utilizadores e grupos](./media/howto-configure-risk-policies/111.png)

- O nível de risco de entrada que dispara a política:

   ![O utilizador tem um nível de risco elevado](./media/howto-configure-risk-policies/112.png)

- O tipo de acesso que você deseja impor quando o nível de risco de entrada tiver sido atendido:  

   ![Aceder](./media/howto-configure-risk-policies/113.png)

- O estado da sua política:

   ![Impor política](./media/howto-configure-risk-policies/114.png)

A caixa de diálogo configuração de política fornece uma opção para estimar o impacto da sua configuração.

![Impacto estimado](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>O que você deve saber sobre as políticas de risco do usuário

Você pode definir uma política de segurança de risco do usuário para bloquear os usuários após a entrada, dependendo do nível de risco.

![Bloqueio](./media/howto-configure-risk-policies/116.png)

Bloqueando uma entrada:

* Impede a geração de novos eventos de risco do usuário para o usuário afetado
* Permite aos administradores corrigir manualmente os eventos de risco que afetam a identidade do usuário e restaurá-los para um estado seguro

## <a name="best-practices"></a>Melhores práticas

Escolher um limite **alto** reduz o número de vezes que uma política é disparada e minimiza o impacto para os usuários.  

No entanto, ele  exclui entradas baixas e **médias** sinalizadas para risco da política, o que pode não impedir que um invasor Explore uma identidade comprometida.

Ao definir a política,

- Excluir usuários que não têm/não podem ter a autenticação multifator
- Excluir usuários em localidades em que a habilitação da política não é prática (por exemplo, sem acesso ao helpdesk)
- Excluir usuários que provavelmente gerarão muitos falsos positivos (desenvolvedores, analistas de segurança)
- Use um limite **alto** durante a distribuição de política inicial ou se você precisar minimizar os desafios vistos pelos usuários finais.
- Use um limite **baixo** se sua organização exigir maior segurança. A seleção de um limite **baixo** apresenta desafios de entrada de usuário adicionais, mas aumentou a segurança.

O padrão recomendado para a maioria das organizações é configurar uma regra para um limite **médio** para um equilíbrio entre a usabilidade e a segurança.

## <a name="next-steps"></a>Passos Seguintes

 [Canal 9: Azure AD e identidade mostram: Versão prévia da proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
