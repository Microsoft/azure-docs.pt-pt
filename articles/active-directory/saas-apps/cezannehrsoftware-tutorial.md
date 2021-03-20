---
title: 'Tutorial: Integração do Azure Ative Directory com a Cezanne HR Software | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Software Rh cezanne.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 0aa0dab7b512c85fbbdf374c962e6ee8e1c7d616
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456295"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integração do Azure Ative Directory com o Software RH cezanne

Neste tutorial, aprende-se a integrar o Software CEzanne HR com o Azure Ative Directory (Azure AD).
A integração do Software RH cezanne com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Software RH Cezanne.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Software CEzanne HR (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Cezanne HR Software, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Cezanne HR Software assinatura ativada única

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Cezanne HR Software suporta **SSO** iniciado SP

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adicionar software Cezanne HR da galeria

Para configurar a integração do Software RH cezanne em AZure AD, você precisa adicionar o Software Rh Cezanne da galeria à sua lista de aplicações geridas saaS.

**Para adicionar o Software CEzanne HR da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite o Software CEzanne HR**, selecione **o Software CEzanne HR** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Software RH Cezanne na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com o Software RH Cezanne baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Software RH Cezanne.

Para configurar e testar o Azure AD com um único sinal de acesso com o Software RH Cezanne, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o software cezanne HR single sign-on](#configure-cezanne-hr-software-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Cezanne HR Software test user](#create-cezanne-hr-software-test-user)** - para ter uma contraparte de Britta Simon em Cezanne HR Software que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Software CEzanne HR, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **do Software Cezanne HR,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Cezanne HR Software Domain e URLs informações únicas de acesso](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Na caixa de texto **identifier (Entity ID),** digite o URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e URL de resposta Sign-On reais. Contacte a [equipa de suporte do Cliente do Cezanne HR Software](https://cezannehr.com/services/support/) para obter estes valores.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção Configurar o **Software Cezanne HR,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Configurar software cezanne HR single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino de Software Cezanne HR como administrador.

2. No painel de navegação esquerdo, clique na **configuração do sistema**. Ir para **definições de segurança**. Em seguida, navegue para a **Configuração Sign-On Única**.

    ![O Screenshot mostra o inquilino de Software Cezanne H R com definições de segurança e configuração de Sign-On única selecionada.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. No **Painel de Serviço De Sign-On Único (SSO),** verifique a caixa **SAML 2.0** e selecione a opção **Configuração Avançada.**

    ![A screenshot mostra o painel de permitir que os utilizadores se despeçam com SAML 2.0 e Configuração Avançada selecionada.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Clique **em Adicionar Novo** botão.

    ![A screenshot mostra o botão Add New.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Execute os seguintes passos na secção **SAML 2.0 IDENTITY PROVIDERS.**

    ![A screenshot mostra um painel onde pode introduzir os valores descritos neste passo.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Insira o nome do seu Fornecedor de Identidade como **Nome de Exibição**.

    b. Na caixa de texto do **Identificador de Entidade,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure.

    c. Altere a **Ligação SAML** para 'POST'.

    d. Na caixa de texto **do Serviço De Segurança Token Endpoint,** cole o valor do URL de **login** que copiou a partir do portal Azure.

    e. Na caixa de texto do nome do atributo do utilizador, introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    f. Clique no ícone **upload** para carregar o certificado descarregado a partir do portal Azure.

    exemplo, Clique no botão **OK**.

6. Clique no botão **Guardar.**

    ![A screenshot mostra o botão Guardar para a configuração de um único sinal de inscrição.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao Software RH Cezanne.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Cezanne HR Software**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Cezanne HR Software**.

    ![A ligação de Software Rh Cezanne na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-cezanne-hr-software-test-user"></a>Criar utilizador de teste de software de RH cezanne

Para permitir que os utilizadores de Azure AD acedam ao Software Rh cezanne, devem ser ateados no Software Rh cezanne. No caso da Cezanne HR Software, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu site da empresa Cezanne HR Software como administrador.

2. No painel de navegação esquerdo, clique na **configuração do sistema**. Ir para **Gerir utilizadores**. Em seguida, navegue para **adicionar novo utilizador**.

    ![O Screenshot mostra o inquilino do Software Cezanne H R com utilizadores de gestão e adicionar novo utilizador selecionado.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Novo Utilizador")

3. Na secção **DETALHES PESSOAIS,** execute abaixo dos passos:

    ![A screenshot mostra a secção DETALHES PESSOA ONDE pode introduzir os valores descritos neste passo.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Novo Utilizador")

    a. Desa parte **do utilizador interno.**

    b. Na caixa de texto **Name Name,** digite o Primeiro Nome de um utilizador como **Britta**.  

    c. Na caixa de texto **Do Último Nome,** digite o último nome do utilizador como **Simon**.

    d. Na **caixa de texto do e-mail,** digite o endereço de e-mail do utilizador como Brittasimon@contoso.com .

4. Na secção **informações sobre contas,** execute abaixo dos passos:

    ![A screenshot mostra informações de conta onde pode introduzir os valores descritos neste passo.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Novo Utilizador")

    a. Na caixa de texto **username,** escreva o e-mail do utilizador como Brittasimon@contoso.com .

    b. Na caixa de texto **da palavra-passe,** digite a palavra-passe do utilizador.

    c. Selecione **HR Professional** como **Função de Segurança**.

    d. Clique em **OK**.

5. Navegue para **o separador 'Sign-on' único** e selecione **Adicionar Novo** na área dos **identificadores SAML 2.0.**

    ![A screenshot mostra o separador Single Sign-On onde pode selecionar Add New.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "User")

6. Escolha o seu Fornecedor de Identidade para o **Fornecedor de Identidade** e na caixa de texto do User **Identifier,** insira o endereço de e-mail da conta Britta Simon.

    ![A screenshot mostra os identificadores SAML 2.0 onde pode selecionar o seu Fornecedor de Identidade e Identificador de Utilizador.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "User")

7. Clique no botão **Guardar.**

    ![A screenshot mostra o botão Guardar para as Definições do Utilizador.](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "User")

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Software RH cezanne no Painel de Acesso, deverá ser automaticamente inscrito no Software RH cezanne para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)