---
title: 'Tutorial: Integração do Azure Ative Directory com o QuickHelp Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o QuickHelp.
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
ms.openlocfilehash: 55ab6b339d6cb71bf46340e458bcc93cd73e2ba9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108665"
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Tutorial: Integração do Azure Ative Directory com QuickHelp

Neste tutorial, aprende-se a integrar o QuickHelp com o Azure Ative Directory (Azure AD).
A integração do QuickHelp com a Ad Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao QuickHelp.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no QuickHelp (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o QuickHelp, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada única QuickHelp

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* QuickHelp suporta **SSO** iniciado sp

* QuickHelp suporta **o fornecimento de utilizadores just in time**

## <a name="adding-quickhelp-from-the-gallery"></a>Adicionando QuickHelp da galeria

Para configurar a integração do QuickHelp no AD Azure, precisa adicionar QuickHelp da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar QuickHelp da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **QuickHelp,** selecione **QuickHelp** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![QuickHelp na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com quickHelp com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no QuickHelp.

Para configurar e testar o Azure AD com quickHelp, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o QuickHelp Single Sign-On](#configure-quickhelp-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create QuickHelp test user](#create-quickhelp-test-user)** - para ter uma contraparte de Britta Simon em QuickHelp que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com quickHelp, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **QuickHelp,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações únicas de acesso ao domínio quickHelp e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://quickhelp.com/<ROUTEURL>`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL: `https://auth.quickhelp.com`

    > [!NOTE]
    > O valor url de inscrição não é real. Atualize o valor com o URL de Sign-On real. Contacte o administrador quickHelp da sua organização ou o seu Gestor de Sucesso do Cliente BrainStorm para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar QuickHelp,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-quickhelp-single-sign-on"></a>Configurar quickhelp single Sign-On

1. Inscreva-se no site da empresa QuickHelp como administrador.

2. No menu em cima, clique em **Administração.**
   
    ![A screenshot mostra o item do menu Admin para Brainstorm.][21]

3. No menu **QuickHelp Admin,** clique em **Definições**.
   
    ![A screenshot mostra definições selecionadas a partir do menu QuickHelp Admin.][22]

4. Clique em **Definições de Autenticação**.

5. Na página **Definições de Autenticação,** execute os seguintes passos
   
    ![A screenshot mostra a página Definições de Autenticação onde pode introduzir os valores descritos.][23]
   
    a. Como **Tipo SSO,** selecione **WSFederation**.
   
    b. Para fazer o upload do seu ficheiro de metadados Azure descarregado, clique em **Procurar,** navegue para o ficheiro, termine e clique em **Upload Metadas**.
   
    c. Na caixa de texto **por e-mail,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
   
    d. Na caixa de texto **do Primeiro Nome,** `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .
   
    e. Na caixa de texto **do último nome,** `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .
   
    f. Na **Barra de Ação,** clique **em Guardar**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao QuickHelp.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **QuickHelp**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **QuickHelp**.

    ![O link QuickHelp na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-quickhelp-test-user"></a>Criar utilizador de teste QuickHelp

Nesta secção, um utilizador chamado Britta Simon é criado em QuickHelp. O QuickHelp suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no QuickHelp, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo QuickHelp no Painel de Acesso, deverá ser automaticamente inscrito no QuickHelp para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[21]: ./media/quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/quickhelp-tutorial/tutorial_quickhelp_07.png