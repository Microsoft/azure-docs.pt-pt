---
title: 'Tutorial: Integração do Diretório Ativo Azure com clarizen [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b34e6087f3a6e7f3cc0f46c53d4fe903838d210
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158594"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Tutorial: Integração do Diretório Ativo Azure com clarizen

Neste tutorial, aprende-se a integrar o Clarizen com o Azure Ative Directory (Azure AD).
Integrar o Clarizen com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Clarizen.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Clarizen (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Clarizen, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de assinatura de assinatura única clarizen

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Clarizen suporta **IDP** iniciado SSO

## <a name="adding-clarizen-from-the-gallery"></a>Adicionando Clarizen da galeria

Para configurar a integração do Clarizen em Azure AD, você precisa adicionar Clarizen da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Clarizen da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Clarizen**, selecione **Clarizen** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Clarizen na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com clarizen com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Clarizen.

Para configurar e testar o único sinal de Azure AD com o Clarizen, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure clarizen single sign-on](#configure-clarizen-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador de teste Clarizen](#create-clarizen-test-user)** - para ter uma contrapartida de Britta Simon em Clarizen que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o Clarizen, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Clarizen,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na configuração de um único sign-on com a página **SAML,** execute os seguintes passos:

    ![Clarizen Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **identificador,** digite um valor:`Clarizen`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > Estes não são os valores reais. Tem de usar o identificador real e responder URL. Aqui sugerimos que use o valor único de uma corda como identificador. Para obter os valores reais, contacte a equipa de [apoio clarizen.](https://success.clarizen.com/hc/en-us/requests/new)

4. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar clarizen,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-clarizen-single-sign-on"></a>Configure Clarizen Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Clarizen como administrador.

1. Clique no seu nome de utilizador e, em seguida, clique em **Definições**.

    ![Clicar em "Definições" sob o seu nome de utilizador](./media/clarizen-tutorial/tutorial_clarizen_001.png "Definições")

1. Clique no separador **Definições Globais.** Em seguida, ao lado da **Autenticação Federada,** clique em **editar**.

    ![Separador "Definições Globais"](./media/clarizen-tutorial/tutorial_clarizen_002.png "Definições Globais")

1. Na caixa de diálogo de **autenticação federada,** execute os seguintes passos:

    ![Caixa de diálogo "Autenticação Federada"](./media/clarizen-tutorial/tutorial_clarizen_003.png "Autenticação Federada")

    a. **Selecione ativar a autenticação federada**.

    b. Clique em **Carregar** para fazer o upload do seu certificado descarregado.

    c. Na caixa URL de **entrada,** introduza o valor do URL de **Login** a partir da janela de configuração da aplicação Azure AD.

    d. Na caixa **URL sign-out,** introduza o valor do URL de **Logout** a partir da janela de configuração da aplicação Azure AD.

    e. Selecione **Use POST**.

    f. Clique em **Guardar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Clarizen.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Clarizen**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Clarizen**.

    ![A ligação Clarizen na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-clarizen-test-user"></a>Criar o utilizador de teste Clarizen

O objetivo desta secção é criar uma utilizadora chamada Britta Simon em Clarizen.

**Se necessitar de criar manualmente o utilizador, execute os seguintes passos:**

Para permitir que os utilizadores de Anúncios Azure entrem no Clarizen, deve fornecer contas de utilizador. No caso do Clarizen, o provisionamento é uma tarefa manual.

1. Inscreva-se no site da sua empresa Clarizen como administrador.

2. Clique em **Pessoas**.

    ![Clicando em "Pessoas"](./media/clarizen-tutorial/create_aaduser_001.png "People")

3. Clique em **Convidar utilizador**.

    ![Botão "Convidar utilizador"](./media/clarizen-tutorial/create_aaduser_002.png "Convidar utilizadores")

1. Na caixa de diálogo **Pessoas Convidadas,** execute os seguintes passos:

    ![Caixa de diálogo "Convidar pessoas"](./media/clarizen-tutorial/create_aaduser_003.png "Convidar pessoas")

    a. Na caixa de **e-mail,** digite o endereço de e-mail da conta Britta Simon.

    b. Clique em **Convidar**.

    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de se tornar ativo.


### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Clarizen no Painel de Acesso, deve ser automaticamente inscrito no Clarizen para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
