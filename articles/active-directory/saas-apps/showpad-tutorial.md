---
title: 'Tutorial: Integração do Azure Ative Directory com showpad | Microsoft Docs'
description: Saiba como configurar um único sinal de acesso entre o Azure Ative Directory e o Showpad.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 314c9d6629c74aa7b593d24d503d195b3401f4e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522143"
---
# <a name="tutorial-azure-active-directory-integration-with-showpad"></a>Tutorial: Integração do Azure Ative Directory com Showpad

Neste tutorial, aprende-se a integrar o Showpad com o Azure Ative Directory (Azure AD).
A integração do Showpad com a AZure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Showpad.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Showpad (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Showpad, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada por showpad

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Showpad suporta **SSO** iniciado SP
* Showpad suporta **provisão do** utilizador Just In Time

## <a name="adding-showpad-from-the-gallery"></a>Adicionar Showpad da galeria

Para configurar a integração do Showpad no Azure AD, é necessário adicionar o Showpad da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o Showpad da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Showpad**, selecione **Showpad** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Showpad na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com Showpad baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Showpad.

Para configurar e testar o Azure AD com um único sinal de acesso com o Showpad, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure showpad Single Sign-On](#configure-showpad-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Showpad test user](#create-showpad-test-user)** - para ter uma contraparte de Britta Simon no Showpad que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com um único sign-on com o Showpad, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Showpad,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Showpad Domain e URLs informações únicas de acesso](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<comapany-name>.showpad.biz/login`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<company-name>.showpad.biz`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Do Showpad](https://help.showpad.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **'Configurar' Showpad,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-showpad-single-sign-on"></a>Configurar showpad single Sign-On

1. Inscreva-se no seu inquilino showpad como administrador.

1. No menu em cima, clique nas **Definições**.

    ![A screenshot mostra definições selecionadas no menu Definições.](./media/showpad-tutorial/tutorial_showpad_001.png)

1. Navegue para **um único sign-on** e clique **em Enable**.

    ![A screenshot mostra single Sign-On selecionados com uma opção Enable.](./media/showpad-tutorial/tutorial_showpad_002.png)

1. No **diálogo de serviço Add a SAML 2.0,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo de serviço SAML 2.0 ONDE pode introduzir os valores descritos.](./media/showpad-tutorial/tutorial_showpad_003.png) 

    a. Na caixa de texto **Name,** digite o nome de Fornecedor de Identificadores (por exemplo: o nome da sua empresa).

    b. Como **Fonte de Metadados,** selecione **XML**.

    c. Copie o conteúdo do ficheiro XML dos metadados, que descarregou a partir do portal Azure e, em seguida, cole-o na caixa de texto **Metadados XML.**

    d. Selecione **contas de provisão automática para novos utilizadores quando iniciarem sessão**.

    e. Clique **em Submeter.**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Showpad.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Showpad**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Showpad**.

    ![O link Showpad na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-showpad-test-user"></a>Criar utilizador de teste showpad

Nesta secção, um utilizador chamado Britta Simon é criado no Showpad. O Showpad suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Showpad, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Showpad no Painel de Acesso, deverá ser automaticamente inscrito no Showpad para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)