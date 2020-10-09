---
title: 'Tutorial: Integração do Azure Ative Directory com o Rally Software Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Rally Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 542d330138885a997a0e4a335272492116686a65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88548905"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Integração do Azure Ative Directory com o Rally Software

Neste tutorial, aprende-se a integrar o Rally Software com o Azure Ative Directory (Azure AD).
A integração do Software Rally com a AZure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Rally Software.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Rally Software (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Rally Software, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada pelo Rally Software

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Rally Software suporta **SSO** iniciado SP

## <a name="adding-rally-software-from-the-gallery"></a>Adicionar Software de Rali da galeria

Para configurar a integração do Rally Software no Azure AD, é necessário adicionar o Rally Software da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Software Rally da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Rally Software**, selecione **Rally Software** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Rally Software na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com o Rally Software baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Rally Software.

Para configurar e testar o Azure AD com um único sign-on com o Rally Software, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o software de rali single sign-on](#configure-rally-software-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Rally Software test user](#create-rally-software-test-user)** - para ter uma contraparte de Britta Simon em Rally Software que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Rally Software, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **do Rally Software,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Rally Software Domain e URLs informações únicas de acesso](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<tenant-name>.rally.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<tenant-name>.rally.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente do Rally Software](https://help.rallydev.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção Configurar o **Software de Rali,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-rally-software-single-sign-on"></a>Configurar o software de rali single Sign-On

1. Inscreva-se no seu inquilino **de Rally Software.**

2. Na barra de ferramentas na parte superior, clique em **Configuração**e, em seguida, **selecione Subscrição**.
   
    ![Subscrição](./media/rally-software-tutorial/ic769531.png "Subscrição")

3. Clique no botão **Ação.** **Selecione Editar a Subscrição** no lado superior direito da barra de ferramentas.

4. Na página de diálogo de **assinatura,** execute os seguintes passos e, em seguida, clique em **Guardar & Fechar**:
   
    ![Autenticação](./media/rally-software-tutorial/ic769542.png "Autenticação")
   
    a. Selecione **a autenticação rally ou SSO** a partir do dropdown de autenticação.

    b. Na caixa de texto **URL do fornecedor de identidade,** cole o valor do **Identificador AD Azure,** que copiou do portal Azure. 

    c. Na caixa de texto **SSO Logout,** cole o valor do **URL logout,** que copiou do portal Azure.

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

Nesta secção, você permite que Britta Simon use a Azure single sign-on, concedendo acesso ao Rally Software.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Rally Software**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Rally Software**.

    ![O link de Software Rally na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-rally-software-test-user"></a>Criar utilizador de teste de Software De Rali

Para que os utilizadores de Azure AD possam iniciar sação, devem ser adu eles à aplicação Rally Software utilizando os seus nomes de utilizadores do Azure Ative Directory.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino de Rally Software.

2. Vá a **Configurar \> UTILIZADORES**e, em seguida, clique **em + Adicionar Novo**.
   
    ![Utilizadores](./media/rally-software-tutorial/ic781039.png "Utilizadores")

3. Digite o nome na caixa de texto do Novo Utilizador e, em seguida, clique em **Adicionar com Detalhes**.

4. Na secção **Criar Utilizador,** execute os seguintes passos:
   
    ![Criar Utilizador](./media/rally-software-tutorial/ic781040.png "Criar Utilizador")

    a. Na caixa de texto **do Nome do Utilizador,** digite o nome do utilizador como **Brittsimon**.
   
    b. Na caixa de texto **do endereço de e-mail,** insira o e-mail do utilizador como brittasimon@contoso.com .

    c. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como **Britta**.

    d. Na caixa de texto **Last Name,** insira o último nome do utilizador como **Simon**.

    e. Clique em **Save & Close** (Guardar e Fechar).

   >[!NOTE]
   >Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Rally Software ou APIs fornecidas pela Rally Software para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Software Rally no Painel de Acesso, deverá ser automaticamente inscrito no Rally Software para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

