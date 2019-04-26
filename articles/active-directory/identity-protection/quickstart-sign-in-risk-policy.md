---
title: Bloquear o acesso quando um risco de sessão é detetado com o Azure Active Directory Identity Protection - guia de introdução | Documentos da Microsoft
description: Este guia de introdução, saiba como pode configurar uma política de acesso condicional do Azure Active Directory (Azure AD) Identity Protection início de sessão de risco para bloquear os inícios de sessão com base em riscos de sessão.
services: active-directory
keywords: proteção de identidade, o acesso condicional para aplicações, o acesso condicional com o Azure AD, o acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f5127342f97a90103ef56efbd7465832440ec0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381825"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Início rápido: Bloquear o acesso quando um risco de sessão é detetado com o Azure Active Directory Identity Protection  

Para manter seu ambiente protegido, poderá querer impedir que os utilizadores suspeitos de iniciar sessão. O Azure Active Directory (Azure AD) Identity Protection analisa cada início de sessão e calcula a probabilidade de que um início de sessão tentar não foi efetuada pelo proprietário legítimo de uma conta de utilizador. A probabilidade (baixa, média, alta) é indicada na forma de um valor calculado chamado de nível de risco de início de sessão. Ao definir a condição de início de sessão de risco, pode configurar uma política de acesso condicional do risco de início de sessão para responder aos níveis de risco de início de sessão específicos. 

Este início rápido mostra como configurar uma política de acesso condicional de início de sessão de risco bloquear um início de sessão quando um meio e acima de risco de início de sessão ao nível foi detetado. 

![Criar política](./media/quickstart-sign-in-risk-policy/1004.png)


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.



## <a name="prerequisites"></a>Pré-requisitos 

Para concluir o cenário deste tutorial, precisa de:

- **Acesso a uma edição do Azure AD Premium P2** -Azure AD Identity Protection é uma funcionalidade do Azure AD Premium P2. 

- **Proteção de identidade** -o cenário neste início rápido requer a proteção de identidade ser ativada. Se não sabe como ativar o Identity Protection, veja [ativar o Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Browser de tor** – a [Browser de Tor](https://www.torproject.org/projects/torbrowser.html.en) foi concebido para o ajudar a preservar a sua privacidade online. Proteção de identidade Deteta um início de sessão num browser de Tor como **inícios de sessão de endereços IP anónimos**, que tem um nível de risco médio. Para obter mais informações, consulte [Eventos de risco do Azure Active Directory](../reports-monitoring/concept-risk-events.md).  

- **Uma conta de teste chamado Alain Charon** – se não saiba como criar uma conta de teste, consulte [adicionar um novo utilizador](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Testar o início de sessão 

O objetivo deste passo é certificar-se de que sua conta de teste pode aceder ao seu inquilino através do Browser de Tor.

**Para testar o início de sessão:**

1. Inicie sessão no seu [portal do Azure](https://portal.azure.com) como **Alain Charon**.

2. Termine a sessão. 


## <a name="create-your-conditional-access-policy"></a>Criar a sua política de acesso condicional 

O cenário neste início rápido utiliza um início de sessão num browser de Tor para gerar um detetado **inícios de sessão de endereços IP anónimos** evento de risco. O nível de risco deste evento de risco é médio. Para responder a este evento de risco, defina a condição de início de sessão de risco como média. 

Esta secção mostra como criar a política de acesso condicional do risco de início de sessão necessário. Na sua política, defina:

|Definição |Value|
|---     | --- |
| Utilizadores  | Alain Charon  |
| Condições | Início de sessão risco, médio e acima |
| Controlos | Bloquear acesso |
 

![Criar política](./media/quickstart-sign-in-risk-policy/201.png)

 


**Para configurar a política de acesso condicional:**

1. Inicie sessão no seu [portal do Azure](https://portal.azure.com) como administrador global.

2. Vá para o [página do Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
 
3. Na **do Azure AD Identity Protection** página, além do **configurar** secção, clique em **política de risco de início de sessão**.
 
4. Na página de política, na **atribuições** secção, clique em **utilizadores**.

5. Sobre o **usuários** página, clique em **selecionar utilizadores**.

6. Sobre o **selecionar utilizadores** , selecione **Alain Charon**e, em seguida, clique em **selecionar**.

7. Sobre o **usuários** página, clique em **feito**. 

8. Na página de política, na **atribuições** secção, clique em **condições**.

9. Sobre o **condições** página, clique em **risco de início de sessão**.

10. Sobre o **início de sessão de risco** página, selecione **médio e acima**e, em seguida, clique em **selecione**. 

11. Sobre o **condições** página, clique em **feito**.

12. Na página de política, na **controles** secção, clique em **acesso**.

13. Sobre o **acesso** página, clique em **permitir o acesso**, selecione **exigir autenticação multifator**e, em seguida, clique em **selecione**.

14. Na página de política, clique em **guardar**.  


## <a name="test-your-conditional-access-policy"></a>Testar a sua política de acesso condicional

Para testar a sua política, tente iniciar sessão no seu [portal do Azure](https://portal.azure.com) como **Alan Charon** usando o navegador de Tor. A tentativa de início de sessão deve ser bloqueada pela sua política de acesso condicional.

![Multi-Factor Authentication](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o utilizador de teste, o navegador de Tor e desativar a política de acesso condicional do risco de início de sessão:

- Se não souber como eliminar um utilizador do Azure AD, veja [como adicionar ou eliminar utilizadores](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Para obter instruções remover o navegador de Tor, consulte [desinstalar](https://tb-manual.torproject.org/uninstalling/).


