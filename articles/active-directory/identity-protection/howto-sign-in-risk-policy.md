---
title: Como configurar a política de risco de início de sessão no Azure Active Directory Identity Protection | Documentos da Microsoft
description: Saiba como configurar a política de risco de início de sessão do Azure AD Identity Protection.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: da3f03cfcf821fa093e9f21cf9a11a2d99d67683
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999035"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Como: Configurar a política de início de sessão de risco

O Azure Active Directory Deteta [tipos de eventos de risco](../reports-monitoring/concept-risk-events.md#risk-event-types) no offline e em tempo real. Cada evento de risco que foi detetado um início de sessão de um utilizador contribui para um conceito lógico chamado o início de sessão arriscado. Um risco início de sessão é um indicador de uma tentativa de início de sessão não poderão ter sido executada pelo proprietário legítimo de uma conta de utilizador.


## <a name="what-is-the-sign-in-risk-policy"></a>O que é a política de risco de início de sessão?

O Azure AD analisa cada início de sessão de um utilizador. É o objetivo da análise detetar as ações suspeitas que acompanham o início de sessão. Por exemplo, o início de sessão efetuado com um endereço IP anónimo, ou o início de sessão iniciado a partir de uma localização não familiar? No Azure AD, as ações suspeitas que consegue detetar o sistema também são conhecidos como eventos de risco. Com base em eventos de risco que foram detetados durante um início de sessão, do Azure AD calcula um valor. O valor representa a probabilidade (baixa, média, alta) que o início de sessão não é efetuado pelo usuário legítimo. Denomina-se a probabilidade **nível de risco de início de sessão**.

A política de risco de início de sessão é uma resposta automática, que pode configurar para um nível de risco de início de sessão específicos. Em sua resposta, pode bloquear o acesso aos seus recursos ou exigir passando um desafio de autenticação multifator (MFA) para obter acesso.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Como posso aceder a política de risco de início de sessão?
   
A política de risco de início de sessão está no **configurar** secção sobre o [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de início de sessão de risco](./media/howto-sign-in-risk-policy/1014.png "política de risco de início de sessão")


## <a name="policy-settings"></a>Definições da política

Ao configurar a política de risco de início de sessão, tem de definir:

- Os utilizadores e grupos que a política aplica-se a:

    ![Utilizadores e grupos](./media/howto-sign-in-risk-policy/11.png)

- O nível de risco de início de sessão que aciona a política:

    ![Nível de risco do início de sessão](./media/howto-sign-in-risk-policy/12.png)

- O tipo de acesso que pretende ser imposta quando tiver sido cumprido o seu nível de risco de início de sessão:  

    ![Access](./media/howto-sign-in-risk-policy/13.png)

- O estado da política:

    ![Impor a política](./media/howto-sign-in-risk-policy/14.png)


A caixa de diálogo de configuração de política fornece uma opção para calcular o impacto de reconfiguração.

![Impacto estimado](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>O que deve saber

Pode configurar uma política de segurança de risco de início de sessão para exigir a MFA:

![Requerer MFA](./media/howto-sign-in-risk-policy/16.png)

No entanto, por motivos de segurança, esta definição só funciona para os utilizadores que já tenham sido registados para a MFA. Proteção de identidade bloqueia os utilizadores com um requisito de MFA se eles não estão registados para o MFA ainda.

Se quiser exigir a MFA para inícios de sessão de risco, deve:

1. Ativar a [política de registo de autenticação multifator](howto-mfa-policy.md) para os utilizadores afetados.

2. Requer que os utilizadores afetados iniciar sessão a uma sessão não risco para efetuar um registo na MFA.

Concluir estes passos garante que a autenticação multifator é necessária um risco para início de sessão.

A política de risco de início de sessão é:

- Aplicadas a todo o tráfego de navegador e inícios de sessão que utilizam autenticação moderna.

- Não é aplicada a aplicações através de protocolos de segurança mais antigos, desativando o ponto de extremidade do WS-Trust no IDP federado, por exemplo, o AD FS.


Para uma descrição geral da experiência do usuário relacionadas, consulte:

* [Recuperação de início de sessão arriscada](flows.md#risky-sign-in-recovery)
* [Risco início de sessão bloqueado](flows.md#risky-sign-in-blocked)  
* [Experiências de início de sessão com o Azure AD Identity Protection](flows.md)  

## <a name="best-practices"></a>Melhores práticas

Escolher uma **elevada** limiar reduz o número de vezes que uma política é acionada e minimiza o impacto para os utilizadores.  

No entanto, exclui **baixa** e **médio** inícios de sessão sinalizados para risco da política, que não pode bloquear um invasor a exploração de uma identidade comprometida.

Ao definir a política

- Excluir os usuários que não o fizer / não é possível ter a autenticação multifator

- Excluir os usuários em localidades onde o ativar a política não é prático (por exemplo, sem acesso ao suporte técnico)

- Excluir utilizadores que têm propensão para gerar vários falso-positivos (desenvolvedores, analistas de segurança)

- Utilize um **elevada** limiar durante a política inicial faseada, ou se deve minimizar desafios vistos pelos utilizadores finais.

- Utilize um **baixa** limiar se a sua organização precisar de maior segurança. Selecionar uma **baixa** limiar introduz utilizador adicional início de sessão desafios, mas uma maior segurança.

A predefinição recomendada na maioria das organizações é configurar uma regra para um **médio** limiar para encontrar o equilíbrio entre segurança e usabilidade.






## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [descrição geral do Azure AD Identity Protection](overview.md).
