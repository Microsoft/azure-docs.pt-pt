---
title: 'Tutorial: Integração do Diretório Ativo Azure com launchDarkly / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3f0671bc-f93f-496e-b465-b9ce8c6633fa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e32f8c3ea300960893163264e99bd6c51138c7c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73159654"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Tutorial: Integração do Diretório Ativo Azure com o LaunchDarkly

Neste tutorial, aprende-se a integrar o LaunchDarkly com o Azure Ative Directory (Azure AD).
Integrar o LaunchDarkly com o Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao LaunchDarkly.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no LaunchDarkly (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o LaunchDarkly, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* LaunchDarkly single sign-on enabled subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* LaunchDarkly suporta **SP e IDP** iniciadoS SSO
* LaunchDarkly suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-launchdarkly-from-the-gallery"></a>Adicionando LaunchDarkly da galeria

Para configurar a integração do LaunchDarkly no Azure AD, precisa de adicionar launchDarkly da galeria à sua lista de aplicações saaS geridas.

**Para adicionar LaunchDarkly a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **LaunchDarkly**, selecione **LaunchDarkly** a partir do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![LançamentoDarkly na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único signo da Azure AD com [nome de aplicação] com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em [Nome de aplicação].

Para configurar e testar o único sinal de Azure AD com [nome de aplicação], é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o LaunchDarkly Single Sign-On](#configure-launchdarkly-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create LaunchDarkly test user](#create-launchdarkly-test-user)** - para ter uma contrapartida de Britta Simon no LaunchDarkly que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com [nome da aplicação], execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **LaunchDarkly,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![LaunchDarkly Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **identificador,** digite um URL:`app.launchdarkly.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > O valor url de resposta não é real. Atualizará o valor com o URL de Resposta real, o que é explicado mais tarde no tutorial. Se pretende utilizar a aplicação no modo **IDP,** tem de deixar o **Sinal no** campo URL em branco, caso contrário não poderá iniciar o login a partir do **IDP**. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://app.launchdarkly.com`

    ![LaunchDarkly Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

6. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

7. Na secção **set up LaunchDarkly,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-launchdarkly-single-sign-on"></a>Configure lançamento Darkly single sign-on

1. Numa janela diferente do navegador web, inicie sessão no site da empresa LaunchDarkly como administrador.

2. Selecione **Definições** de Conta do painel de navegação esquerdo.

    ![Configuração de lançamentoDarkly](./media/launchdarkly-tutorial/configure1.png)

3. Clique no separador **Segurança.**

    ![Configuração de lançamentoDarkly](./media/launchdarkly-tutorial/configure2.png)

4. Clique em **Ativação SSO** e, em seguida, **EDITAR CONFIGURAÇÃO SAML**.

    ![Configuração de lançamentoDarkly](./media/launchdarkly-tutorial/configure3.png)

5. Na secção **de configuração SAML Editar,** execute os seguintes passos:

    ![Configuração de lançamentoDarkly](./media/launchdarkly-tutorial/configure4.png)

    a. Copie o URL de serviço ao **consumidor SAML** para a sua instância e cole-o na caixa de texto URL de resposta no **launchDarkly Domain e urLs** no portal Azure.

    b. Na caixa de texto **sAL De Acesso,** colá o valor URL do **Login,** que copiou do portal Azure.

    c. Abra o certificado descarregado do portal Azure no Notepad, copie o conteúdo e, em seguida, cole-o na caixa de **certificados X.509** ou pode carregar diretamente o certificado clicando no **upload um**.

    d. Clique em **Guardar**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao LaunchDarkly.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **LaunchDarkly**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LaunchDarkly**.

    ![O link LaunchDarkly na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-launchdarkly-test-user"></a>Criar o utilizador de teste LaunchDarkly

O objetivo desta secção é criar um utilizador chamado Britta Simon no LaunchDarkly. O LaunchDarkly suporta o fornecimento just-in-time, que está por defeito habilitado. Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder ao LaunchDarkly se ainda não existir.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte ao [Cliente LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo LaunchDarkly no Painel de Acesso, deve ser automaticamente inscrito no LaunchDarkly para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
