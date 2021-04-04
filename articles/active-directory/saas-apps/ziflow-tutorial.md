---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Ziflow | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ziflow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e10ca98e0c9257187288d02483ed81915a7b321d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92894747"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Tutorial: Integração do Diretório Ativo Azure com o Ziflow

Neste tutorial, aprende-se a integrar o Ziflow com o Azure Ative Directory (Azure AD).
A integração do Ziflow com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Ziflow.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Ziflow (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Ziflow, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada única ziflow

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Ziflow suporta **SSO** iniciado sp

## <a name="adding-ziflow-from-the-gallery"></a>Adicionando Ziflow da galeria

Para configurar a integração do Ziflow no Azure AD, é necessário adicionar Ziflow da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Ziflow da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Ziflow,** selecione **Ziflow** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Ziflow na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sinal de entrada com Ziflow com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Ziflow.

Para configurar e testar o Azure AD com um único sinal de acesso com ziflow, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Ziflow Single Sign-On](#configure-ziflow-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Ziflow test user](#create-ziflow-test-user)** - para ter uma contraparte de Britta Simon em Ziflow que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com ziflow, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Ziflow,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Ziflow Domain e URLs informações únicas de entrada](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Os valores anteriores não são reais. Irá atualizar o valor de ID único no Identificador e Assinar no URL com valor real, que é explicado mais tarde no tutorial.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **De Ziflow Configurar,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-ziflow-single-sign-on"></a>Configure Ziflow Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no Ziflow como Administrador de Segurança.

2. Clique no Avatar no canto superior direito e, em seguida, clique em **Gerir a conta**.

    ![Gerir a configuração do Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. No superior esquerdo, clique em **'Sign-On' único.**

    ![Sinal de configuração ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Na página **'Sign-On' único,** execute os seguintes passos:

    ![Single de configuração ziflow](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Selecione **Tipo** como **SAML2.0**.

    b. Na caixa de texto **do Sign In URL,** cole o valor do URL de **login,** que copiou a partir do portal Azure.

    c. Faça o upload do certificado codificado base-64 que descarregou do portal Azure para o **Certificado de Assinatura X509**.

    d. Na caixa de texto **url sign out,** cole o valor do **URL logout,** que copiou a partir do portal Azure.

    e. A partir das **Definições de Configuração para a secção Fornecedor de Identificação,** copie o valor de ID único destacado e adicione-o ao Identificador e Assine no URL na **Configuração Básica SAML** no portal Azure.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** brittasimon@yourcompanydomain.extension . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Ziflow.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Ziflow**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Ziflow**.

    ![O link Ziflow na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-ziflow-test-user"></a>Criar utilizador de teste ziflow

Para permitir que os utilizadores de Azure AD inscrevam-se no Ziflow, devem ser a provisionados no Ziflow. Em Ziflow, o provisionamento é uma tarefa manual.

Para obter uma conta de utilizador, execute os seguintes passos:

1. Inscreva-se no Ziflow como Administrador de Segurança.

2. Navegue para **as pessoas** no topo.

    ![Pessoas de configuração ziflow](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Clique **em Adicionar** e, em seguida, clique em Adicionar **utilizador**.

    ![A screenshot mostra a opção de utilizador Adicionar selecionada.](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. No **Popup Add um utilizador,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo do utilizador Adicionar onde pode introduzir os valores descritos.](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como brittasimon@contoso.com .

    b. Na caixa de texto **do primeiro nome,** insira o primeiro nome de utilizador como Britta.

    c. Na caixa de texto **do último nome,** insira o último nome de utilizador como Simon.

    d. Selecione a sua função Ziflow.

    e. Clique **em Adicionar 1 utilizador.**

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de ficar ativa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ziflow no Painel de Acesso, deverá ser automaticamente inscrito no Ziflow para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)