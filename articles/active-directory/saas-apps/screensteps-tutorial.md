---
title: 'Tutorial: Integração do Azure Ative Directory com ScreenSteps Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o ScreenSteps.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 8f23f9763d25d5edfebd0e4cff7f3b7772ef1554
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543360"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integração do Diretório Ativo Azure com ScreenSteps

Neste tutorial, aprende-se a integrar o ScreenSteps com o Azure Ative Directory (Azure AD).
A integração de ScreenSteps com Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao ScreenSteps.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no ScreenSteps (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o ScreenSteps, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por ScreenSteps

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* ScreenSteps suporta **SSO** iniciado sp

## <a name="adding-screensteps-from-the-gallery"></a>Adicionando ScreenSteps da galeria

Para configurar a integração do ScreenSteps no AD Azure, é necessário adicionar ScreenSteps da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar ScreenSteps da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ScreenSteps**, selecione **ScreenSteps** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![ScreenSteps na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com ScreenSteps baseado num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no ScreenSteps.

Para configurar e testar o Azure AD com um único sinal de acesso com ScreenSteps, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar screenSteps single sign-on](#configure-screensteps-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create ScreenSteps test user](#create-screensteps-test-user)** - para ter uma contraparte de Britta Simon em ScreenSteps que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o ScreenSteps, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **ScreenSteps,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![ScreenSteps Domain e URLs informações únicas de súmis](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL Sign-On real, que é explicado mais tarde neste tutorial.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar ScreenSteps,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-screensteps-single-sign-on"></a>Configurar screenSteps single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da empresa ScreenSteps como administrador.

1. Clique em **Definições de Conta**.

    ![Gestão de contas](./media/screensteps-tutorial/ic778523.png "Gestão de contas")

1. Clique **em 'S sign-on' único**.

    ![Autenticação remota](./media/screensteps-tutorial/ic778524.png "Autenticação remota")

1. Clique **em Criar um ponto final único de sinal de assinatura.**

    ![Autenticação remota](./media/screensteps-tutorial/ic778525.png "Autenticação remota")

1. Na secção **'Criar ponto final único',** execute os seguintes passos:

    ![Criar um ponto final de autenticação](./media/screensteps-tutorial/ic778526.png "Criar um ponto final de autenticação")

    a. Na caixa de texto **do título,** digite um título.

    b. Na lista **de Modo,** selecione **SAML**.

    c. Clique em **Criar**.

1. **Editar** o novo ponto final.

    ![Editar ponto final](./media/screensteps-tutorial/ic778528.png "Editar ponto final")

1. Na secção **'Ponto final único' de edição,** execute os seguintes passos:

    ![Ponto final de autenticação remota](./media/screensteps-tutorial/ic778527.png "Ponto final de autenticação remota")

    a. Clique **em Carregar novo ficheiro de Certificado SAML**e, em seguida, faça o upload do certificado, que descarregou a partir do portal Azure.

    b. Cole o valor **URL do Login,** que copiou do portal Azure para a caixa de texto **URL do Início Remoto.**

    c. Cole o valor **URL logout,** que copiou do portal Azure para a caixa de texto **URL de Log out.**

    d. Selecione um **Grupo** para atribuir utilizadores quando estiverem a provisionados.

    e. Clique em **Atualizar**.

    f. Copie o **URL do consumidor SAML** para a prancheta e **cole-a** na caixa de texto URL de acesso em **Configuração Básica SAML** no portal Azure.

    exemplo, Voltar ao **ponto final de inscrição única de edição.**

    h. Clique no **botão 'Fazer predefinição' para** utilizar este ponto final para todos os utilizadores que iniciarem sessão no ScreenSteps. Em alternativa, pode clicar no botão **Adicionar ao Site para** utilizar este ponto final para sites específicos em **ScreenSteps**.

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

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso a ScreenSteps.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **ScreenSteps**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ScreenSteps**.

    ![O link ScreenSteps na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-screensteps-test-user"></a>Criar utilizador de teste ScreenSteps

Nesta secção, cria-se um utilizador chamado Britta Simon em ScreenSteps. Trabalhe com a [equipa de suporte do cliente ScreenSteps](https://www.screensteps.com/contact)para adicionar os   utilizadores na plataforma ScreenSteps. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ScreenSteps no Painel de Acesso, deverá ser automaticamente inscrito nos ScreenSteps para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)