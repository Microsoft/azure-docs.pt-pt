---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Comm100 Live Chat | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e41161a2e1bfd5544410bba0cb470bacbf152fd2
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561260"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Comm100 Live Chat

Neste tutorial, você aprenderá a integrar o Comm100 Live Chat com o Azure Active Directory (Azure AD). Ao integrar o Comm100 Live chat ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Comm100 Live Chat.
* Habilite seus usuários a serem conectados automaticamente ao Comm100 Live Chat com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Comm100 Live Chat.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O chat ao vivo do Comm100 dá suporte ao SSO iniciado pelo **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Adicionando o Comm100 Live Chat da Galeria

Para configurar a integração do Comm100 Live chat ao Azure AD, você precisará adicionar o Comm100 Live Chat da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Comm100 Live Chat** na caixa de pesquisa.
1. Selecione **chat ao vivo do Comm100** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Configurar e testar o logon único do Azure AD para o Comm100 Live Chat

Configure e teste o SSO do Azure AD com o Comm100 Live Chat usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Comm100 Live Chat.

Para configurar e testar o SSO do Azure AD com o Comm100 Live Chat, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Comm100 Live Chat](#configure-comm100-live-chat-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Comm100 Live Chat](#create-comm100-live-chat-test-user)** – para ter um equivalente de B. Simon no chat ao vivo do Comm100 que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Comm100 Live Chat** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > O valor da URL de logon não é real. Você atualizará o valor da URL de logon com a URL de logon real, que é explicada posteriormente no tutorial.

1. O aplicativo Comm100 Live Chat espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/edit-attribute.png)

1. Além do Comm100, o aplicativo de chat ao vivo espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.

    | Nome |  Atributo de origem|
    | ---------------| --------------- |
    |   e-mail    | user.mail |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar o chat ao vivo do Comm100** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Comm100 Live Chat.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Comm100 Live Chat**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-comm100-live-chat-sso"></a>Configurar SSO do Comm100 Live Chat

1. Em uma janela diferente do navegador da Web, entre no Comm100 Live Chat como um administrador de segurança.

1. No canto superior direito da página, clique em **minha conta**.

   ![Myaccount do Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. No lado esquerdo do menu, clique em **segurança** e, em seguida, clique em **logon único do Agent**.

   ![Segurança de chat ao vivo do Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Na página **logon único do Agent** , execute as seguintes etapas:

   ![Segurança de chat ao vivo do Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Copie o primeiro link realçado e cole-o na caixa de texto **URL de logon** na seção **configuração básica do SAML** em portal do Azure.

   b. Na caixa de texto **URL de SSO do SAML** , Cole o valor da URL de **logon**copiado do portal do Azure.

   c. Na caixa de texto **URL de logout remoto** , Cole o valor da **URL de logout**copiado do portal do Azure.

   d. Clique em **escolher um arquivo** para carregar o certificado codificado de base 64 que você baixou do portal do Azure no **certificado**.

   e. Clique em **Guardar Alterações**.

### <a name="create-comm100-live-chat-test-user"></a>Criar usuário de teste do Comm100 Live Chat

Para permitir que os usuários do Azure AD entrem no chat ao vivo do Comm100, eles devem ser provisionados no chat ao vivo do Comm100. No Comm100 Live Chat, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Comm100 Live Chat como um administrador de segurança.

2. No canto superior direito da página, clique em **minha conta**.

    ![Myaccount do Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. No lado esquerdo do menu, clique em **agentes** e, em seguida, clique em **novo agente**.

    ![Agente de chat ao vivo do Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na página **novo agente** , execute as seguintes etapas:

    ![Novo agente do Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Na caixa de texto **email** , insira o email do usuário, como **B. Simon\@contoso.com**.

    b. Na caixa de texto **nome** , insira o nome do usuário como **B**.

    c. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.

    d. Na caixa de texto **nome de exibição** , insira o nome de exibição do usuário, como **B. Simon**

    e. Na caixa de texto **senha** , digite sua senha.

    f. Clique em **Guardar**.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Comm100 Live Chat no painel de acesso, você deverá entrar automaticamente no chat ao vivo do Comm100 para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o chat ao vivo do Comm100 com o Azure AD](https://aad.portal.azure.com/)

