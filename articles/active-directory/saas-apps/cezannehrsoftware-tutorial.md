---
title: 'Tutorial: Integração de Diretórios Ativos Azure com o Software Cezanne HR [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cezanne HR Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa689e6138f8d965e59f7cfa7a85e0835301086c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73158709"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integração de Diretório Ativo Azure com o Software Cezanne HR

Neste tutorial, aprende-se a integrar o Software Cezanne HR com o Azure Ative Directory (Azure AD).
Integrar o Software Cezanne HR com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Software Cezanne HR.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Software Cezanne HR (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Software Cezanne HR, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de assinatura ativada por um único sinal de cezanne HR Software

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Cezanne HR Software suporta **SP** iniciado SSO

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adicionando O Software Cezanne HR da galeria

Para configurar a integração do Software Cezanne HR no Azure AD, precisa adicionar o Software Cezanne HR da galeria à sua lista de aplicações geridas pela SaaS.

**Para adicionar o Software Cezanne HR da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **o Software Cezanne HR,** selecione **O Software Cezanne HR** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![Software Cezanne HR na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on do Azure AD com o Software Cezanne HR baseado num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Software Cezanne HR.

Para configurar e testar o único sign-on da Azure AD com o Software Cezanne HR, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o único sinal de software Cezanne HR](#configure-cezanne-hr-software-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de teste de **[Software Cezanne HR](#create-cezanne-hr-software-test-user)** - para ter uma contrapartida da Britta Simon no Software Cezanne HR que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com o Software Cezanne HR, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **Cezanne HR Software,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Cezanne HR Software Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Na caixa de texto **identificador (Id** da entidade), escreva o URL:`https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Inscrição e Resposta real. Contacte a equipa de suporte do [Cliente do Cliente da Cezanne HR](https://cezannehr.com/services/support/) para obter estes valores.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **De Configuração de Software Cezanne HR,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Configure Cezanne HR Software Single Sign-On

1. Numa janela de navegador web diferente, inscreva-se no seu inquilino de Software Cezanne HR como administrador.

2. No painel de navegação à esquerda, clique em **Configuração do Sistema**. Ir para **as Definições**de Segurança . Em seguida, navegue para **configuração de sinal único**.

    ![Configure o único sinal no lado da aplicação](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. No **permitir que os utilizadores iniciem sessão utilizando o seguinte painel de serviço single sign-on (SSO),** verifique a caixa **SAML 2.0** e selecione a opção **Configuração Avançada.**

    ![Configure o único sinal no lado da aplicação](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Clique em **Adicionar Novo** botão.

    ![Configure o único sinal no lado da aplicação](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Efetue os seguintes passos na secção **SAML 2.0 IDENTITY PROVIDERS.**

    ![Configure o único sinal no lado da aplicação](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Insira o nome do seu Fornecedor de Identidade como nome de **exibição**.

    b. Na caixa de texto **Entidade Identificador,** colhe o valor do **Identificador Azure Ad** que copiou do portal Azure.

    c. Alterar a **ligação SAML** para 'POST'.

    d. Na caixa de texto de suporte do **serviço De token Security,** colá o valor do URL de **Login** que copiou do portal Azure.

    e. Na caixa de texto de nome `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`de identificação do utilizador, introduza .

    f. Clique no ícone **upload** para fazer o upload do certificado descarregado do portal Azure.

    g. Clique no botão **OK**.

6. Clique no botão **Guardar.**

    ![Configure o único sinal no lado da aplicação](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Software Cezanne HR.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **O Software Cezanne HR**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Cezanne HR Software**.

    ![O link de Software Cezanne HR na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-cezanne-hr-software-test-user"></a>Criar o utilizador de teste de Software Cezanne HR

Para permitir que os utilizadores da AD Azure entrem no Software Cezanne HR, devem ser aprovisionados no Software Cezanne HR. No caso da Cezanne HR Software, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da empresa cezanne HR Software como administrador.

2. No painel de navegação à esquerda, clique em **Configuração do Sistema**. Ir para **Gerir utilizadores**. Em seguida, navegue para **adicionar novo utilizador**.

    ![Novo Utilizador](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Novo Utilizador")

3. Na secção **DETALHES DA PESSOA,** execute abaixo os passos:

    ![Novo Utilizador](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Novo Utilizador")

    a. Desloque **o Utilizador Interno** como OFF.

    b. Na caixa de texto **First Name,** digite o Primeiro Nome de utilizador como **Britta**.  

    c. Na caixa de texto **Last Name,** digite o último nome do utilizador como **Simon**.

    d. Na caixa de texto **por e-mail,** Brittasimon@contoso.comdigite o endereço de e-mail do utilizador como .

4. Na secção **informação sobre contas,** execute abaixo os passos:

    ![Novo Utilizador](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Novo Utilizador")

    a. Na caixa de texto **username,** digite o e-mail do utilizador como Brittasimon@contoso.com.

    b. Na caixa de texto **Password,** digite a palavra-passe do utilizador.

    c. Selecione **HR Professional** como **Função**de Segurança .

    d. Clique em **OK**.

5. Navegue para um separador **de sinal único** e selecione Adicionar **Novo** na área de **identificadores SAML 2.0.**

    ![Utilizador](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utilizador")

6. Escolha o seu Fornecedor de Identidade para o Fornecedor de **Identidade** e na caixa de texto do Identificador de **Utilizador,** insira o endereço de e-mail da conta Britta Simon.

    ![Utilizador](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utilizador")

7. Clique no botão **Guardar.**

    ![Utilizador](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utilizador")

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo cezanne HR Software no Painel de Acesso, deve ser automaticamente inscrito no Software Cezanne HR para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
