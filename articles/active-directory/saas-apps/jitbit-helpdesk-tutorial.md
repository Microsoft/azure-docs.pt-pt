---
title: 'Tutorial: Integração do Azure Ative Directory com o Jitbit Helpdesk Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jitbit Helpdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: bd47a528b6c9aef354df8e52da63409002f6fb32
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850800"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integração do Diretório Ativo Azure com Jitbit Helpdesk

Neste tutorial, você aprende a integrar Jitbit Helpdesk com Azure Ative Directory (Azure AD).
A integração do Jitbit Helpdesk com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Jitbit Helpdesk.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Jitbit Helpdesk (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Jitbit Helpdesk, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Jitbit Helpdesk única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Jitbit Helpdesk suporta **SP** iniciado SSO

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Adicionando Jitbit Helpdesk da galeria

Para configurar a integração do Jitbit Helpdesk no AD Azure, precisa adicionar Jitbit Helpdesk da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Jitbit Helpdesk da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Jitbit Helpdesk,** selecione **Jitbit Helpdesk** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Jitbit Helpdesk na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Jitbit Helpdesk com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Jitbit Helpdesk.

Para configurar e testar o Azure AD com o Jitbit Helpdesk, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Igigure Jitbit Helpdesk Single Sign-On](#configure-jitbit-helpdesk-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Jitbit Helpdesk test user](#create-jitbit-helpdesk-test-user)** - para ter uma contraparte de Britta Simon em Jitbit Helpdesk que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Jitbit Helpdesk, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Jitbit Helpdesk,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Jitbit Helpdesk Domain e URLs informações únicas de súrbio](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL Sign-On real. Contacte [a equipa de suporte do cliente Jitbit Helpdesk](https://www.jitbit.com/support/) para obter este valor.

    b. Na caixa de texto **identifier (Entity ID),** escreva um URL como seguinte: `https://www.jitbit.com/web-helpdesk/`

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Jitbit Helpdesk,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Configure Jitbit Helpdesk Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Jitbit Helpdesk como administrador.

1. Na barra de ferramentas em cima, clique em **Administração**.

    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administração")

1. Clique **nas definições gerais**.

    ![A screenshot mostra a ligação Configurações Gerais.](./media/jitbit-helpdesk-tutorial/ic777680.png "Utilizadores, empresas e permissões")

1. Na secção de configuração de **configuração de definições de autenticação,** execute os seguintes passos:

    ![Definições de autenticação](./media/jitbit-helpdesk-tutorial/ic777683.png "Definições de autenticação")

    a. **Selecione Ativar o sinal único SAML 2.0**, para iniciar sinsução utilizando Sign-On único (SSO), com **OneLogin**.

    b. Na caixa de texto **url do EndPoint,** cole o valor do URL de **login** que copiou do portal Azure.

    c. Abra o certificado codificado **base-64** no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado X.509**

    d. Clique **em Guardar as alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Jitbit Helpdesk.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Jitbit Helpdesk**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Jitbit Helpdesk**.

    ![O link Jitbit Helpdesk na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-jitbit-helpdesk-test-user"></a>Criar utilizador de teste Jitbit Helpdesk

Para permitir que os utilizadores de Azure AD inscrevam-se no Jitbit Helpdesk, devem ser a provisionados no Jitbit Helpdesk. No caso do Jitbit Helpdesk, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **Jitbit Helpdesk.**

1. No menu em cima, clique em **Administração.**

    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administração")

1. Clique em **Utilizadores, empresas e permissões.**

    ![Utilizadores, empresas e permissões](./media/jitbit-helpdesk-tutorial/ic777682.png "Utilizadores, empresas e permissões")

1. Clique **em Adicionar utilizador**.

    ![Adicionar utilizador](./media/jitbit-helpdesk-tutorial/ic777685.png "Adicionar utilizador")

1. Na secção Criar, digite os dados da conta Azure AD que pretende obter da seguinte forma:

    ![Criar](./media/jitbit-helpdesk-tutorial/ic777686.png "Criar")

   a. Na caixa de texto **username,** digite o nome de utilizador do utilizador como **BrittaSimon**.

   b. Na caixa de texto **por e-mail,** escreva e-mail do utilizador como **BrittaSimon@contoso.com** .

   c. Na caixa de texto **Name Name,** escreva o primeiro nome do utilizador como **Britta**.

   d. Na caixa de texto **Do Último Nome,** escreva o apelido do utilizador como **Simon**.

   e. Clique em **Criar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Jitbit Helpdesk ou APIs fornecidas pela Jitbit Helpdesk para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Jitbit Helpdesk no Painel de Acesso, deverá ser automaticamente inscrito no Jitbit Helpdesk para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
