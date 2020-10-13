---
title: 'Tutorial: Integração do Azure Ative Directory com o Dealpath Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Dealpath.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.openlocfilehash: 5ffdc510fd2a0f81fbfa5d9d04c337c99b389110
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91774548"
---
# <a name="tutorial-azure-active-directory-integration-with-dealpath"></a>Tutorial: Integração do Diretório Ativo Azure com Dealpath

Neste tutorial, aprende-se a integrar o Dealpath com o Azure Ative Directory (Azure AD).
A integração do Dealpath com a Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar em Azure AD que tem acesso ao Dealpath.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Dealpath (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com o Dealpath, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por dealpath

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Dealpath apoia **SP** iniciado SSO

## <a name="adding-dealpath-from-the-gallery"></a>Adicionar Dealpath da galeria

Para configurar a integração do Dealpath no AD Azure, precisa adicionar Dealpath da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Dealpath da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Dealpath**, selecione **Dealpath** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Dealpath na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on with Dealpath com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Dealpath.

Para configurar e testar o Azure AD com um único sign-on com o Dealpath, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On único do Dealpath](#configure-dealpath-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Dealpath test user](#create-dealpath-test-user)** - ter uma contraparte de Britta Simon em Dealpath que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de inscrição com o Dealpath, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Dealpath,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de assinatura única de Dealpath Domain e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL,** digite um URL: `https://app.dealpath.com/account/login`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://api.dealpath.com/saml/metadata/<ID>`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Contacte [a equipa de suporte do Cliente Dealpath](mailto:kenter@dealpath.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração Single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar o Dealpath,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-dealpath-single-sign-on"></a>Configurar dealpath single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no Dealpath como administrador.

2. No superior direito, clique em **Ferramentas de Administração** e navegue para **Integrações,** em seguida, na secção **de autenticação SAML 2.0** clique em **Definições de Atualização**:

    ![Screenshot que mostra a página "Admin Tools - Integrações" com a secção "S A M L 2.0 Authentication" e "Atualização de Definições" selecionadas.](./media/dealpath-tutorial/tutorial_dealpath_admin.png)

3. Na página **de autenticação Configuração SAML 2.0,** execute os seguintes passos:

    ![Configuração dealpath](./media/dealpath-tutorial/tutorial_dealpath_saml.png) 

    a. Na caixa de texto **URL SSO SAML,** cole o valor do URL de **Login,** que copiou do portal Azure.

    b. Na caixa de texto do **Fornecedor de Identidade,** cole o valor do **Identificador Azure Ad,** que copiou do portal Azure.

    c. Copie o conteúdo do ficheiro de certificado descarregado **(Base64)** em bloco de notas e, em seguida, cole-o na caixa de texto **do Certificado Público.**

    d. Clique **nas definições de Atualização**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome,** **insira BrittaSimon**.
  
    b. No campo **nome do utilizador,** **escreva brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Dealpath.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Dealpath**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Dealpath**.

    ![O link Dealpath na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-dealpath-test-user"></a>Criar utilizador de teste Dealpath

Nesta secção, cria-se um utilizador chamado Britta Simon in Dealpath. Trabalhe com a [equipa de suporte do Cliente Dealpath](mailto:kenter@dealpath.com) para adicionar os utilizadores na plataforma Dealpath. Os utilizadores devem ser criados e ativados antes de utilizar o único sinal de sômes

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Dealpath no Painel de Acesso, deverá ser automaticamente inscrito no Dealpath para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

