---
title: 'Tutorial: Integração do Azure Ative Directory com o UserEcho Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o UserEcho.
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
ms.openlocfilehash: 2c6e09405fbc2fec6da516cabc7f509de712c45d
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487216"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Tutorial: Integração do Diretório Ativo Azure com o UserEcho

Neste tutorial, aprende-se a integrar o UserEcho com o Azure Ative Directory (Azure AD).
A integração do UserEcho com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao UserEcho.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no UserEcho (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o UserEcho, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada pelo UserEcho

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* UserEcho suporta SSO iniciado **pela SP**

## <a name="adding-userecho-from-the-gallery"></a>Adicionar UserEcho da galeria

Para configurar a integração do UserEcho no AD Azure, é necessário adicionar o UserEcho da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar UserEcho da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite UserEcho**, selecione **UserEcho** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![UserEcho na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o UserEcho com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no UserEcho.

Para configurar e testar o Azure AD com o UserEcho, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único do UserEcho](#configure-userecho-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create UserEcho test user](#create-userecho-test-user)** - para ter uma contraparte de Britta Simon no UserEcho que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o UserEcho, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **UserEcho,** selecione **'Único s-on'.**

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmis de dados únicos do UserEcho Domain e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.userecho.com/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente UserEcho](https://feedback.userecho.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar UserEcho,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-userecho-single-sign-on"></a>Configurar userEcho Single Sign-On

1. Em outra janela do navegador, inscreva-se no site da empresa UserEcho como administrador.

2. Na barra de ferramentas no topo, clique no nome de utilizador para expandir o menu e, em seguida, clique em **Configuração**.
   
    ![A screenshot mostra a configuração selecionada a partir do site UserEcho.](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Clique **em Integrações**.
   
    ![O Screenshot mostra integrações selecionadas no menu Definições.](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Clique **no Site**e, em seguida, clique em **'S.' Single sign-on (SAML2)**.
   
    ![A screenshot mostra um ÚNICO SAML2 de sinalização selecionado a partir do menu Integrações.](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Na página **single sign-on (SAML),** execute os seguintes passos:
   
    ![A screenshot mostra a página SAML de assinatura única onde pode introduzir os valores descritos.](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Como **ativado pelo SAML,** selecione **Sim**.
    
    b. Colar **URL de login,** que copiou do portal Azure para a caixa de texto **URL SSO SAML.**
    
    c. Pasta **URL logout**, que copiou do portal Azure para a caixa de texto **URL de logout remoto.**
    
    d. Abra o seu certificado descarregado no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **do Certificado X.509.**
    
    e. Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso ao UserEcho.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **UserEcho**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **UserEcho**.

    ![O link UserEcho na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-userecho-test-user"></a>Criar utilizador de teste UserEcho

O objetivo desta secção é criar um utilizador chamado Britta Simon no UserEcho.

**Para criar um utilizador chamado Britta Simon in UserEcho, execute os seguintes passos:**

1. Iniciar súmico no site da empresa UserEcho como administrador.

2. Na barra de ferramentas no topo, clique no nome de utilizador para expandir o menu e, em seguida, clique em **Configuração**.
   
    ![A screenshot mostra a configuração selecionada a partir do site UserEcho.](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Clique **em Utilizadores,** para expandir a secção **de Utilizadores.**
   
    ![A screenshot mostra os utilizadores selecionados a partir do menu Definições.](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Clique em **Utilizadores**.
   
    ![A screenshot mostra os utilizadores selecionados.](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Clique **em Convidar um novo utilizador.**
   
    ![A screenshot mostra ao Convidar um novo controlo do utilizador.](./media/userecho-tutorial/tutorial_userecho_12.png)

6. No **Convite um novo** diálogo de utilizador, execute os seguintes passos:
   
    ![A screenshot mostra ao Convidar uma nova caixa de diálogo do utilizador onde pode introduzir informações do utilizador.](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Na caixa de texto **Name,** tipo nome do utilizador como Britta Simon.
    
    b.  Na caixa de texto **por e-mail,** digite o endereço de e-mail do utilizador como Brittasimon@contoso.com .
    
    c. Clique **em Convidar.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo UserEcho no Painel de Acesso, deverá ser automaticamente inscrito no UserEcho para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

