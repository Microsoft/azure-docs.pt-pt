---
title: Como configurar a política de risco do utilizador no Azure Active Directory Identity Protection | Documentos da Microsoft
description: Saiba como configurar a política de risco de utilizador do Azure AD Identity Protection.
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
ms.openlocfilehash: 8caf7d0670246d14c5197fda763826b970bd125e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993077"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Como: Configurar a política de utilizador de risco

Com o risco de utilizador, o Azure AD Deteta a probabilidade de que uma conta de utilizador foi comprometida. Como administrador, pode configurar uma política de acesso condicional de risco de utilizador, responder automaticamente a um nível de risco de utilizador específico.
 
Este artigo fornece as informações necessárias configurar uma política de risco do utilizador.


## <a name="what-is-a-user-risk-policy"></a>O que é uma política de risco do utilizador?

O Azure AD analisa cada início de sessão de um utilizador. É o objetivo da análise detetar as ações suspeitas que acompanham o início de sessão. No Azure AD, as ações suspeitas que consegue detetar o sistema também são conhecidos como eventos de risco. Embora alguns riscos eventos podem ser detectados em tempo real, também existem eventos de risco que exigem mais tempo. Por exemplo, para detectar uma deslocação impossível para localizações atípicas, o sistema exige um período de aprendizagem inicial de 14 dias para saber mais sobre o comportamento normal de um utilizador. Existem várias opções para resolver eventos de risco detetados. Por exemplo, pode resolver eventos de risco individuais manualmente ou pode obtê-las resolvido através de um risco de início de sessão ou uma política de acesso condicional de risco do utilizador.

Todos os eventos de risco que foram detetados para um utilizador e não são resolvidos são conhecidos como eventos de risco de Active Directory. Os eventos de risco de Active Directory que estão associados um utilizador são conhecidos como risco de utilizador. Com base no risco do utilizador, o Azure AD calcula uma probabilidade (baixa, média, alta) que um utilizador foi comprometido. A probabilidade é chamada de nível de risco do utilizador.

![Riscos de utilizador](./media/howto-user-risk-policy/1031.png)

A política de risco do utilizador é uma resposta automática, que pode configurar para um nível de risco de utilizador específico. Com uma política de risco do utilizador, pode bloquear o acesso aos seus recursos ou exigir uma alteração de palavra-passe para obter uma conta de utilizador de volta num estado limpo.


## <a name="how-do-i-access-the-user-risk-policy"></a>Como posso aceder a política de risco do utilizador?
   
A política de risco de início de sessão está no **configurar** secção sobre o [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política de risco de utilizador](./media/howto-user-risk-policy/1014.png)



## <a name="policy-settings"></a>Definições da política

Ao configurar a política de risco de início de sessão, tem de definir:

- Os utilizadores e grupos que a política aplica-se a:

    ![Utilizadores e grupos](./media/howto-user-risk-policy/11.png)

- O nível de risco de início de sessão que aciona a política:

    ![O utilizador tem um nível de risco elevado](./media/howto-user-risk-policy/12.png)

- O tipo de acesso que pretende ser imposta quando tiver sido cumprido o seu nível de risco de início de sessão:  

    ![Access](./media/howto-user-risk-policy/13.png)

- O estado da política:

    ![Impor a política](./media/howto-user-risk-policy/14.png)

A caixa de diálogo de configuração de política fornece uma opção para calcular o impacto da sua configuração.

![Impacto estimado](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>O que deve saber

Pode definir uma política de segurança de risco do utilizador para impedir que os utilizadores após o início de sessão dependendo do nível de risco.

![Bloqueio](./media/howto-user-risk-policy/16.png)


Bloquear um início de sessão:

* Impede que a geração de novos eventos de risco de utilizador para o utilizador afectado
* Permite aos administradores manualmente remediar os eventos de risco que afetam a identidade do utilizador e restaurá-lo para um estado seguro

## <a name="best-practices"></a>Melhores práticas

Escolher uma **elevada** limiar reduz o número de vezes que uma política é acionada e minimiza o impacto para os utilizadores.
No entanto, exclui **baixa** e **médio** utilizadores sinalizados para risco da política, que pode não proteger identidades ou dispositivos que foram anteriormente suspeita ou conhecidos por ser comprometido.

Ao definir a política

* Excluir utilizadores que têm propensão para gerar muitos falsos positivos (desenvolvedores, analistas de segurança)
* Excluir os usuários em localidades onde o ativar a política não é prático (por exemplo, sem acesso ao suporte técnico)
* Utilize um **elevada** limiar durante o política inicial com, ou se deve minimizar desafios vistos pelos utilizadores finais.
* Utilize um **baixa** limiar se a sua organização precisar de maior segurança. Selecionar uma **baixa** limiar introduz utilizador adicional início de sessão desafios, mas uma maior segurança.

A predefinição recomendada na maioria das organizações é configurar uma regra para um **médio** limiar para encontrar o equilíbrio entre segurança e usabilidade.

Para uma descrição geral da experiência do usuário relacionadas, consulte:

* [Comprometido de fluxo de recuperação de conta](flows.md#compromised-account-recovery).  
* [Comprometido fluxo conta bloqueada](flows.md#compromised-account-blocked).  

**Para abrir a caixa de diálogo de configuração relacionados**:

- Sobre o **Azure AD Identity Protection** painel, na **configurar** secção, clique em **política de risco do utilizador**.

    ![Política de risco do utilizador](./media/howto-user-risk-policy/1009.png "política de risco do utilizador")




## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [descrição geral do Azure AD Identity Protection](overview.md).
