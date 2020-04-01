---
title: 'Tutorial: Integração do Diretório Ativo Azure com rollbar [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Rollbar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: d76f4e9d61d8fd210fe9332084f9f44d19e54eed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092684"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Tutorial: Integração do Diretório Ativo Azure com rollbar

Neste tutorial, aprende-se a integrar o Rollbar com o Azure Ative Directory (Azure AD).
Integrar rollbar com Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Rollbar.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Rollbar (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Rollbar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada por sinal único rollbar

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Rollbar suporta **SP e IDP** iniciadoS SSO

## <a name="adding-rollbar-from-the-gallery"></a>Adicionando Rollbar da galeria

Para configurar a integração do Rollbar em Azure AD, precisa de adicionar rollbar da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Rollbar da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **rollbar**, **selecione Rollbar** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Rollbar na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com rollbar com base num utilizador de teste chamado **Britta Simon**.
Para que o início de sessão simples funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Rollbar.

Para configurar e testar o único sinal de Azure AD com rollbar, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o sinal único](#configure-rollbar-single-sign-on)** da barra de rollbar - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Rollbar test user](#create-rollbar-test-user)** - para ter uma contrapartida de Britta Simon em Rollbar que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com rollbar, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Rollbar,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Rollbar Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **identificador,** digite o URL:`https://saml.rollbar.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://rollbar.com/<accountname>/saml/sso/azure/`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Rollbar Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://rollbar.com/<accountname>/saml/login/azure/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Resposta real e URL de Sinal. Contacte a equipa de suporte ao [Cliente Rollbar](mailto:support@rollbar.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

7. Na secção **'Rollbar' configurar,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-rollbar-single-sign-on"></a>Configure Rollbar Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Rollbar como administrador.

1. Clique nas **Definições** de Perfil no canto superior direito e, em seguida, clique em **definições**de Nome de Conta .

    ![Configuração](./media/rollbar-tutorial/general.png)

1. Clique em **Fornecedor de Identidade** em SEGURANÇA.

    ![Configuração](./media/rollbar-tutorial/configure1.png)

1. Na secção **SAML Identity Provider,** execute os seguintes passos:

    ![Configuração](./media/rollbar-tutorial/configure2.png)

    a. Selecione **AZURE** do Fornecedor de **Identidade SAML.**

    b. Abra o ficheiro de metadados no bloco de notas, copie o conteúdo do mesmo na sua área de redação e, em seguida, cole-o na caixa de texto **dos Metadados SAML.**

    c. Clique em **Guardar**.

1. Depois de clicar no botão de salvamento, o ecrã será assim:

    ![Configuração](./media/rollbar-tutorial/configure3.png)

    > [!NOTE]
    > Para completar o seguinte passo, primeiro deve adicionar-se como utilizador à aplicação Rollbar em Azure.
    >

    a. Se pretender exigir que todos os utilizadores autentiquem via Azure, clique em **iniciar sessão através** do seu fornecedor de identidade para se reautenticar via Azure.  

    b.  Assim que for devolvido ao ecrã, selecione o **login Require via SAML Identity Provider.**

    b. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Rollbar.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Rollbar**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Rollbar**.

    ![O link Rollbar na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-rollbar-test-user"></a>Criar o utilizador de teste Rollbar

Para permitir que os utilizadores da AD Azure assinem o Rollbar, devem ser aprovisionados no Rollbar. No caso do Rollbar, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa Rollbar como administrador.

1. Clique nas **Definições** de Perfil no canto superior direito e, em seguida, clique em **definições**de Nome de Conta .

    ![Utilizador](./media/rollbar-tutorial/general.png)

1. Clique em **Utilizadores**.

    ![Adicionar Empregado](./media/rollbar-tutorial/user1.png)

1. Clique **em convidar membros da equipa**.

    ![Convidar pessoas](./media/rollbar-tutorial/user2.png)

1. Na caixa de texto, introduza o nome do utilizador como **brittasimon\@contoso.com** e clique **Em Adicionar/Convidar**.

    ![Convidar pessoas](./media/rollbar-tutorial/user3.png)

1. O utilizador recebe um convite e depois de aceitá-lo são criados no sistema.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Rollbar no Painel de Acesso, deve ser automaticamente inscrito no Rollbar para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

