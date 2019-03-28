---
title: Início rápido - bloquear o acesso quando um risco de sessão é detetado com o acesso condicional do Azure Active Directory | Documentos da Microsoft
description: Este guia de introdução, saiba como pode configurar uma política de acesso condicional do Azure Active Directory (Azure AD) para bloquear os inícios de sessão com base em riscos de sessão.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2018
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de5fdfab0858536cdd0cd81a81ea7cbc012b855
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518223"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Início rápido: Bloquear o acesso quando um risco de sessão é detetado com o acesso condicional do Azure Active Directory  

Para manter seu ambiente protegido, poderá querer impedir que os utilizadores suspeitos de início de sessão. [O Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analisa cada início de sessão e calcula a probabilidade de que um início de sessão tentar não foi efetuada pelo proprietário legítimo de uma conta de utilizador. A probabilidade (baixa, média, alta) é indicada na forma de um valor calculado chamado [níveis de risco de início de sessão](conditions.md#sign-in-risk). Ao definir a condição de início de sessão de risco, pode configurar uma política de acesso condicional para responder aos níveis de risco de início de sessão específicos. 

Este início rápido mostra como configurar uma [política de acesso condicional](../active-directory-conditional-access-azure-portal.md) que bloqueia um início de sessão quando foi detetado um nível de risco de início de sessão configurados. 

![Criar política](./media/app-sign-in-risk/1000.png)


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.



## <a name="prerequisites"></a>Pré-requisitos 

Para concluir o cenário deste tutorial, precisa de:

- **Acesso a uma edição do Azure AD Premium P2** -enquanto o acesso condicional é um recurso do Azure AD Premium P1, terá de uma edição de P2 porque o cenário neste início rápido requer a proteção de identidade. 

- **Proteção de identidade** -o cenário neste início rápido requer a proteção de identidade ser ativada. Se não sabe como ativar o Identity Protection, veja [ativar o Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Browser de tor** – a [Browser de Tor](https://www.torproject.org/projects/torbrowser.html.en) foi concebido para o ajudar a preservar a sua privacidade online. Proteção de identidade Deteta um início de sessão num browser de Tor como **inícios de sessão de endereços IP anónimos**, que tem um nível de risco médio. Para obter mais informações, consulte [Eventos de risco do Azure Active Directory](../reports-monitoring/concept-risk-events.md).  

- **Uma conta de teste chamado Alain Charon** – se não saiba como criar uma conta de teste, consulte [adicionar utilizadores com base na cloud](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Testar o início de sessão 

O objetivo deste passo é certificar-se de que sua conta de teste pode aceder ao seu inquilino através do Browser de Tor.

**Para testar o início de sessão:**

1. Inicie sessão no seu [portal do Azure](https://portal.azure.com) como **Alain Charon**.

2. Termine a sessão. 


## <a name="create-your-conditional-access-policy"></a>Criar a sua política de acesso condicional 

O cenário neste início rápido utiliza um início de sessão num browser de Tor para gerar um detetado **inícios de sessão de endereços IP anónimos** evento de risco. O nível de risco deste evento de risco é médio. Para responder a este evento de risco, defina a condição de início de sessão de risco como média. Num ambiente de produção, deve definir a condição de início de sessão de risco para alta ou para média e alta.     

Esta secção mostra como criar a política de acesso condicional necessário. Na sua política, defina:

|Definição |Valor|
|---     | --- |
| Utilizadores e grupos | Alain Charon  |
| Aplicações na cloud | Todas as aplicações na cloud |
| Risco de início de sessão | Médio |
| Conceder | Bloquear acesso |
 

![Criar política](./media/app-sign-in-risk/130.png)

 


**Para configurar a política de acesso condicional:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.

2. No portal do Azure, na barra de navegação esquerda, clique em **do Azure Active Directory**. 

    ![Azure Active Directory](./media/app-sign-in-risk/02.png)

3. Na **do Azure Active Directory** página, além do **segurança** secção, clique em **acesso condicional**.

    ![Acesso condicional](./media/app-sign-in-risk/03.png)
 
4. Sobre o **acesso condicional** página, na barra de ferramentas na parte superior, clique em **Add**.

    ![Name](./media/app-sign-in-risk/108.png)

5. No **New** na página a **nome** caixa de texto, tipo **bloquear o acesso de nível de risco médio**.

    ![Name](./media/app-sign-in-risk/104.png)

6. Na **atribuição** secção, clique em **utilizadores e grupos**.

    ![Utilizadores e grupos](./media/app-sign-in-risk/06.png)

7. Sobre o **utilizadores e grupos** página:

    ![Acesso condicional](./media/app-sign-in-risk/107.png)

    a. Clique em **selecionar utilizadores e grupos**e, em seguida, selecione **utilizadores e grupos**.

    b. Clique em **Selecionar**.

    c. Sobre o **selecionar** , selecione **Alain Charon**e, em seguida, clique em **selecionar**.

    d. Sobre o **utilizadores e grupos** página, clique em **feito**.

8. Clique em **aplicações na Cloud**.

    ![Aplicações na cloud](./media/app-sign-in-risk/08.png)

9. Sobre o **aplicações na Cloud** página:

    ![Acesso condicional](./media/app-sign-in-risk/109.png)

    a. Clique em **todas as aplicações na cloud**.

    b. Clique em **Concluído**.

10. Clique em **condições**. 

    ![Controlos de acesso](./media/app-sign-in-risk/19.png)

11. Sobre o **condições** página:

    ![Nível de risco do início de sessão](./media/app-sign-in-risk/21.png)

    a. Clique em **risco de início de sessão**.
 
    b. Como **configurar**, clique em **Sim**.

    c. Como o nível de risco de início de sessão, selecione **médio**.

    d. Clique em **Selecionar**.

    e. Sobre o **condições** página, clique em **feito**.



10. Na **controlos de acesso** secção, clique em **concessão**.

    ![Controlos de acesso](./media/app-sign-in-risk/10.png)

11. Sobre o **concessão** página:

    ![Acesso condicional](./media/app-sign-in-risk/105.png)

    a. Selecione **bloquear o acesso**.

    b. Clique em **Selecionar**.

12. Na **ativar política** secção, clique em **no**.

    ![Activar política](./media/app-sign-in-risk/18.png)

13. Clique em **Criar**.


## <a name="evaluate-a-simulated-sign-in"></a>Avaliar um simulado início de sessão

Agora que configurou a política de acesso condicional, provavelmente quer saber se funciona conforme esperado. Como primeiro passo, utilize o acesso condicional **e se a ferramenta de política** para simular um início de sessão do seu utilizador de teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.  

Quando executa o **e se a ferramenta de política** para este cenário, o **bloquear o acesso de nível de risco médio** deverá ser listada nas **políticas que serão aplicadas**. 

![Utilizador](./media/app-sign-in-risk/117.png)


**Para avaliar a sua política de acesso condicional:**

1. Sobre o [acesso condicional - políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) página, no menu na parte superior, clique em **e se**.  
 
    ![What If](./media/app-sign-in-risk/14.png)

2. Clique em **usuário**, selecione **Alan Charon** no **utilizadores** página e, em seguida, clique em **selecione**.

    ![Utilizador](./media/app-sign-in-risk/116.png)

3. Como **início de sessão de risco**, selecione **médio**.

    ![Utilizador](./media/app-sign-in-risk/119.png)


3. Clique em **e se**.


## <a name="test-your-conditional-access-policy"></a>Testar a sua política de acesso condicional

Na secção anterior, aprendeu como avaliar um simulado início de sessão. Além de uma simulação, também deve testar sua política de acesso condicional para se certificar de que ele funciona conforme esperado. 

Para testar a sua política, tente iniciar sessão no seu [portal do Azure](https://portal.azure.com) como **Alan Charon** usando o navegador de Tor. A tentativa de início de sessão deve ser bloqueada pela sua política de acesso condicional.

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o utilizador de teste, o navegador de Tor e a política de acesso condicional:

- Se não souber como eliminar um utilizador do Azure AD, veja [eliminar utilizadores do Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Para eliminar a política, selecione a política e, em seguida, clique em **eliminar** na barra de ferramentas de acesso rápido.

    ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- Para obter instruções remover o navegador de Tor, consulte [desinstalar](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Exigir a termos de utilização para ser aceite](require-tou.md)
> [exigir a MFA para aplicações específicas](app-based-mfa.md)

