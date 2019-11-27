---
title: 'Tutorial: integração do Azure Active Directory com o bônus | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o bônus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9c88f8eb8ac7b72f11ff286d2294df8cb70860
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232050"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: integração do Azure Active Directory com o bônus

Neste tutorial, você aprende a integrar o bônus com o Azure Active Directory (Azure AD).
A integração de bônus com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao bônus.
* Você pode permitir que seus usuários sejam automaticamente conectados ao bônus (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o bônus, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do bônus

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O dá suporte a SSO iniciado pelo **IDP** de bônus

## <a name="adding-bonusly-from-the-gallery"></a>Adicionando bônus da Galeria

Para configurar a integração de bônus ao Azure AD, você precisa adicionar o bônus da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o bônus por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **bônus**, selecione **bônus** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Bónus na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o bônus, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no bônus.

Para configurar e testar o logon único do Azure AD com o bônus, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único de bônus](#configure-bonusly-single-sign-on)** para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste de bônus](#create-bonusly-test-user)** : para ter um equivalente de Brenda Simon no bônus que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o bônus, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **bônus** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs de bônus](common/idp-reply.png)

    Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de resposta real. Contate a [equipe de suporte ao cliente de bônus](https://bonus.ly/contact) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na seção **certificado de autenticação SAML** , clique no botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** .

    ![Editar certificado de autenticação SAML](common/edit-certificate.png)

6. Na seção **certificado de autenticação SAML** , copie a **impressão digital** e salve-a no computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

7. Na seção **Configurar bônus** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-bonusly-single-sign-on"></a>Configurar o logon único de bônus

1. Em uma janela diferente do navegador, entre no seu locatário **bônus** .

1. Na barra de ferramentas na parte superior, clique em **configurações** e, em seguida, selecione **integrações e aplicativos**.

    ![Seção social de bônus](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Em **logon único**, selecione **SAML**.

1. Na página da caixa de diálogo **SAML** , execute as seguintes etapas:

    ![Página de diálogo do SAML de bônus](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. Na caixa de texto **URL de destino do SSO do IDP** , Cole o valor da URL de **logon**copiado de portal do Azure.

    b. Na caixa de texto **URL de logon do IDP** , Cole o valor da URL de **logon**copiado de portal do Azure.

    c. Na caixa de texto **emissor do IDP** , Cole o valor do **identificador do Azure ad**que você copiou do portal do Azure.
    
    d. Cole o valor de **impressão digital** copiado de portal do Azure na caixa de texto **impressão digital do certificado** .
    
    e. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao bônus.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **bônus**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **bônus**.

    ![O link de bônus na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-bonusly-test-user"></a>Criar usuário de teste de bônus

Para permitir que os usuários do AD do Azure entrem no bônus, eles devem ser provisionados no bônus. No caso de bônus, o provisionamento é uma tarefa manual.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário bônus ou APIs fornecidas por bônus para provisionar contas de usuário do Azure AD. 

**Para configurar o provisionamento de usuário, execute as seguintes etapas:**

1. Em uma janela do navegador da Web, entre no seu locatário bônus.

1. Clique em **configurações**.

    ![Definições](./media/bonus-tutorial/ic781041.png "Definições")

1. Clique na guia **usuários e bônus** .

    ![Usuários e bônus](./media/bonus-tutorial/ic781042.png "Usuários e bônus")

1. Clique em **gerenciar usuários**.

    ![Gerenciar usuários](./media/bonus-tutorial/ic781043.png "Gerenciar usuários")

1. Clique em **Adicionar usuário**.

    ![Adicionar usuário](./media/bonus-tutorial/ic781044.png "Adicionar Utilizador")

1. Na caixa de diálogo **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar usuário](./media/bonus-tutorial/ic781045.png "Adicionar Utilizador")  

    a. Na caixa de texto **nome** , insira o nome do usuário, como **Brenda**.

    b. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.

    c. Na caixa de texto **email** , insira o email do usuário, como `brittasimon\@contoso.com`.

    d. Clique em **Guardar**.

    > [!NOTE]
    > O titular da conta do Azure AD recebe um email que inclui um link para confirmar a conta antes que ela se torne ativa.  

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco de bônus no painel de acesso, você deverá entrar automaticamente no bônus para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
