---
title: 'Tutorial: Integração do Azure Ative Directory com xMatters OnDemand / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o xMatters OnDemand.
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
ms.openlocfilehash: 9e84be9cbc6aa50f4beb3ed333b47459b15fbcc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546292"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Integração do Azure Ative Directory com xMatters OnDemand

Neste tutorial, aprende-se a integrar xMatters OnDemand com o Azure Ative Directory (Azure AD).
A integração do xMatters OnDemand com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a xMatters OnDemand.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no xMatters OnDemand (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com xMatters OnDemand, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* subscrição ativada por xMatters OnDemand

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* xMatters OnDemand suporta **IDP** iniciado SSO

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Adicionando xMatters OnDemand da galeria

Para configurar a integração do xMatters OnDemand em Azure AD, é necessário adicionar xMatters OnDemand da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar xMatters OnDemand da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **xMatters OnDemand,** selecione **xMatters OnDemand** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![xMatters OnDemand na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com xMatters OnDemand com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em xMatters OnDemand.

Para configurar e testar o único sinal de Azure AD com xMatters OnDemand, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure xMatters OnDemand Single Sign-On](#configure-xmatters-ondemand-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Crie xMatters OnDemand utilizador de teste](#create-xmatters-ondemand-test-user)** - para ter uma contraparte de Britta Simon em xMatters OnDemand que está ligada à representação AD AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com xMatters OnDemand, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **xMatters OnDemand,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![xMatters OnDemand Domain e URLs informações únicas de súp livre](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    - `https://<companyname>.au1.xmatters.com.au/`
    - `https://<companyname>.cs1.xmatters.com/`
    - `https://<companyname>.xmatters.com/`
    - `https://www.xmatters.com`
    - `https://<companyname>.xmatters.com.au/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    - `https://<companyname>.au1.xmatters.com.au`
    - `https://<companyname>.xmatters.com/sp/<instancename>`
    - `https://<companyname>.cs1.xmatters.com/sp/<instancename>`
    - `https://<companyname>.au1.xmatters.com.au/<instancename>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de [suporte do cliente xMatters OnDemand](https://www.xmatters.com/company/contact-us/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

    > [!IMPORTANT]
    > Tem de reencaminhar o certificado para a [equipa de apoio xMatters OnDemand](https://www.xmatters.com/company/contact-us/). O certificado tem de ser carregado pela equipa de suporte xMatters antes de finalizar a configuração de inscrição única.

6. Na secção **Configurar xMatters OnDemand,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Configurar xMatters OnDemand Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa XMatters OnDemand como administrador.

2. Na barra de ferramentas no topo, clique em **Administração**e, em seguida, clique em **Detalhes da Empresa** na barra de navegação do lado esquerdo.

    ![Administração](./media/xmatters-ondemand-tutorial/IC776795.png "Administrador")

3. Na página **de Configuração SAML,** execute os seguintes passos:

    ![Configuração SAML](./media/xmatters-ondemand-tutorial/IC776796.png "Configuração SAML")

    a. Selecione **Ativar SAML**.

    b. Na caixa de texto **ID do Fornecedor de Identidade,** cole o valor **identificador Azure AD** que copiou do portal Azure.

    c. Na caixa de texto de url sign **in url,** cole o valor URL de **login** que copiou a partir do portal Azure.

    d. Na caixa de texto **SIM LOGOUT URL,** pasta **URL logout,** que copiou a partir do portal Azure.

    e. Na página Detalhes da Empresa, no topo, clique em **Guardar Alterações.**

    ![Detalhes da empresa](./media/xmatters-ondemand-tutorial/IC776797.png "Detalhes da empresa")

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a xMatters OnDemand.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **xMatters OnDemand**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **xMatters OnDemand**.

    ![O link xMatters OnDemand na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-xmatters-ondemand-test-user"></a>Criar xMatters OnDemand utilizador de teste

O objetivo desta secção é criar um utilizador chamado Britta Simon em xMatters OnDemand.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **XMatters OnDemand.**

2. Clique **no separador Utilizadores** e, em seguida, clique em **Adicionar Utilizador**.

    ![Utilizadores](./media/xmatters-ondemand-tutorial/IC781048.png "Utilizadores")

3. Na secção **Adicionar um Utilizador,** execute os seguintes passos:

    ![Adicionar um Utilizador](./media/xmatters-ondemand-tutorial/IC781049.png "Adicionar um Utilizador")

    a. Selecione **Ative**.

    b. Na caixa de texto **do Utilizador,** digite o id do utilizador como Brittasimon@contoso.com .

    c. Na caixa de texto **Name First,** escreva o primeiro nome do utilizador como Britta.

    d. Na caixa de texto **Do Último Nome,** escreva o apelido do utilizador como Simon.

    e. Na caixa de texto do **Site,** insira o site válido de uma conta AZure AD válida que pretenda obter.

    f. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo xMatters OnDemand no Painel de Acesso, deverá ser automaticamente inscrito no xMatters OnDemand para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

