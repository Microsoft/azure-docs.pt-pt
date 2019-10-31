---
title: 'Tutorial: integração do Azure Active Directory com o Comm100 Live Chat | Microsoft Docs'
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
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91a7bf535a4afe0d46a13bbdc5643da7d5ce3334
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73157342"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Tutorial: integração do Azure Active Directory com o Comm100 Live Chat

Neste tutorial, você aprenderá a integrar o Comm100 Live Chat com o Azure Active Directory (Azure AD).
A integração do Comm100 Live Chat com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Comm100 Live Chat.
* Você pode permitir que seus usuários façam logon automaticamente no Comm100 Live Chat (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Comm100 Live Chat, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Comm100 Live Chat

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O chat ao vivo do Comm100 dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Adicionando o Comm100 Live Chat da Galeria

Para configurar a integração do Comm100 Live chat ao Azure AD, você precisará adicionar o Comm100 Live Chat da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Comm100 Live Chat da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Comm100 Live Chat**, selecione **Comm100 Live Chat** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Chat ao vivo do Comm100 na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Comm100 Live Chat com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Comm100 Live Chat.

Para configurar e testar o logon único do Azure AD com o Comm100 Live Chat, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Comm100 Live Chat](#configure-comm100-live-chat-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Comm100 Live Chat](#create-comm100-live-chat-test-user)** – para ter um equivalente de Brenda Simon no Comm100 Live chat que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Comm100 Live Chat, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Comm100 Live Chat** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Comm100 Live Chat](common/sp-signonurl.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > O valor da URL de logon não é real. Você atualizará o valor da URL de logon com a URL de logon real, que é explicada posteriormente no tutorial.

5. O aplicativo Comm100 Live Chat espera que as asserções SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas: 

    | Nome |  Atributo de origem|
    | ---------------| --------------- |
    |   e-mail    | User. mail |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**

    g. Clique em **Guardar**.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar o chat ao vivo do Comm100** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-comm100-live-chat-single-sign-on"></a>Configurar o logon único do Comm100 Live Chat

1. Em uma janela diferente do navegador da Web, faça logon no chat ao vivo do Comm100 como um administrador de segurança.

1. No canto superior direito da página, clique em **minha conta**.

   ![Myaccount do Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. No lado esquerdo do menu, clique em **segurança** e, em seguida, clique em **logon único do Agent**.

   ![Segurança de chat ao vivo do Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Na página **logon único do Agent** , execute as seguintes etapas:

   ![Segurança de chat ao vivo do Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Copie o primeiro link realçado e cole-o na caixa de texto **URL de logon** na seção **domínio e URLs do Comm100 Live Chat** em portal do Azure.

   b. Na caixa de texto **URL de SSO do SAML** , Cole o valor da URL de **logon**copiado do portal do Azure.

   c. Na caixa de texto **URL de logout remoto** , Cole o valor da **URL de logout**copiado do portal do Azure.

   d. Clique em **escolher um arquivo** para carregar o certificado codificado de base 64 que você baixou do portal do Azure no **certificado**.

   e. Clique em **salvar alterações**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao chat ao vivo do Comm100.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Comm100 Live Chat**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Comm100 Live Chat**.

    ![O link do Comm100 Live Chat na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-comm100-live-chat-test-user"></a>Criar usuário de teste do Comm100 Live Chat

Para permitir que os usuários do Azure AD façam logon no Comm100 Live Chat, eles devem ser provisionados no chat ao vivo do Comm100. No Comm100 Live Chat, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no Comm100 Live Chat como administrador de segurança.

2. No canto superior direito da página, clique em **minha conta**.

    ![Myaccount do Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. No lado esquerdo do menu, clique em **agentes** e, em seguida, clique em **novo agente**.

    ![Agente de chat ao vivo do Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na página **novo agente** , execute as seguintes etapas:

    ![Novo agente do Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Na caixa de texto **email** , insira o email do usuário, como **brendafernandes\@contoso.com**.

    b. Na caixa de texto **nome** , insira o nome do usuário, como **Brenda**.

    c. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.

    d. Na caixa de texto **nome de exibição** , insira o nome de exibição do usuário, como **Brenda Simon**

    e. Na caixa de texto **senha** , digite sua senha.

    f. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Comm100 Live Chat no painel de acesso, você deverá entrar automaticamente no chat ao vivo do Comm100 para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

