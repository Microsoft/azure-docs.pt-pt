---
title: 'Tutorial: Integração do Azure Ative Directory com o UserVoice Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o UserVoice.
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
ms.openlocfilehash: 3313e923bbe5218a965c58d2faee810182c00aa6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88532807"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Integração do Diretório Ativo Azure com o UserVoice

Neste tutorial, aprende a integrar o UserVoice com o Azure Ative Directory (Azure AD).
A integração do UserVoice com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao UserVoice.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no UserVoice (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o UserVoice, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada pelo UserVoice

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* UserVoice suporta **SSO** iniciado sp

## <a name="adding-uservoice-from-the-gallery"></a>Adicionar UserVoice da galeria

Para configurar a integração do UserVoice no AD Azure, é necessário adicionar o UserVoice da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar UserVoice na galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **UserVoice**, selecione **UserVoice** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![UserVoice na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o UserVoice com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no UserVoice.

Para configurar e testar o Azure AD com o UserVoice, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o sign-on único do UserVoice](#configure-uservoice-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create UserVoice test user user](#create-uservoice-test-user)** - para ter uma contraparte de Britta Simon no UserVoice que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o UserVoice, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **UserVoice,** selecione **'Único s-on'.**

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmis de dados únicos do UserVoice e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<tenantname>.UserVoice.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente UserVoice](https://www.uservoice.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção **certificado de assinatura SAML,** copie a **impressão digital e** guarde-a no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

7. Na secção **Configurar o UserVoice,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-uservoice-single-sign-on"></a>Configurar o Sign-On único do utilizador

1. Numa janela diferente do navegador web, inscreva-se no site da empresa UserVoice como administrador.

2. Na barra de ferramentas na parte superior, clique em **Definições**e, em seguida, selecione o **portal Web** a partir do menu.
   
    ![Secção de definições no lado da aplicação](./media/uservoice-tutorial/ic777519.png "Definições")

3. No separador **portal Web,** na secção **de autenticação** do Utilizador, clique em **Editar** para abrir a página de diálogo de autenticação do utilizador de **Edição.**
   
    ![Separador do portal web](./media/uservoice-tutorial/ic777520.png "Portal Web")

4. Na página de diálogo **de autenticação do utilizador editar,** execute os seguintes passos:
   
    ![Editar a autenticação do utilizador](./media/uservoice-tutorial/ic777521.png "Editar a autenticação do utilizador")
   
    a. Clique **em Sign-On única (SSO)**.
 
    b. Cole o valor URL do **Login,** que copiou do portal Azure para a caixa de texto **SSO Remote Sign-In.**

    c. Cole o valor **URL logout,** que copiou do portal Azure para a **caixa de texto SSO Remote Sign-Out**.
 
    d. Cole o valor **de impressão digital thumbprint** , que copiou do portal Azure para a caixa **de texto de impressão digital Sha1 do certificado atual.**
    
    e. Clique **em Guardar as definições de autenticação**.

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

Nesta secção, permite à Britta Simon utilizar o Azure single sign-on, concedendo acesso ao UserVoice.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **UserVoice**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **UserVoice**.

    ![O link UserVoice na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-uservoice-test-user"></a>Criar utilizador de teste UserVoice

Para permitir que os utilizadores de Azure AD inscrevam-se no UserVoice, devem ser a provisionados no UserVoice. No caso do UserVoice, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para obter uma conta de utilizador, execute os seguintes passos:

1. Inscreva-se no seu inquilino **UserVoice.**

2. Ir para **Definições**.
   
    ![Definições](./media/uservoice-tutorial/ic777811.png "Definições")

3. Clique **em Geral.**

4. Clique **em Agentes e permissões**.
   
    ![Agentes e permissões](./media/uservoice-tutorial/ic777812.png "Agentes e permissões")

5. Clique **em Adicionar administradores**.
   
    ![Adicionar administradores](./media/uservoice-tutorial/ic777813.png "Adicionar administradores")

6. No diálogo **dos administradores do Convite,** execute os seguintes passos:
   
    ![Convidar administradores](./media/uservoice-tutorial/ic777814.png "Convidar administradores")
   
    a. Na caixa de texto de Emails, digite o endereço de e-mail da conta que pretende prestar e, em seguida, clique em **Adicionar**.
   
    b. Clique **em Convidar.**

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador userVoice ou APIs fornecidas pelo UserVoice para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo UserVoice no Painel de Acesso, deverá ser automaticamente inscrito no UserVoice para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

