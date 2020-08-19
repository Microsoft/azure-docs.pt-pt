---
title: 'Tutorial: Integração do Azure Ative Directory com n2F - Relatórios de despesas Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o N2F - Relatórios de despesas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 6036ff86c430422556ed4f7e1bc80fe122ed0a30
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552570"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Tutorial: Integração do Diretório Ativo Azure com N2F - Relatórios de despesas

Neste tutorial, aprende-se a integrar n2F - Relatórios de despesas com o Azure Ative Directory (Azure AD).
Integração N2F - Relatórios de despesas com a Azure AD fornece-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a N2F - Relatórios de despesas.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no N2F - Relatórios de despesas (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com n2F - Relatórios de despesas, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* N2F - Relatórios de despesas de assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* N2F - Relatórios de despesas **suportam SP** e **IDP** iniciado SSO

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Adicionar N2F - Relatórios de despesas da galeria

Para configurar a integração de N2F - Relatórios de despesas em AZure AD, você precisa adicionar N2F - Relatórios de despesas da galeria à sua lista de aplicações geridas saaS.

**Para adicionar N2F - Relatórios de despesas da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, tipo **N2F - Relatórios de despesas**, selecione **N2F - Relatórios** de despesas do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

     ![N2F - Relatórios de despesas na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com N2F - Relatórios de despesas baseados num utilizador de teste chamado **Britta Simon**.
Para um único sign-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em N2F - Relatórios de despesas.

Para configurar e testar o Azure AD com relatórios de despesas N2F - You need to complete os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure N2F - Relatórios de despesas Único Sinal-On](#configure-n2f---expense-reports-single-sign-on)** - para configurar as definições de Sign-On único no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create N2F - Relatórios de despesas utilizador](#create-n2f---expense-reports-test-user)** - para ter uma contraparte de Britta Simon em N2F - Relatórios de despesas que estão ligados à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de inscrição com N2F - Relatórios de despesas, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de **pedidos de relatórios de despesas N2F** , selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração de 'Sessão única' com** a página SAML, clique em **Editar** o ícone para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **Configuração Básica saml,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** o utilizador não tem de realizar quaisquer passos, uma vez que a aplicação já está pré-integrada com o Azure.

    ![N2F - Relatórios de despesas Informações de domínio e URLs únicas informações de súmis](common/preintegrated.png)

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![N2F - Relatórios de despesas Informações de domínio e URLs únicas informações de súmis](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://www.n2f.com/app/`

6. Na **configuração de um único sinal com página SAML,** na secção certificado de assinatura **SAML,** clique no botão de cópia para copiar o **Url de metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

7. Na secção **Configuração myPolicies,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Configure N2F - Relatórios de despesas único sinal-on

1. Numa janela diferente do navegador web, inscreva-se no seu N2F - Relatórios de despesas site da empresa como administrador.

2. Clique em **Definições** e, em seguida, selecione **Definições de Avanço** a partir do dropdown.

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecione **separador definições de conta.**

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure2.png)

4. Selecione **Autenticação** e, em seguida, **selecione + Adicione um separador de método de autenticação.**

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure3.png)

5. Selecione **SAML Microsoft Office 365** como método de autenticação.

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure4.png)

6. Na secção **método de autenticação,** execute os seguintes passos:

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure5.png)

    a. Na caixa de texto **ID** da Entidade, cole o valor do **identificador Azure AD,** que copiou a partir do portal Azure.

    b. Na caixa de texto **URL dos metadados,** cole o valor do **url da Federação de Metadados** da App, que copiou a partir do portal Azure.

    c. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a N2F - Relatórios de despesas.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **N2F - Relatórios de despesas**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **N2F - Relatórios de despesas.**

    ![O N2F - Ligação de relatórios de despesas na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-n2f---expense-reports-test-user"></a>Criar N2F - Relatórios de despesas testam utilizador

Para permitir que os utilizadores da Azure AD iniciem sessão no N2F - Relatórios de despesas, devem ser a provisionados em N2F - Relatórios de despesas. No caso da N2F - Relatórios de despesas, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu N2F - Relatórios de despesas site da empresa como administrador.

2. Clique em **Definições** e, em seguida, selecione **Definições de Avanço** a partir do dropdown.

    ![N2F - Despesa Adicionar utilizador](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecione o separador **Utilizadores** do painel de navegação esquerdo.

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/user1.png)

4. Selecione + Novo separador **de utilizador.**

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/user2.png)

5. Na secção **Utilizador,** execute os seguintes passos:

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/user3.png)

    a. Na caixa de texto **do endereço de e-mail,** insira o endereço de e-mail do utilizador como ** \@ brittasimon contoso.com**.

    b. Na caixa de texto do **primeiro nome,** insira o primeiro nome de utilizador como **Britta**.

    c. Na caixa de texto **Name,** insira o nome de utilizador como **BrittaSimon**.

    d. Escolha **Fun, Direct manager (N+1)** e **Divisão** de acordo com o seu requisito de organização.

    e. Clique **em Validar e enviar convite.**

    > [!NOTE]
    > Se estiver a enfrentar algum problema ao adicionar o utilizador, contacte a [equipa de suporte de relatórios de despesas N2F - Despesas](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no N2F - Relatórios de despesas no Painel de Acesso, deverá ser automaticamente inscrito no N2F - Relatórios de despesas para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

