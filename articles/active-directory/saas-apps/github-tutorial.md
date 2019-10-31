---
title: 'Tutorial: integração do Azure Active Directory com o GitHub | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5021d3a8bed48003f38f01c3d61eac4bdd96b0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159217"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Tutorial: integração do Azure Active Directory com o GitHub

Neste tutorial, você aprenderá a integrar o GitHub ao Azure Active Directory (Azure AD).
A integração do GitHub ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao GitHub.
* Você pode permitir que seus usuários façam logon automaticamente no GitHub (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o GitHub, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma organização do GitHub criada no [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), que requer o [plano de cobrança do GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O GitHub dá suporte ao SSO iniciado pelo **SP**

* O GitHub dá suporte ao [provisionamento **automatizado** de usuários](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>Adicionando o GitHub da Galeria

Para configurar a integração do GitHub ao Azure AD, você precisará adicionar o GitHub da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o GitHub da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **GitHub**, selecione **github.com** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![GitHub na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o GitHub, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do GitHub.

Para configurar e testar o logon único do Azure AD com o GitHub, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do GitHub](#configure-github-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do GitHub](#create-github-test-user)** – para ter um equivalente de Brenda Simon no GitHub que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o GitHub, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **GitHub** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do GitHub](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>/sso`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com a URL de entrada e o identificador reais. Aqui, sugerimos que você use o valor exclusivo da cadeia de caracteres no identificador. Acesse a seção de administração do GitHub para recuperar esses valores.

5. Seu aplicativo GitHub espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo GitHub espera que **nameidentifier** seja mapeado com **User. mail**, portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![imagem](common/edit-attribute.png)

6. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

7. Na seção **Configurar o GitHub** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-github-single-sign-on"></a>Configurar o logon único do GitHub

1. Em uma janela diferente do navegador da Web, faça logon em seu site de organização do GitHub como administrador.

2. Navegue até **configurações** e clique em **segurança**

    ![Definições](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Marque a caixa **habilitar autenticação SAML** , revelando os campos de configuração de logon único. Em seguida, use o valor da URL de logon único para atualizar a URL de logon único na configuração do Azure AD.

    ![Definições](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Configure os seguintes campos:

    ![Definições](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon** que você copiou do portal do Azure.

    b. Na caixa de texto **emissor** , Cole o valor do **identificador do Azure ad** copiado do portal do Azure.

    c. Abra o certificado baixado de portal do Azure no bloco de notas, Cole o conteúdo na caixa de texto **certificado público** .

    d. Clique no ícone **Editar** para editar o **método de assinatura** e o método de **Resumo** de **RSA-SHA1** e **SHA1** para **RSA-SHA256** e **SHA256** , conforme mostrado abaixo.

    ![imagem](./media/github-tutorial/tutorial_github_sha.png)

5. Clique em **testar configuração do SAML** para confirmar que não há falhas de validação ou erros durante o SSO.

    ![Definições](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Clicar em **Guardar**

> [!NOTE]
> O logon único no GitHub é autenticado em uma organização específica no GitHub e não substitui a autenticação do próprio GitHub. Portanto, se a sessão github.com do usuário tiver expirado, você poderá ser solicitado a autenticar com a ID/senha do GitHub durante o processo de logon único.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao GitHub.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **GitHub**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **GitHub**.

    ![O link do GitHub na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-github-test-user"></a>Criar usuário de teste do GitHub

O objetivo desta seção é criar um usuário chamado Brenda Simon no GitHub. O GitHub dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [aqui](github-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do GitHub como administrador.

2. Clique em **pessoas**.

    ![Porta](./media/github-tutorial/tutorial_github_config_github_08.png "Pessoas")

3. Clique em **convidar membro**.

    ![Convidar usuários](./media/github-tutorial/tutorial_github_config_github_09.png "Convidar usuários")

4. Na página da caixa de diálogo **convidar membro** , execute as seguintes etapas:

    a. Na caixa de texto **email** , digite o endereço de email da conta Brenda Simon.

    ![Convidar pessoas](./media/github-tutorial/tutorial_github_config_github_10.png "Convidar pessoas")

    b. Clique em **Enviar convite**.

    ![Convidar pessoas](./media/github-tutorial/tutorial_github_config_github_11.png "Convidar pessoas")

    > [!NOTE]
    > O titular da conta Azure Active Directory receberá um email e seguirá um link para confirmar sua conta antes que ela se torne ativa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do GitHub no painel de acesso, você deverá ser conectado automaticamente ao GitHub para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
