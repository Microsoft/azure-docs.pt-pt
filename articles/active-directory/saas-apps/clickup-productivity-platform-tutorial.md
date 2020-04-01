---
title: 'Tutorial: Integração de Diretório Ativo Azure com plataforma de produtividade ClickUp [ ClickUp] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e a Plataforma de Produtividade ClickUp.
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
ms.openlocfilehash: f497955b698d7ca390b40686e94d553a7cd5c948
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048709"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Tutorial: Integração de Diretório Ativo Azure com plataforma de produtividade ClickUp

Neste tutorial, aprende-se a integrar a Plataforma de Produtividade clickUp com o Diretório Ativo Azure (Azure AD).
Integrar a Plataforma de Produtividade clickUp com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso à Plataforma de Produtividade ClickUp.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Plataforma de Produtividade clickUp (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Anúncio Azure com a Plataforma de Produtividade ClickUp, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por sinal único da Plataforma de Produtividade clickUp

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Plataforma de Produtividade ClickUp suporta **SP** iniciado SSO

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Adicionar plataforma de produtividade ClickUp a partir da galeria

Para configurar a integração da Plataforma de Produtividade ClickUp em Azure AD, precisa de adicionar a Plataforma de Produtividade ClickUp da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar a Plataforma de Produtividade ClickUp a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite a **Plataforma de Produtividade ClickUp,** selecione **ClickUp Productivity Platform** a partir do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![Plataforma de Produtividade ClickUp na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure ad com a Plataforma de Produtividade ClickUp com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Plataforma de Produtividade ClickUp.

Para configurar e testar o único sign-on do Azure AD com a Plataforma de Produtividade ClickUp, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a plataforma de produtividade ClickUp Single Sign-On](#configure-clickup-productivity-platform-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de teste da Plataforma de **[Produtividade ClickUp](#create-clickup-productivity-platform-test-user)** - para ter uma contrapartida da Britta Simon na Plataforma de Produtividade ClickUp que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on do Azure AD com a Plataforma de Produtividade ClickUp, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da Plataforma de **Produtividade ClickUp,** selecione **um único sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![ClickUp Productivity Platform Domain and URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL:`https://app.clickup.com/login/sso`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > O valor do identificador não é real. Atualize este valor com o identificador real, o que é explicado mais tarde neste tutorial.

5. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Configure clickUp plataforma de produtividade único sign-on

1. Numa janela de navegador web diferente, inscreva-se no seu inquilino da Plataforma de Produtividade ClickUp como administrador.

2. Clique no **perfil do Utilizador**e, em seguida, selecione **Definições**.

    ![Configuração de Produtividade clickUp](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Configuração de Produtividade clickUp](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Selecione **Microsoft**, no fornecedor de um único sinal (SSO).

    ![Configuração de Produtividade clickUp](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Na página **Configure Microsoft Single Sign On,** execute os seguintes passos:

    ![Configuração de Produtividade clickUp](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Clique em **Copiar** para copiar o valor id da entidade e cole-o na caixa de texto **identificador (Id entidade)** na secção **de Configuração Básica SAML** no portal Azure.
    
    b. Na caixa de texto **URL da Federação Azure,** colá o valor de url de metadados da Federação de Aplicações, que copiou do portal Azure e, em seguida, clique em **Guardar**.

5. Para completar a configuração, clique em **Authenticate com a Microsoft para completar a configuração** e autenticar com a conta da Microsoft.

    ![Configuração de Produtividade clickUp](./media/clickup-productivity-platform-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a Britta Simon utilize um único sign-on Azure, concedendo acesso à Plataforma de Produtividade ClickUp.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **ClickUp Productivity Platform**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ClickUp Productivity Platform**.

    ![O link clickUp Productivity Platform na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-clickup-productivity-platform-test-user"></a>Criar o utilizador de teste da Plataforma de Produtividade clickUp

1. Numa janela de navegador web diferente, inscreva-se no seu inquilino da Plataforma de Produtividade ClickUp como administrador.

2. Clique no **perfil do Utilizador**e, em seguida, selecione **Pessoas**.
   
    ![Configuração de Produtividade clickUp](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Configuração de Produtividade clickUp](./media/clickup-productivity-platform-tutorial/user1.png)

3. Introduza o endereço de e-mail do utilizador na caixa de texto e clique em **Convidar**.

    ![Configuração de Produtividade clickUp](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > O utilizador receberá a notificação e deve aceitar o convite para ativar a conta.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Plataforma de Produtividade ClickUp no Painel de Acesso, deve ser automaticamente inscrito na Plataforma de Produtividade ClickUp para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

