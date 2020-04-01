---
title: 'Tutorial: Integração do Diretório Ativo Azure com my award points Top Sub/Top Team [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o My Award Points Top Sub/Top Team.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: b1de9e394dd1e6b4c47b8de3175dd1e8fba1d87e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161334"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Tutorial: Integração de Diretório Ativo Azure com my award points top sub/top team

Neste tutorial, aprende-se a integrar o My Award Points Top Sub/Top Team com o Azure Ative Directory (Azure AD).
Integrar o meu prémio pontos de sub/top team com azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a My Award Points Top Sub/Top Team.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no My Award Points Top Sub/Top Team (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Anúncio Azure com o My Award Points Top Sub/Top Team, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* My Award Points Top Sub/Top Team assinatura de inscrição única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* My Award Points Top Sub/Top Team suporta **SP** iniciado SSO

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Adicionando os meus pontos de prêmio top sub/top team da galeria

Para configurar a integração do My Award Points Top Sub/Top Team em Azure AD, você precisa adicionar My Award Points Top Sub/Top Team da galeria à sua lista de aplicações geridas saaS.

**Para adicionar my award points Top Sub/Top Team da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **My Award Points Top Sub/Top Team,** selecione My Award Points Top **Sub/Top Team** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![My Award Points Top Sub/Top Team na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure ad com my award points Top Sub/Top Team com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no My Award Points Top Sub/Top Team.

Para configurar e testar o single sign-on azure AD com my award points Top Sub/Top Team, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **Configure o meu prémio pontos de sub/top team single sign-on** - para configurar as definições de início de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **Create My Award Points Top Sub/Top Team user** - para ter uma contrapartida de Britta Simon no My Award Points Top Sub/Top Team que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o single sign-on azure com my award points Top Sub/Top Team, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **sub/top team** de My Award Points Top, selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![My Award Points Top Sub/Top Team Domain and URLs single sign-on information](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<Azure AD Identifier>`

    > [!NOTE]
    > O valor não é real. Você terá `<Azure AD Identifier>` o valor nos passos posteriores neste tutorial.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na **secção "Configurar os pontos de topo" do meu prémio,** copie os URL(s) apropriados de acordo com o seu requisito. 

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

    >[!NOTE]
    >Anexar o valor copiado do identificador Azure AD `<Azure AD Identifier>` com o Sinal no URL no lugar da secção **de Configuração SAML Básica** no portal Azure.

### <a name="configure-my-award-points-top-subtop-team-single-sign-on"></a>Configure o meu prémio aponta para o top sub/top team single sign-on

To configure single sign-on on **My Award Points Top Sub/Top Team** side, you need to send the downloaded **Federation Metadata XML** and appropriate copied URLs from Azure portal to [My Award Points Top Sub/Top Team support team](mailto:myawardpoints@biworldwide.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao My Award Points Top Sub/Top Team.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **My Award Points Top Sub/Top Team**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **My Award Points Top Sub/Top Team**.

    ![O link My Award Points Top Sub/Top Team na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-my-award-points-top-subtop-team-test-user"></a>Crie o meu melhor utilizador de testes sub/top team

Nesta secção, cria-se uma utilizadora chamada Britta Simon no My Award Points Top Sub/Top Team. Trabalhe com a equipa de [suporte sub/top team](mailto:myawardpoints@biworldwide.com) my Award Points para adicionar os utilizadores na plataforma My Award Points Top Sub/Top Team. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo My Award Points Top Sub/Top Team no Painel de Acesso, deve ser automaticamente inscrito na Sub/Top Team my Award Points para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
