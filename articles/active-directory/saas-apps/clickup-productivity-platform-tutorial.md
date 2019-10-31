---
title: 'Tutorial: integração do Azure Active Directory com a plataforma de produtividade ClickUp | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a plataforma de produtividade ClickUp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06853edd-e8da-4ad2-a4e6-5555d592cee5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 4647d78255f611959bb86ca00ff6920bc823d49a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158564"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Tutorial: integração do Azure Active Directory com a plataforma de produtividade ClickUp

Neste tutorial, você aprenderá a integrar a plataforma de produtividade ClickUp com o Azure Active Directory (Azure AD).
A integração da plataforma de produtividade ClickUp ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso à plataforma de produtividade ClickUp.
* Você pode permitir que seus usuários sejam conectados automaticamente à plataforma de produtividade ClickUp (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a plataforma de produtividade ClickUp, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único da plataforma de produtividade ClickUp

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A plataforma de produtividade ClickUp dá suporte ao SSO iniciado por **SP**

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Adicionando a plataforma de produtividade ClickUp da Galeria

Para configurar a integração da plataforma ClickUp Productivity ao Azure AD, você precisará adicionar a plataforma de produtividade ClickUp da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a plataforma de produtividade ClickUp da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **plataforma de produtividade ClickUp**, selecione **plataforma de produtividade ClickUp** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Plataforma de produtividade ClickUp na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com a plataforma de produtividade ClickUp, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado da plataforma de produtividade ClickUp.

Para configurar e testar o logon único do Azure AD com a plataforma de produtividade ClickUp, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único da plataforma de produtividade ClickUp](#configure-clickup-productivity-platform-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste da plataforma ClickUp Productivity](#create-clickup-productivity-platform-test-user)** – para ter um equivalente de Brenda Simon na plataforma ClickUp Productivity que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com a plataforma de produtividade ClickUp, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos da **plataforma de produtividade ClickUp** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs da plataforma de produtividade ClickUp](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL: `https://app.clickup.com/login/sso`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > O valor do identificador não é real. Atualize esse valor com o identificador real, que é explicado posteriormente neste tutorial.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download do certificado](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Configurar o logon único da plataforma de produtividade ClickUp

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário da plataforma de produtividade do ClickUp como administrador.

2. Clique no **perfil do usuário** e selecione **configurações**.

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Selecione **Microsoft**, em provedor de logon único (SSO).

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Na página **Configurar logon único da Microsoft** , execute as seguintes etapas:

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Clique em **copiar** para copiar o valor ID da entidade e cole-o na caixa de texto **identificador (ID da entidade)** na seção **configuração básica do SAML** no portal do Azure.
    
    b. Na caixa de texto **URL de metadados de Federação do Azure** , Cole o valor da URL de metadados de Federação do aplicativo copiado do portal do Azure e, em seguida, clique em **salvar**.

5. Para concluir a instalação, clique em **autenticar com a Microsoft para concluir a instalação** e autenticar com a conta da Microsoft.

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/configure4.png)

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso à plataforma de produtividade ClickUp.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **plataforma de produtividade ClickUp**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **plataforma de produtividade ClickUp**.

    ![O link da plataforma de produtividade ClickUp na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-clickup-productivity-platform-test-user"></a>Criar usuário de teste da plataforma ClickUp Productivity

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário da plataforma de produtividade do ClickUp como administrador.

2. Clique no **perfil do usuário** e selecione **usuários**.

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/user1.png)

3. Insira o endereço de email do usuário na caixa de texto e clique em **convidar**.

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > O usuário receberá a notificação e deverá aceitar o convite para ativar a conta.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco plataforma de produtividade ClickUp no painel de acesso, você deverá entrar automaticamente na plataforma de produtividade ClickUp para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

