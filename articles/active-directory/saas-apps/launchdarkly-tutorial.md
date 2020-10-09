---
title: 'Tutorial: Integração do Azure Ative Directory com o LaunchDarkly Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LaunchDarkly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 78f9217ea7288bac56b008911f7b39c73bba7771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856556"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Tutorial: Integração do Azure Ative Directory com o LaunchDarkly

Neste tutorial, aprende-se a integrar o LaunchDarkly com o Azure Ative Directory (Azure AD).
Integrar o LaunchDarkly com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao LaunchDarkly.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no LaunchDarkly (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o LaunchDarkly, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* LaunchDarkly assinatura ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* LaunchDarkly apoia **SP e IDP** iniciado SSO
* LaunchDarkly suporta o fornecimento **de utilizadores just in time**

## <a name="adding-launchdarkly-from-the-gallery"></a>Adicionar LaunchDarkly da galeria

Para configurar a integração do LaunchDarkly no AD Azure, é necessário adicionar o LaunchDarkly da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar LaunchDarkly da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite LaunchDarkly**, selecione **LaunchDarkly** do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** para adicionar a aplicação.

     ![LançamentoDarkly na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com [Nome de aplicação] com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em [Nome de aplicação].

Para configurar e testar o único sinal de Azure AD com [Nome da aplicação], é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o LaunchDarkly Single Sign-On](#configure-launchdarkly-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create LaunchDarkly test user](#create-launchdarkly-test-user)** - para ter uma contraparte de Britta Simon no LaunchDarkly que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com [Nome da aplicação], execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **LaunchDarkly,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite um URL: `app.launchdarkly.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > O valor URL de resposta não é real. Irá atualizar o valor com o URL de resposta real, que é explicado mais tarde no tutorial. Se pretender utilizar a aplicação no modo **IDP,** tem de deixar o Sinal em branco no campo **URL,** caso contrário não poderá iniciar o login a partir do **IDP**. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://app.launchdarkly.com`

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **set up LaunchDarkly,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-launchdarkly-single-sign-on"></a>Configure lançamentoDarkly single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no seu site da empresa LaunchDarkly como administrador.

2. Selecione **Definições** de Conta do painel de navegação esquerdo.

    ![A screenshot mostra o item Definições de Conta selecionados em Produção.](./media/launchdarkly-tutorial/configure1.png)

3. Clique no separador **Segurança.**

    ![A screenshot mostra o separador segurança das definições de Conta.](./media/launchdarkly-tutorial/configure2.png)

4. Clique **EM ATIVAR SSO** e, em seguida, **EDITAR CONFIGURAÇÃO SAML**.

    ![A screenshot mostra a página de assinatura única onde pode ATIVAR S S O e EDIT SAML CONFIGURAÇÃO.](./media/launchdarkly-tutorial/configure3.png)

5. Na secção **de configuração da Editar,** execute os seguintes passos:

    ![A screenshot mostra a secção de configuração DOML onde pode escriminar as alterações descritas aqui.](./media/launchdarkly-tutorial/configure4.png)

    a. Copie o **URL de serviço ao consumidor SAML** para o seu exemplo e cole-o na caixa de texto de URL de resposta na secção **LaunchDarkly Domain and URLs** no portal Azure.

    b. Na caixa de texto **URL de acesso,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    c. Abra o certificado descarregado do portal Azure para o Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa **de certificados X.509** ou pode fazer o upload direto do certificado clicando no **upload um**.

    d. Clicar em **Guardar**

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

Nesta secção, você permite que Britta Simon use a Azure single sign-on, concedendo acesso ao LaunchDarkly.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **LaunchDarkly**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LaunchDarkly**.

    ![O link LaunchDarkly na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-launchdarkly-test-user"></a>Criar LaunchDarkly test user

O objetivo desta secção é criar um utilizador chamado Britta Simon no LaunchDarkly. O LaunchDarkly suporta o provisionamento just-in-time, que é por defeito ativado. Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder ao LaunchDarkly se ainda não existir.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte do Cliente LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo LaunchDarkly no Painel de Acesso, deverá ser automaticamente inscrito no LaunchDarkly para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
