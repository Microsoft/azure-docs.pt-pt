---
title: 'Tutorial: Integração do Azure Ative Directory com o XaitPorter Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o XaitPorter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: f729828627fdb0be18f220d34227aac756ed4e13
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92894887"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Tutorial: Integração do Azure Ative Directory com o XaitPorter

Neste tutorial, aprende-se a integrar o XaitPorter com o Azure Ative Directory (Azure AD).
A integração do XaitPorter com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao XaitPorter.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no XaitPorter (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o XaitPorter, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pelo XaitPorter

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* XaitPorter suporta SSO iniciado **pela SP**

## <a name="adding-xaitporter-from-the-gallery"></a>Adicionar XaitPorter da galeria

Para configurar a integração do XaitPorter no AD Azure, é necessário adicionar o XaitPorter da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar XaitPorter da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **XaitPorter** , selecione **XaitPorter** do painel de resultados e clique em **Adicionar** botão adicionar a aplicação.

     ![XaitPorter na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o XaitPorter com base num utilizador de teste chamado **Britta Simon** .
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no XaitPorter.

Para configurar e testar o Azure AD com o XaitPorter, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único do XaitPorter](#configure-xaitporter-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create XaitPorter test user](#create-xaitporter-test-user)** - para ter uma contraparte de Britta Simon em XaitPorter que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o XaitPorter, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **XaitPorter,** selecione **Single sign-on** .

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![XaitPorter Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<subdomain>.xaitporter.com/saml/login`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<subdomain>.xaitporter.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente XaitPorter](https://www.xait.com/support/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

6. Forneça o **endereço IP** ou o Url **de Metadados da Federação de Aplicações** à [equipa de suporte smartRecruiters,](https://www.smartrecruiters.com/about-us/contact-us/)para que o XaitPorter possa garantir que o endereço IP seja acessível a partir da sua lista de identificação de instâncias XaitPorter ao seu lado. 

### <a name="configure-xaitporter-single-sign-on"></a>Configurar XaitPorter Single Sign-On

1. Para automatizar a configuração dentro do XaitPorter, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão** .

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup XaitPorter** irá direcioná-lo para a aplicação XaitPorter. A partir daí, forneça as credenciais de administração para assinar no XaitPorter. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o XaitPorter manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa XaitPorter como administrador e execute os seguintes passos:

4. Clique em **Administração.**

    ![A screenshot mostra O Administrador selecionado no site do XaitPorter.](./media/xaitporter-tutorial/user1.png)

5. **Selecione Gerir o Sign-On único** a partir da lista de desistências da configuração do **sistema.**

    ![O screenshot mostra gerir o single Sign-On selecionados a partir da configuração do sistema.](./media/xaitporter-tutorial/user2.png)

6. Na secção **GESTÃO ÚNICA SIGN-ON,** execute os seguintes passos:

    ![A screenshot mostra a secção GESTIÃO ÚNICA SIGN-ON onde pode executar estes passos.](./media/xaitporter-tutorial/user3.png)

    a. Selecione **Ativar a autenticação Sign-On única** .

    b. Na caixa de texto **de Definições de Fornecedor de Identidade,** cole **url de metadados da Federação de Aplicações** que copiou a partir do portal Azure e clique em **Fetch** .

    c. Selecione **Ativar a auto-criação dos utilizadores.**

    d. Clique em **OK** .

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon** .
  
    b. No tipo de campo **do nome do utilizador** brittasimon@yourcompanydomain.extension . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao XaitPorter.

1. No portal Azure, selecione **Aplicações empresariais** , selecione **Todas as aplicações,** em seguida, selecione **XaitPorter** .

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **XaitPorter** .

    ![O link XaitPorter na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos** .

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-xaitporter-test-user"></a>Criar utilizador de teste XaitPorter

Nesta secção, cria-se um utilizador chamado Britta Simon em XaitPorter. Trabalhe com a [equipa de suporte do XaitPorter para](https://www.xait.com/support/) adicionar os utilizadores na plataforma XaitPorter. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo XaitPorter no Painel de Acesso, deverá ser automaticamente inscrito no XaitPorter para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)