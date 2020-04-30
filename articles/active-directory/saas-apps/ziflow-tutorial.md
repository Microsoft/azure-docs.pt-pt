---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Ziflow Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: d9745bdb1cb6de86a96946564865958433d49732
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67086202"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Tutorial: Integração do Diretório Ativo Azure com a Ziflow

Neste tutorial, aprende-se a integrar o Ziflow com o Azure Ative Directory (Azure AD).
Integrar o Ziflow com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Ziflow.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Ziflow (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Ziflow, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de entrada única de ziflow ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Ziflow apoia **SP** iniciado SSO

## <a name="adding-ziflow-from-the-gallery"></a>Adicionando Ziflow da galeria

Para configurar a integração do Ziflow em Azure AD, você precisa adicionar Ziflow da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Ziflow da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Ziflow**, selecione **Ziflow** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Ziflow na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com ziflow com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Ziflow.

Para configurar e testar o único sinal de Azure AD com o Ziflow, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sinal Único ziflow](#configure-ziflow-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-ziflow-test-user)** do teste Ziflow - para ter uma contrapartida da Britta Simon no Ziflow que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o Ziflow, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Ziflow,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Ziflow Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Os valores anteriores não são reais. Irá atualizar o valor único de ID no Identificador e Assinar no URL com valor real, o que é explicado mais tarde no tutorial.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção Configurar o **Ziflow,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-ziflow-single-sign-on"></a>Configure o sinal único do Ziflow

1. Numa janela diferente do navegador web, inscreva-se na Ziflow como Administrador de Segurança.

2. Clique no Avatar no canto superior direito e, em seguida, clique em **Gerir a conta**.

    ![Gestão de configuração ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. Na parte superior à esquerda, clique **em Um Sinal Único**.

    ![Sinal de configuração de ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Na página **De Sessão Individual,** execute os seguintes passos:

    ![Configuração ziflow única](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Selecione **Tipo** como **SAML2.0**.

    b. Na caixa de texto **SAL Sign In** URL, colá o valor do URL de **Login,** que copiou do portal Azure.

    c. Faça upload do certificado codificado base-64 que descarregou do portal Azure, para o Certificado de **Assinatura X509**.

    d. Na caixa de texto **url sign out,** cola o valor do URL de **Logout,** que copiou do portal Azure.

    e. A partir das Definições de Configuração para a sua secção **De identificação,** copie o valor de ID único realçado e apreenda-o com o Identificador e assine-o no URL na **Configuração Básica do SAML** no portal Azure.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo brittasimon@yourcompanydomain.extensionde campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Ziflow.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Ziflow**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Ziflow**.

    ![O link Ziflow na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-ziflow-test-user"></a>Criar o utilizador do teste Ziflow

Para permitir que os utilizadores da AD Azure assinem o Ziflow, devem ser aprovisionados no Ziflow. Em Ziflow, o provisionamento é uma tarefa manual.

Para fornecer uma conta de utilizador, execute os seguintes passos:

1. Inscreva-se na Ziflow como administrador de segurança.

2. Navegue para **as pessoas** no topo.

    ![Pessoas de configuração de Ziflow](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Clique em **Adicionar** e, em seguida, clique em **Adicionar .**

    ![Configuração Ziflow adicionando utilizador](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. No **popup Adicionar um utilizador,** execute os seguintes passos:

    ![Configuração Ziflow adicionando utilizador](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Na caixa de texto **por e-mail,** introduza o e-mail do utilizador como brittasimon@contoso.com.

    b. Na caixa de texto **de primeiro nome,** introduza o primeiro nome de utilizador como Britta.

    c. Na caixa de texto **de apelido,** introduza o último nome de utilizador como Simon.

    d. Selecione a sua função Ziflow.

    e. Clique em **Adicionar 1 utilizador**.

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de se tornar ativo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Ziflow no Painel de Acesso, deve ser automaticamente inscrito no Ziflow para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

