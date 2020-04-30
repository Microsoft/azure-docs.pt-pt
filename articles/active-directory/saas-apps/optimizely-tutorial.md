---
title: 'Tutorial: Integração do Diretório Ativo Azure com optimizadamente Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 2e25c615e040dd4359e278b95045fbc71ca60ef1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68943945"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Tutorial: Integração do Diretório Ativo Azure com optimizadamente

Neste tutorial, aprende-se a integrar a Optimizely com o Azure Ative Directory (Azure AD).
Integrar optimizadamente com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Optimizely.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Optimizely (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Optimizely, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Optimizadamente única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Otimizadamente suporta **SP** iniciado SSO

## <a name="adding-optimizely-from-the-gallery"></a>Adicionando Otimizadamente da galeria

Para configurar a integração da Optimizely no Azure AD, precisa de adicionar optimizely da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar Optimizely a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Optimizely**, selecione **Optimizely** a partir do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Otimizadamente na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com optimizely baseado num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Optimizely.

Para configurar e testar o único sinal de Azure AD com optimizely, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure optimizemente o single sign-on](#configure-optimizely-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Optimizely test user](#create-optimizely-test-user)** - para ter uma contrapartida de Britta Simon em Optimizely que está ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com optimizely, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Optimizamente,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Otimizar informações de inscrição única de domínio e URLs](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://app.optimizely.net/<instance name>`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Estes valores não são os reais. Atualizará o valor com o URL e Identificador de Sign-on real, o que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. A sua aplicação Optimizamente espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos saml. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

6. Além de acima, a aplicação Optimizely espera que poucos atributos sejam retransmitidos na resposta SAML. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, execute os seguintes passos para adicionar atributo token SAML, conforme indicado no quadro abaixo:

    | Nome | Atributo fonte |
    | ---------------| --------------- |
    | e-mail | utilizador.mail |
    
    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

4. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar Optimizamente,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-optimizely-single-sign-on"></a>Configure optimizemente um único signo

1. Para configurar um único sinal no lado **otimizado,** contacte o Gestor de Conta Optimizely e forneça o Certificado descarregado **(Base64)** e URLs copiados apropriados.

2. Em resposta ao seu e-mail, a Optimizely fornece-lhe o URL de Sinal (SSO iniciado por SP) e os valores de Identificação (Entidade fornecedora de serviços).

    a. Copie o **URL SSO iniciado por SP** fornecido pela Optimizely e cole na caixa de texto **Sign On URL** na secção basic **SAML Configuração** no portal Azure.

    b. Copie o ID da **Entidade prestadora** de serviços fornecido pela Optimizely e cole na caixa de texto **identificador** na secção **basic SAML Configuração** no portal Azure.

3. Numa janela de navegador diferente, inscreva-se na sua aplicação Optimizely.

4. Clique no nome da conta no canto superior direito e, em seguida, **definições de conta**.

    ![Sinal único azure AD](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. No separador Conta, verifique a caixa **Ativar SSO** em 'Sinal Único' na secção **Visão Geral.**
  
    ![Sinal único azure AD](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Clique em **Guardar**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso à Optimizely.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Optimizely**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Optimizely**.

    ![O link Optimizely na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-optimizely-test-user"></a>Criar o utilizador de teste Optimizamente

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Optimizely.

1. Na página inicial, selecione separador **Colaboradores.**

2. Para adicionar um novo colaborador ao projeto, clique em **Novo Colaborador.**
   
    ![Criação de um utilizador de teste azure AD](./media/optimizely-tutorial/create_aaduser_10.png)

3. Preencha o endereço de e-mail e atribua-lhes um papel. Clique em **Convidar**.

    ![Criação de um utilizador de teste azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. Recebem um convite por e-mail. Utilizando o endereço de e-mail, eles têm de fazer login na Optimizely.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Optimizely no Painel de Acesso, deve ser automaticamente inscrito no Optimizely para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

