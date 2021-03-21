---
title: 'Tutorial: Integração do Azure Ative Directory com o InstaVR Viewer | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o InstaVR Viewer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: e60e8c73c9f1da617851cc67fb2dbab7171f1cb0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460013"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Tutorial: Integração do Azure Ative Directory com o InstaVR Viewer

Neste tutorial, aprende-se a integrar o InstaVR Viewer com o Azure Ative Directory (Azure AD).
Integrar o InstaVR Viewer com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao InstaVR Viewer.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no InstaVR Viewer (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o InstaVR Viewer, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada pelo InstaVR Viewer

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* InstaVR Viewer suporta **SSO** iniciado SP
* O Visualizador do InstaVR suporta o fornecimento do utilizador **Just In Time**

## <a name="adding-instavr-viewer-from-the-gallery"></a>Adicionar Espectador de InstaVR da galeria

Para configurar a integração do InstaVR Viewer no AZure AD, é necessário adicionar o InstaVR Viewer da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o InstaVR Viewer da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite o Visualizador InstaVR,** selecione **InstaVR Viewer** a partir do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Visualizador do InstaVR na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o InstaVR Viewer com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador AD Azure e o utilizador relacionado no InstaVR Viewer.

Para configurar e testar o Azure AD com o InstaVR Viewer, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Único Sign-on do Espectador Do InstaVR](#configure-instavr-viewer-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. Crie o utilizador de **[teste do InstaVR Viewer](#create-instavr-viewer-test-user)** - para ter uma contrapartida de Britta Simon no InstaVR Viewer que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o InstaVR Viewer, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **do InstaVR Viewer,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![InstaVR Viewer Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Não existe um padrão fixo para o sinal no URL. É gerado quando o cliente do InstaVR Viewer faz embalagens web. É único para cada cliente e pacote. Para obter o sinal exato no URL, tem de iniciar sessão no seu InstaVR Viewer e fazer a embalagem web.

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize este valor com o valor real do Identificador que é explicado mais tarde neste tutorial.

5. Na **configuração Single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** e **o Arquivo de Metadados da Federação** a partir das opções dadas de acordo com o seu requisito e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadata-certificatebase64.png)

6. Na secção Configurar o **Visualizador InstaVR,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-instavr-viewer-single-sign-on"></a>Configurar o Sign-On único do espectador do InstaVR

1. Abra uma nova janela do navegador web e inicie sessão no site da empresa Do InstaVR Viewer como administrador.

2. Clique no **Ícone do Utilizador** e selecione **Conta.**

    ![A screenshot mostra o seu site do InstaVR Viewer com um utilizador selecionado.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Desloque-se até ao **Auth SAML** e execute os seguintes passos:

    ![A screenshot mostra a página SAML Auth onde pode introduzir os valores descritos neste passo.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Na caixa de texto **SSO URL,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    b. Na caixa de texto **URL logout,** cole o valor **URL logout,** que copiou a partir do portal Azure.

    c. Na caixa de texto de **ID** da Entidade, cole o valor **Azure Ad Identifier,** que copiou a partir do portal Azure.

    d. Para fazer o upload do seu ficheiro Certificado descarregado, clique em **Update**.

    e. Para fazer o upload do ficheiro de metadados da Federação descarregado, clique em **Update**.

    f. Copie o valor de **ID** da Entidade e cole-o na caixa de texto **identifier (Entity ID)** na secção **de Configuração BÁSICA SAML** no portal Azure.

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

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao InstaVR Viewer.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **InstaVR Viewer**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **o InstaVR Viewer.**

    ![O link do Visualizador InstaVR na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-instavr-viewer-test-user"></a>Criar utilizador de teste do InstaVR Viewer

Nesta secção, um utilizador chamado Britta Simon é criado no InstaVR Viewer. O InstaVR Viewer suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no InstaVR Viewer, um novo é criado após a autenticação. Se tiver algum problema, contacte a equipa de [suporte do InstaVR Viewer.](mailto:contact@instavr.co)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

1. Abra uma nova janela do navegador web e inicie sessão no site da empresa Do InstaVR Viewer como administrador.

2. Selecione **Pacote** a partir do painel de navegação esquerdo e selecione **Faça o pacote para web**.

    ![O Screenshot mostra o site da empresa do InstaVR Viewer com pacote select e fazer pacote para web selecionado.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Selecione **Transferir**.

    ![A screenshot mostra o ícone de Descarregamento selecionado.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Selecione **Open Hosted Page** depois disso, será redirecionado para Azure AD para login.

    ![A screenshot mostra a página de anfitriões aberta selecionada.](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Insira as suas credenciais AZure AD para iniciar sessão com sucesso no AD AZure via SSO.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)