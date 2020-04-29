---
title: 'Tutorial: Integração de Diretório Ativo Azure com o Visualizador do InstaVR [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o InstaVR Viewer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de94f83c260a86f313a2dd04cdd5a7ae8fc1cda
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73155306"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Tutorial: Integração de Diretório Ativo Azure com o Visualizador do InstaVR

Neste tutorial, aprende-se a integrar o Visualizador do InstaVR com o Azure Ative Directory (Azure AD).
Integrar o Visualizador instavr com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Visualizador InstaVR.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no InstaVR Viewer (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Visualizador do InstaVR, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* InstaVR Viewer única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* O Visualizador do InstaVR suporta **sP** iniciado SSO
* O Visualizador instaVR suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-instavr-viewer-from-the-gallery"></a>Adicionar o Espectador do InstaVR da galeria

Para configurar a integração do Visualizador instavr em Anúncio azure, você precisa adicionar o Visualizador InstaVR da galeria à sua lista de aplicações saaS geridas.

**Para adicionar o Visualizador InstaVR da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o **Visualizador InstaVR,** selecione **O Visualizador instavr** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Visualizador do InstaVR na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com o InstaVR Viewer com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Visualizador instaVR.

Para configurar e testar o único sinal de Azure AD com o Visualizador do InstaVR, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Visualizal Do Visualização Do InstaVR Single Sign-On](#configure-instavr-viewer-single-sign-on)** - para configurar as definições de início de sessão individual no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador do teste do Visualizador instavr](#create-instavr-viewer-test-user)** - para ter uma contrapartida de Britta Simon no Visualizador instaVR que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o Visualizador InstaVR, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do **Visualizador InstaVR,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![InstaVR Viewer Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Não existe um padrão fixo para o Sign on URL. É gerado quando o cliente do Visualizador do InstaVR faz embalagens web. É único para cada cliente e pacote. Para obter o sinal exato no URL, precisa de iniciar sessão na sua instância do Visualizador InstaVR e fazer embalagens web.

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > O valor do identificador não é real. Atualize este valor com o valor do identificador real que é explicado mais tarde neste tutorial.

5. Na configuração do Registo Único com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** e o **Ficheiro de Metadados da Federação** a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadata-certificatebase64.png)

6. Na secção Configurar o **Visualizador instaVR,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-instavr-viewer-single-sign-on"></a>Configure InstaVR Viewer Single Sign-On

1. Abra uma nova janela do navegador web e inicie sessão no site da empresa Do Visualizador do InstaVR como administrador.

2. Clique no **Ícone do Utilizador** e selecione **Conta**.

    ![Configuração do Visualizador InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Percorra até ao **SAML Auth** e execute os seguintes passos:

    ![Configuração do Visualizador InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Na caixa de texto **SSO URL,** colá o valor URL de **Login,** que copiou do portal Azure.

    b. Na caixa de texto **logout URL,** colá o valor URL de **Logout,** que copiou do portal Azure.

    c. Na caixa de texto **Id da Entidade,** colá o valor do **Identificador De Ad Azure,** que copiou do portal Azure.

    d. Para fazer o upload do ficheiro certificado descarregado, clique em **Atualizar**.

    e. Para fazer o upload do ficheiro de Metadados da Federação descarregado, clique em **Atualizar**.

    f. Copie o valor de ID da **Entidade** e cole na caixa de texto **identificador (Id entidade)** na secção **de Configuração SAML Básica** no portal Azure.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Visualizador instaVR.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **O Visualizador do InstaVR**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **Visualizador instaVR**.

    ![O link do Visualizador InstaVR na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-instavr-viewer-test-user"></a>Criar o utilizador de teste do Visualizador InstaVR

Nesta secção, um utilizador chamado Britta Simon é criado no Visualizador de InstaVR. O Visualizador InstaVR suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Visualizador do InstaVR, um novo é criado após a autenticação. Se enfrentar algum problema, contacte a equipa de suporte do [Visualizador do InstaVR](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

1. Abra uma nova janela do navegador web e inicie sessão no site da empresa Do Visualizador do InstaVR como administrador.

2. Selecione **Pacote** a partir do painel de navegação esquerdo e selecione **Fazer pacote para web**.

    ![Configuração do Visualizador InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Selecione **Transferir**.

    ![Configuração do Visualizador InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Selecione **Página Aberta Hospedada** depois disso será redirecionada para Azure AD para login.

    ![Configuração do Visualizador InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Introduza as suas credenciais Azure AD para iniciar sessão com sucesso no Azure AD via SSO.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
