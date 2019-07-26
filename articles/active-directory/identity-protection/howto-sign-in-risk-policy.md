---
title: Como configurar a política de risco de entrada no Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como configurar a política de risco de entrada do Azure AD Identity Protection.
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
ms.openlocfilehash: 0645e01c8ad9c620b77abd9af6cf7fe7c26ab4ea
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335407"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Como: Configurar a política de início de sessão de risco

Azure Active Directory detecta [tipos de eventos de risco](../reports-monitoring/concept-risk-events.md#risk-event-types) em tempo real e offline. Cada evento de risco que foi detectado para uma entrada de um usuário contribui para um conceito lógico chamado de entrada arriscada. Uma entrada arriscada é um indicador de uma tentativa de entrada que pode não ter sido executada pelo proprietário legítimo de uma conta de usuário.

## <a name="what-is-the-sign-in-risk-policy"></a>O que é a política de risco de entrada?

O Azure AD analisa cada entrada de um usuário. O objetivo da análise é detectar ações suspeitas que venham junto com a entrada. Por exemplo, a entrada é feita usando um endereço IP anônimo ou a entrada foi iniciada a partir de um local desconhecido? No Azure AD, as ações suspeitas que o sistema pode detectar também são conhecidas como eventos de risco. Com base nos eventos de risco que foram detectados durante uma entrada, o Azure AD calcula um valor. O valor representa a probabilidade (baixa, média, alta) que a entrada não é executada pelo usuário legítimo. A probabilidade é chamada de **nível de risco de entrada**.

A política de risco de entrada é uma resposta automatizada que você pode configurar para um nível de risco de entrada específico. Em sua resposta, você pode bloquear o acesso aos seus recursos ou exigir a passagem de um desafio de MFA (autenticação multifator) para obter acesso.
   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Como fazer acessar a política de risco de entrada?
   
A política de risco de entrada está na seção **Configurar** na [página Azure ad Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de risco de entrada](./media/howto-sign-in-risk-policy/1014.png "Política de risco de entrada")

## <a name="policy-settings"></a>Definições de política

Ao configurar a política de risco de entrada, você precisa definir:

- Os usuários e grupos aos quais a política se aplica:

    ![Utilizadores e grupos](./media/howto-sign-in-risk-policy/11.png)

- O nível de risco de entrada que dispara a política:

    ![Nível de risco do início de sessão](./media/howto-sign-in-risk-policy/12.png)

- O tipo de acesso que você deseja impor quando o nível de risco de entrada tiver sido atendido:  

    ![Aceder](./media/howto-sign-in-risk-policy/13.png)

- O estado da sua política:

    ![Impor política](./media/howto-sign-in-risk-policy/14.png)

A caixa de diálogo configuração de política fornece uma opção para estimar o impacto da reconfiguração.

![Impacto estimado](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>O que deve saber

Você pode configurar uma política de segurança de risco de entrada para exigir MFA:

![Requerer MFA](./media/howto-sign-in-risk-policy/16.png)

No entanto, por motivos de segurança, essa configuração só funciona para usuários que já foram registrados para MFA. A proteção de identidade bloqueia os usuários com um requisito de MFA se eles ainda não estiverem registrados para MFA.

Se você quiser exigir MFA para entradas arriscadas, deverá:

1. Habilite a [política de registro da autenticação](howto-mfa-policy.md) multifator para os usuários afetados.
2. Exigir que os usuários afetados entrem em uma sessão não arriscada para executar um registro de MFA.

A conclusão dessas etapas garante que a autenticação multifator seja necessária para uma entrada arriscada.

A política de risco de entrada é:

- Aplicado a todo o tráfego e entradas do navegador usando a autenticação moderna.
- Não aplicado a aplicativos que usam protocolos de segurança mais antigos desabilitando o ponto de extremidade WS-Trust no IDP federado, como o ADFS.

Para obter uma visão geral da experiência do usuário relacionada, consulte:

* [Recuperação de entrada arriscada](flows.md#risky-sign-in-recovery)
* [Entrada arriscada bloqueada](flows.md#risky-sign-in-blocked)  
* [Experiências de entrada com Azure AD Identity Protection](flows.md)  

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

Para obter uma visão geral do Azure AD Identity Protection, consulte a [visão geral Azure ad Identity Protection](overview.md).
