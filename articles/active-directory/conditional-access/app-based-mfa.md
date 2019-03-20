---
title: Início rápido - exigir autenticação multifator (MFA) para aplicações específicas com acesso condicional do Azure Active Directory | Documentos da Microsoft
description: Este guia de introdução, saiba como é possível vincular seus requisitos de autenticação para o tipo de aplicação de cloud acedidos através do acesso condicional do Azure Active Directory (Azure AD).
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/30/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07466e40834b8bfaf4a452cf728bae517b8c7b00
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122724"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Início rápido: Exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory 

Para simplificar a experiência de início de sessão dos utilizadores, pode querer permitir-lhes iniciar sessão suas aplicações na cloud com um nome de utilizador e uma palavra-passe. No entanto, muitos ambientes têm, pelo menos, algumas aplicações para o qual é recomendado para exigir um formulário mais forte de verificação de conta, por exemplo, a autenticação multifator (MFA). Isto pode ser, para true de exemplo, para obter acesso ao sistema de e-mail da sua organização ou as suas aplicações de RH. No Azure Active Directory (Azure AD), pode concretizar este objetivo com uma política de acesso condicional.    

Este início rápido mostra como configurar uma [política de acesso condicional do Azure AD](../active-directory-conditional-access-azure-portal.md) que requer autenticação multifator para uma aplicação em nuvem selecionado no seu ambiente.

![Criar política](./media/app-based-mfa/32.png)


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.



## <a name="prerequisites"></a>Pré-requisitos 

Para concluir o cenário neste início rápido, precisa de:

- **Acesso a uma edição do Azure AD Premium** -acesso condicional do Azure AD é um recurso do Azure AD Premium. 

- **Uma conta de teste chamado Isabella Simonsen** – se não saiba como criar uma conta de teste, consulte [adicionar utilizadores com base na cloud](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


O cenário neste início rápido requer que por utilizador MFA não está ativada para a sua conta de teste. Para obter mais informações, consulte [como requerer verificação de dois passos para um utilizador](../authentication/howto-mfa-userstates.md).


## <a name="test-your-sign-in"></a>Testar o início de sessão

O objetivo deste passo é obter uma impressão da experiência de início de sessão sem uma política de acesso condicional.

**Para inicializar seu ambiente:**

1. Inicie sessão no portal do Azure como Isabella Simonsen.

2. Termine a sessão.


## <a name="create-your-conditional-access-policy"></a>Criar a sua política de acesso condicional 

Esta secção mostra como criar a política de acesso condicional necessário. O cenário neste início rápido utiliza:

- Portal do Azure como marcador de posição para uma aplicação de cloud que necessita de MFA. 
- O utilizador de exemplo para testar a política de acesso condicional.  

Na sua política, defina:

|Definição |Value|
|---     | --- |
|Utilizadores e grupos | Isabella Simonsen |
|Aplicações na cloud | Microsoft Azure Management |
|Conceder acesso | Exigir autenticação multifator |
 

![Criar política](./media/app-based-mfa/31.png)

 


**Para configurar a política de acesso condicional:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.

2. No portal do Azure, na barra de navegação esquerda, clique em **do Azure Active Directory**. 

    ![Azure Active Directory](./media/app-based-mfa/02.png)

3. Na **do Azure Active Directory** página, além do **segurança** secção, clique em **acesso condicional**.

    ![Acesso condicional](./media/app-based-mfa/03.png)
 
4. Sobre o **acesso condicional** página, na barra de ferramentas na parte superior, clique em **nova política**.

    ![Adicionar](./media/app-based-mfa/04.png)

5. No **New** na página a **nome** caixa de texto, tipo **exigir a MFA para acesso ao portal do Azure**.

    ![Name](./media/app-based-mfa/05.png)

6. Na **atribuição** secção, clique em **utilizadores e grupos**.

    ![Utilizadores e grupos](./media/app-based-mfa/06.png)

7. Sobre o **utilizadores e grupos** página, execute os seguintes passos:

    ![Utilizadores e grupos](./media/app-based-mfa/24.png)

    a. Clique em **selecionar utilizadores e grupos**e, em seguida, selecione **utilizadores e grupos**.

    b. Clique em **Selecionar**.

    c. Sobre o **selecionar** , selecione **Isabella Simonsen**e, em seguida, clique em **selecionar**.

    d. Sobre o **utilizadores e grupos** página, clique em **feito**.

8. Clique em **aplicações na Cloud**.

    ![Aplicações na cloud](./media/app-based-mfa/08.png)

9. Sobre o **aplicações na Cloud** página, execute os seguintes passos:

    ![Selecione aplicações na cloud](./media/app-based-mfa/26.png)

    a. Clique em **selecionar aplicações**.

    b. Clique em **Selecionar**.

    c. Sobre o **selecionar** , selecione **Microsoft Azure Management**e, em seguida, clique em **selecionar**.

    d. Sobre o **aplicações na Cloud** página, clique em **feito**.


10. Na **controlos de acesso** secção, clique em **concessão**.

    ![Controlos de acesso](./media/app-based-mfa/10.png)

11. Sobre o **concessão** página, execute os seguintes passos:

    ![Conceder](./media/app-based-mfa/11.png)

    a. Selecione **conceder acesso**.

    a. Selecione **exigir autenticação multifator**.

    b. Clique em **Selecionar**.

12. Na **ativar política** secção, clique em **no**.

    ![Activar política](./media/app-based-mfa/18.png)

13. Clique em **Criar**.


## <a name="evaluate-a-simulated-sign-in"></a>Avaliar um simulado início de sessão

Agora que configurou a política de acesso condicional, provavelmente quer saber se funciona conforme esperado. Como primeiro passo, utilize o acesso condicional e se a política de ferramenta para simular um início de sessão do seu utilizador de teste. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação.  

Para inicializar o que se o conjunto de ferramentas de avaliação da política,:

- **Isabella Simonsen** como utilizador 
- **Microsoft Azure Management** como aplicação de cloud

  Clicar **e se** cria um relatório de simulação, que mostra:

- **Exigir a MFA para acesso ao portal do Azure** em **políticas que serão aplicadas** 
- **Exigir autenticação multifator** como **conceder controlos de**.

![E se a ferramenta de política](./media/app-based-mfa/23.png)



**Para avaliar a sua política de acesso condicional:**

1. Sobre o [acesso condicional - políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) página, no menu na parte superior, clique em **e se**.  
 
    ![What If](./media/app-based-mfa/14.png)

2. Clique em **usuários**, selecione **Isabella Simonsen**e, em seguida, clique em **selecionar**.

    ![Utilizador](./media/app-based-mfa/15.png)

2. Para selecionar uma aplicação na cloud, execute os seguintes passos:

    ![Aplicações na cloud](./media/app-based-mfa/16.png)

    a. Clique em **aplicações na Cloud**.

    b. Sobre o **página de aplicações na Cloud**, clique em **selecionar aplicações**.

    c. Clique em **Selecionar**.

    d. Sobre o **selecionar** , selecione **Microsoft Azure Management**e, em seguida, clique em **selecionar**.

    e. Na página de aplicações na cloud, clique em **feito**.

3. Clique em **e se**.


## <a name="test-your-conditional-access-policy"></a>Testar a sua política de acesso condicional

Na secção anterior, aprendeu como avaliar um simulado início de sessão. Além de uma simulação, também deve testar sua política de acesso condicional para garantir que funciona conforme esperado. 

Para testar a sua política, tente iniciar sessão no seu [portal do Azure](https://portal.azure.com) usando sua **Isabella Simonsen** conta de teste. Deverá ver uma caixa de diálogo que requer que defina a sua conta para verificação de segurança adicional.

![Multi-Factor Authentication](./media/app-based-mfa/22.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o utilizador de teste e a política de acesso condicional:

- Se não souber como eliminar um utilizador do Azure AD, veja [eliminar utilizadores do Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Para eliminar a política, selecione a política e, em seguida, clique em **eliminar** na barra de ferramentas de acesso rápido.

    ![Multi-Factor Authentication](./media/app-based-mfa/33.png)


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Exigir a termos de utilização para ser aceite](require-tou.md)
> [bloquear o acesso quando é detetado um risco de sessão](app-sign-in-risk.md)
