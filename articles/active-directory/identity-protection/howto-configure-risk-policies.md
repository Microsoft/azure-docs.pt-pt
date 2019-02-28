---
title: Como configurar políticas de risco na proteção de identidade do Azure Active Directory (atualizada) | Documentos da Microsoft
description: Como configurar políticas de risco na proteção de identidade do Azure Active Directory (atualizada).
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dd988004112fa4f420af7d0149bde19fdc06bfc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958354"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Como: Configurar políticas de risco na proteção de identidade do Azure Active Directory (atualizada)


Azure AD Deteta eventos de risco são indicadores de identidades potencialmente comprometidos. Ao configurar políticas de risco, pode definir respostas automáticas para os resultados de Deteção:

- Com a política de risco de início de sessão, pode configurar uma resposta a eventos de risco em tempo real que foram detectados durante o início de sessão do utilizador. 
- Com a política de risco do utilizador, pode configurar uma resposta para todos os riscos de utilizador do Active Directory que foram detetados para um utilizador ao longo do tempo.  

> [!VIDEO https://youtu.be/zEsbbik-BTE]
## <a name="what-is-the-sign-in-risk-policy"></a>O que é a política de risco de início de sessão?

O Azure AD analisa cada início de sessão de um utilizador. É o objetivo da análise detetar as ações suspeitas que acompanham o início de sessão. Por exemplo, o início de sessão efetuado com um endereço IP anónimo, ou o início de sessão iniciado a partir de uma localização não familiar? No Azure AD, as ações suspeitas que consegue detetar o sistema também são conhecidos como eventos de risco. Com base em eventos de risco que foram detetados durante um início de sessão, do Azure AD calcula um valor. O valor representa a probabilidade (baixa, média, alta) que o início de sessão não é efetuado pelo usuário legítimo. Denomina-se a probabilidade **nível de risco de início de sessão**.

A política de risco de início de sessão é uma resposta automática, que pode configurar para um nível de risco de início de sessão específicos. Em sua resposta, pode bloquear o acesso aos seus recursos ou exigir passando um desafio de autenticação multifator (MFA) para obter acesso.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Como posso aceder a política de risco de início de sessão?
   
A política de risco de início de sessão está no **configurar** secção sobre o [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de início de sessão de risco](./media/howto-configure-risk-policies/1014.png "política de risco de início de sessão")


## <a name="sign-in-risk-policy-settings"></a>Definições de política de risco de início de sessão

Ao configurar a política de risco de início de sessão, tem de definir:

- Os utilizadores e grupos que a política aplica-se a:

    ![Utilizadores e grupos](./media/howto-configure-risk-policies/11.png)

- O nível de risco de início de sessão que aciona a política:

    ![Nível de risco do início de sessão](./media/howto-configure-risk-policies/12.png)

- O tipo de acesso que pretende ser imposta quando tiver sido cumprido o seu nível de risco de início de sessão:  

    ![Access](./media/howto-configure-risk-policies/13.png)

- O estado da política:

    ![Impor a política](./media/howto-configure-risk-policies/14.png)


A caixa de diálogo de configuração de política fornece uma opção para calcular o impacto de reconfiguração.

![Impacto estimado](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>O que deve saber sobre as políticas de início de sessão de risco

Pode configurar uma política de segurança de risco de início de sessão para exigir a MFA:

![Requerer MFA](./media/howto-configure-risk-policies/16.png)

No entanto, por motivos de segurança, esta definição só funciona para os utilizadores que já tenham sido registados para a MFA. Proteção de identidade bloqueia os utilizadores com um requisito de MFA se eles não estão registados para o MFA ainda.

Se quiser exigir a MFA para inícios de sessão de risco, deve:

1. Ative a política de registo de autenticação multifator para os utilizadores afetados.

2. Requer que os utilizadores afetados para início de sessão numa sessão não risco para efetuar um registo na MFA.

Concluir estes passos garante que a autenticação multifator é necessária um risco para início de sessão.

A política de risco de início de sessão é:

- Aplicadas a todo o tráfego de navegador e inícios de sessão que utilizam autenticação moderna.

- Não é aplicada a aplicações através de protocolos de segurança mais antigos, desativando o ponto de extremidade do WS-Trust no IDP federado, por exemplo, o AD FS.


Para uma descrição geral da experiência do usuário relacionadas, consulte:

* [Recuperação de início de sessão arriscada](flows.md#risky-sign-in-recovery)
* [Risco início de sessão bloqueado](flows.md#risky-sign-in-blocked)  
* [Experiências de início de sessão com o Azure AD Identity Protection](flows.md)  









## <a name="what-is-a-user-risk-policy"></a>O que é uma política de risco do utilizador?

O Azure AD analisa cada início de sessão de um utilizador. É o objetivo da análise detetar as ações suspeitas que acompanham o início de sessão. No Azure AD, as ações suspeitas que consegue detetar o sistema também são conhecidos como eventos de risco. Embora alguns riscos eventos podem ser detectados em tempo real, também existem eventos de risco que exigem mais tempo. Por exemplo, para detectar uma deslocação impossível para localizações atípicas, o sistema exige um período de aprendizagem inicial de 14 dias para saber mais sobre o comportamento normal de um utilizador. Existem várias opções para resolver eventos de risco detetados. Por exemplo, pode resolver eventos de risco individuais manualmente ou pode obtê-las resolvido através de um risco de início de sessão ou uma política de acesso condicional de risco do utilizador.

Todos os eventos de risco que foram detetados para um utilizador e não são resolvidos são conhecidos como eventos de risco de Active Directory. Os eventos de risco de Active Directory que estão associados um utilizador são conhecidos como risco de utilizador. Com base no risco do utilizador, o Azure AD calcula uma probabilidade (baixa, média, alta) que um utilizador foi comprometido. A probabilidade é chamada de nível de risco do utilizador.

![Riscos de utilizador](./media/howto-configure-risk-policies/11031.png)

A política de risco do utilizador é uma resposta automática, que pode configurar para um nível de risco de utilizador específico. Com uma política de risco do utilizador, pode bloquear o acesso aos seus recursos ou exigir uma alteração de palavra-passe para obter uma conta de utilizador de volta num estado limpo.


## <a name="how-do-i-access-the-user-risk-policy"></a>Como posso aceder a política de risco do utilizador?
   
A política de risco do utilizador está no **configurar** secção sobre o [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de risco de utilizador](./media/howto-configure-risk-policies/11014.png)



## <a name="user-risk-policy-settings"></a>Definições de política de risco do utilizador

Ao configurar a política de risco do utilizador, tem de definir:

- Os utilizadores e grupos que a política aplica-se a:

    ![Utilizadores e grupos](./media/howto-configure-risk-policies/111.png)

- O nível de risco de início de sessão que aciona a política:

    ![O utilizador tem um nível de risco elevado](./media/howto-configure-risk-policies/112.png)

- O tipo de acesso que pretende ser imposta quando tiver sido cumprido o seu nível de risco de início de sessão:  

    ![Access](./media/howto-configure-risk-policies/113.png)

- O estado da política:

    ![Impor a política](./media/howto-configure-risk-policies/114.png)

A caixa de diálogo de configuração de política fornece uma opção para calcular o impacto da sua configuração.

![Impacto estimado](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>O que deve saber sobre o risco de utilizador políticas

Pode definir uma política de segurança de risco do utilizador para impedir que os utilizadores após o início de sessão dependendo do nível de risco.

![Bloqueio](./media/howto-configure-risk-policies/116.png)


Bloquear um início de sessão:

* Impede que a geração de novos eventos de risco de utilizador para o utilizador afectado
* Permite aos administradores manualmente remediar os eventos de risco que afetam a identidade do utilizador e restaurá-lo para um estado seguro


























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

 [Channel 9: O Azure AD e mostrar de identidade: Pré-visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

