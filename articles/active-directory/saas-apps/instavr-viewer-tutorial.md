---
title: 'Tutorial: integração do Azure Active Directory com o Visualizador do InstaVR | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Visualizador do InstaVR.
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
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73155306"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Tutorial: integração do Azure Active Directory com o Visualizador do InstaVR

Neste tutorial, você aprenderá a integrar o InstaVR Viewer ao Azure Active Directory (Azure AD).
A integração do InstaVR Viewer ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Visualizador do InstaVR.
* Você pode permitir que seus usuários sejam automaticamente conectados ao InstaVR Viewer (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao InstaVR Viewer, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do InstaVR Viewer

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Visualizador de InstaVR dá suporte ao SSO iniciado por **SP**
* O InstaVR Viewer dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-instavr-viewer-from-the-gallery"></a>Adicionando o InstaVR Viewer da Galeria

Para configurar a integração do InstaVR Viewer ao Azure AD, você precisará adicionar o InstaVR Viewer da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o InstaVR Viewer da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **InstaVR Viewer**, selecione **Visualizador de InstaVR** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![InstaVR Viewer na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o InstaVR Viewer, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do InstaVR Viewer.

Para configurar e testar o logon único do Azure AD com o InstaVR Viewer, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do InstaVR Viewer](#configure-instavr-viewer-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do InstaVR Viewer](#create-instavr-viewer-test-user)** – para ter um equivalente de Brenda Simon no InstaVR Viewer que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o InstaVR Viewer, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **InstaVR Viewer** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do InstaVR Viewer](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Não há nenhum padrão fixo para a URL de logon. Ele é gerado quando o cliente do InstaVR Viewer faz empacotamento da Web. Ele é exclusivo para cada cliente e pacote. Para obter a URL de logon exata, você precisa fazer logon na instância do Visualizador do InstaVR e fazer o empacotamento da Web.

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > O valor do identificador não é real. Atualize esse valor com o valor do identificador real que é explicado posteriormente neste tutorial.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** e o arquivo de **metadados de Federação** das opções determinadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download do certificado](common/metadata-certificatebase64.png)

6. Na seção **Configurar o Visualizador do InstaVR** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-instavr-viewer-single-sign-on"></a>Configurar o logon único do Visualizador do InstaVR

1. Abra uma nova janela do navegador da Web e faça logon em seu site de empresa do InstaVR Viewer como administrador.

2. Clique no **ícone do usuário** e selecione **conta**.

    ![Configuração do Visualizador do InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Role para baixo até a **autenticação SAML** e execute as seguintes etapas:

    ![Configuração do Visualizador do InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Na caixa de texto **URL de SSO** , Cole o valor da **URL de logon** copiado do portal do Azure.

    b. Na caixa de texto **URL de logout** , Cole o valor da **URL de logout** copiado do portal do Azure.

    c. Na caixa de texto **ID da entidade** , Cole o valor do **identificador do Azure ad** copiado do portal do Azure.

    d. Para carregar o arquivo de certificado baixado, clique em **Atualizar**.

    e. Para carregar o arquivo de metadados de Federação baixado, clique em **Atualizar**.

    f. Copie o valor **ID da entidade** e cole na caixa de texto **identificador (ID da entidade)** na seção **configuração básica do SAML** no portal do Azure.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Visualizador do InstaVR.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Visualizador de InstaVR**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Visualizador de InstaVR**.

    ![O link do InstaVR Viewer na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-instavr-viewer-test-user"></a>Criar usuário de teste do InstaVR Viewer

Nesta seção, um usuário chamado Brenda Simon é criado no Visualizador do InstaVR. O InstaVR Viewer dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no InstaVR Viewer, um novo será criado após a autenticação. Se você enfrentar problemas, entre em contato com a [equipe de suporte do InstaVR Viewer](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

1. Abra uma nova janela do navegador da Web e faça logon em seu site de empresa do InstaVR Viewer como administrador.

2. Selecione **pacote** no painel de navegação à esquerda e selecione **criar pacote para a Web**.

    ![Configuração do Visualizador do InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Selecione **Transferir**.

    ![Configuração do Visualizador do InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Selecione **abrir página hospedada** depois que ela será redirecionada para o Azure ad para logon.

    ![Configuração do Visualizador do InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Insira suas credenciais do Azure AD para fazer logon com êxito no Azure AD por meio do SSO.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
