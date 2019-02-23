---
title: 'Tutorial: Autenticação multifator para B2B - Azure Active Directory | Documentos da Microsoft'
description: Saiba como exigir a autenticação multifator (MFA) ao utilizar o Azure AD para colaboração B2B com organizações parceiras e utilizadores externos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70f6718c13534e7f43b183400a1ccf25c3f8d1e1
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669016"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Tutorial: Impor o multi-factor authentication para utilizadores convidados B2B

Ao colaborar com utilizadores convidados B2B externos, é recomendado proteger as suas aplicações com políticas de autenticação multifator (MFA). Deste modo, os utilizadores externos precisarão de mais do que apenas um nome de utilizador e palavra-passe para aceder aos seus recursos. No Azure Active Directory (Azure AD), pode fazê-lo com uma política de acesso condicional que necessita da MFA para aceder. As políticas de MFA podem ser impostas ao nível do inquilino, da aplicação ou do utilizador convidado da mesma forma que são ativadas para os membros da sua organização.

Exemplo:

![Aplicação B2B a exigir a MFA](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  Um administrador ou colaborador na Empresa A convida um utilizador para utilizar uma aplicação no local ou na cloud configurada para exigir a MFA para aceder.
2.  O utilizador convidado inicia sessão com a respetiva identidade das redes sociais, da escola ou do trabalho. 
3.  É pedido ao utilizador que conclua o pedido da MFA. 
4.  O utilizador configura a MFA com a Empresa A e escolhe a respetiva opção de MFA. Será concedido ao utilizador acesso à aplicação.

Neste tutorial, irá:

> [!div class="checklist"]
> * Testar a experiência de início de sessão antes de configurar a MFA.
> * Criar uma política de acesso condicional que exija a MFA para aceder a uma aplicação na cloud no seu ambiente. Neste tutorial, utilizaremos a aplicação Microsoft Azure Management para ilustrar o processo.
> * Utilizar a ferramenta What If para simular o início de sessão com a MFA.
> * Testar a sua política de acesso condicional.
> * Limpar a política e o utilizador de teste.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário deste tutorial, precisa de:

 - **Acesso à edição do Azure AD Premium**, que inclui funcionalidades de política de acesso condicional. Para impor a MFA, tem de criar uma política de acesso condicional do Azure AD. Tenha em atenção que as políticas de MFA serão sempre impostas na sua organização, independentemente de o parceiro possuir funcionalidades de MFA. Se tiver configurado a MFA para a sua organização, terá de certificar-se de que possui licenças suficientes do Azure AD Premium para os seus utilizadores convidados. 
 - **Uma conta de e-mail externa válida** que pode adicionar ao diretório do seu inquilino como um utilizador convidado e utilizar para iniciar sessão. Se não souber como criar uma conta de convidado, veja [Adicionar um utilizador convidado B2B no portal do Azure](add-users-administrator.md).

## <a name="create-a-test-guest-user-in-azure-ad"></a>Criar um utilizador convidado de teste no Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador do Azure AD.
2. No painel esquerdo, selecione **Azure Active Directory**.
3.  Em **Gerir**, selecione **Utilizadores**.
4.  Selecione **Novo utilizador convidado**.

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  Em **Nome de utilizador**, introduza o endereço de e-mail do utilizador externo. Também tem a opção de incluir uma mensagem de boas-vindas. 

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  Selecione **Convidar** para enviar automaticamente o convite ao utilizador convidado. Será apresentada a mensagem **Utilizador convidado com sucesso**. 
7.  Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>Testar a experiência de início de sessão antes de configurar a MFA
1.  Utilize o seu nome de utilizador e palavra-passe de teste para iniciar sessão no [portal do Azure](https://portal.azure.com/).
2.  Tenha em atenção que pode aceder ao portal do Azure apenas com as suas credenciais de início de sessão. Não é necessária autenticação adicional.
3.  Termine a sessão.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>Criar uma política de acesso condicional que exija a MFA
1.  Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global, administrador de segurança ou administrador de acesso condicional.
2.  No portal do Azure, selecione **Azure Active Directory**. 
3.  Na página **Azure Active Directory**, na secção **Segurança**, selecione **Acesso condicional**.
4.  Na página **Acesso Condicional**, na barra de ferramentas na parte superior, selecione **Nova política**.
5.  Na página **Novo**, na caixa de texto **Nome**, escreva **Exigir a MFA para aceder ao portal do B2B**.
6.  Na secção **Atribuições**, selecione **Utilizadores e grupos**.
7.  Na página **Utilizadores e grupos**, selecione **Selecione utilizadores e grupos** e, em seguida, selecione **Todos os utilizadores convidados (pré-visualização)**.

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  Selecione **Done** (Concluído).
10. Na página **Novo**, na secção **Atribuições**, selecione **Aplicações na cloud**.
11. Na página **Aplicações na cloud**, selecione **Selecionar aplicações** e, em seguida, selecione **Selecionar**.

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. Na página **Selecionar**, selecione **Microsoft Azure Management** e, em seguida, selecione **Selecionar**.

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. Na página **Aplicações na cloud**, selecione **Concluído**.
14. Na página **Novo**, na secção **Controlos de acesso**, selecione **Conceder**.
15. Na página **Conceder**, selecione **Conceder acesso**, selecione a caixa de verificação **Exigir autenticação multifator** e, em seguida, selecione **Selecionar**.

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. Em **Ativar política**, selecione **Ativado**.

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. Selecione **Criar**.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>Utilizar a opção What If para simular o início de sessão

1.  Na página **Acesso condicional – Políticas**, selecione **What If**. 

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  Selecione **Utilizador**, selecione o seu utilizador convidado de teste e, em seguida, selecione **Selecionar**.

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  Selecione **Aplicações na cloud**.
4.  Na página **Aplicações na cloud**, selecione **Selecionar aplicações** e, em seguida, selecione **Selecionar**. Na lista de aplicações, selecione **Microsoft Azure Management** e, em seguida, selecione **Selecionar**. 

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  Na página **Aplicações na cloud**, selecione **Concluído**.
6.  Selecione **What If** e verifique se a sua nova política é apresentada em **Resultados da avaliação** no separador **Políticas que serão aplicadas**.

    ![Selecione Azure Active Directory](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>Testar a sua política de acesso condicional
1.  Utilize o seu nome de utilizador e palavra-passe de teste para iniciar sessão no [portal do Azure](https://portal.azure.com/).
2.  Deverá ver um pedido de métodos de autenticação adicionais. Tenha em atenção que poderá demorar algum tempo até que a política entre em vigor.

    ![Selecione Azure Active Directory](media/tutorial-mfa/mfa-required.png)
 
3.  Termine a sessão.

## <a name="clean-up-resources"></a>Limpar recursos
Remova o utilizador de teste e a política de acesso condicional de teste quando já não forem necessários.
1.  Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador do Azure AD.
2.  No painel esquerdo, selecione **Azure Active Directory**.
3.  Em **Gerir**, selecione **Utilizadores**.
4.  Selecione o utilizador de teste e, em seguida, selecione **Eliminar utilizador**.
5.  No painel esquerdo, selecione **Azure Active Directory**.
6.  Em **Segurança**, selecione **Acesso Condicional**.
7.  Na lista **Nome da política**, selecione o menu de contexto (…) da sua política de teste e selecione **Eliminar**. Selecione **Sim** para confirmar.
## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, criou uma política de acesso condicional que exige que os utilizadores convidados utilizem a MFA ao iniciar sessão numa das suas aplicações na cloud. Para saber mais sobre como adicionar utilizadores para colaboração, veja [Adicionar utilizadores de colaboração B2B do Azure Active Directory no portal do Azure](add-users-administrator.md).
