---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o BlueJeans para o Azure AD | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BlueJeans para o Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595039"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o BlueJeans para Azure AD

Neste tutorial, você aprenderá a integrar o BlueJeans ao Azure AD com o Azure Active Directory (Azure AD). Ao integrar o BlueJeans para o Azure AD com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao BlueJeans para Azure AD.
* Habilite seus usuários a serem conectados automaticamente ao BlueJeans para o Azure AD com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* BlueJeans para assinatura habilitada para SSO (logon único) do Azure AD.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O BlueJeans para Azure AD dá suporte ao SSO iniciado pelo **SP**

* O BlueJeans para Azure AD dá suporte ao [provisionamento **automatizado** de usuários](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Adicionando o BlueJeans para o Azure AD da Galeria

Para configurar a integração do BlueJeans para o Azure AD ao Azure AD, você precisará adicionar o BlueJeans para o Azure AD da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **BLUEJEANS para Azure ad** na caixa de pesquisa.
1. Selecione **BlueJeans para o Azure ad** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Configurar e testar o logon único do Azure AD para o BlueJeans para Azure AD

Configure e teste o SSO do Azure AD com o BlueJeans para Azure AD usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no BlueJeans para o Azure AD.

Para configurar e testar o SSO do Azure AD com o BlueJeans para Azure AD, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o BlueJeans para o SSO do Azure ad](#configure-bluejeans-for-azure-ad-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar BlueJeans para o usuário de teste do Azure ad](#create-bluejeans-for-azure-ad-test-user)** – para ter um equivalente de B. Simon no BlueJeans para Azure AD que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **BlueJeans para o Azure ad** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.bluejeans.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do BlueJeans para Azure ad](https://support.bluejeans.com/contact) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar o BlueJeans para o Azure ad** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao BlueJeans para o Azure AD.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **BlueJeans para o Azure ad**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Configurar o BlueJeans para o SSO do Azure AD

1. Em uma janela diferente do navegador da Web, entre em seu site de empresa do **BlueJeans para Azure ad** como administrador.

2. Vá para **administração \> configurações do grupo \> segurança**.

    ![ADM](./media/bluejeans-tutorial/ic785868.png "ADM")

3. Na seção **segurança** , execute as seguintes etapas:

    ![Logon único do SAML](./media/bluejeans-tutorial/ic785869.png "Logon único do SAML")

    a. Selecione **logon único do SAML**.

    b. Selecione **habilitar provisionamento automático**.

4. Passe com as seguintes etapas:

    ![Caminho do certificado](./media/bluejeans-tutorial/ic785870.png "Caminho do certificado")

    a. Clique em **escolher arquivo**para carregar o certificado codificado em base 64 que você baixou do portal do Azure.

    b. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon** que você copiou do portal do Azure.

    c. Na caixa de texto **URL de alteração de senha** , Cole o valor da URL de alteração de **senha** que você copiou do portal do Azure.

    d. Na caixa de texto **URL de logout** , Cole o valor da **URL de logout** que você copiou do portal do Azure.

5. Passe com as seguintes etapas:

    ![Salvar alterações](./media/bluejeans-tutorial/ic785874.png "Salvar alterações")

    a. Na caixa de texto **ID de usuário** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Na caixa de texto **email** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Clique em **salvar alterações**.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Criar BlueJeans para o usuário de teste do Azure AD

O objetivo desta seção é criar um usuário chamado B. Simon no BlueJeans para o Azure AD. O BlueJeans para Azure AD dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [aqui](bluejeans-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Entre em seu site **de empresa do BlueJeans para Azure ad** como administrador.

2. Vá para **administrador \> gerenciar usuários \> Adicionar usuário**.

    ![ADM](./media/bluejeans-tutorial/ic785877.png "ADM")

    > [!IMPORTANT]
    > A guia **Adicionar usuário** só estará disponível se, na **guia Segurança**, **habilitar o provisionamento automático** estiver desmarcada.

3. Na seção **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar usuário](./media/bluejeans-tutorial/ic785886.png "Adicionar Utilizador")

    a. Na caixa de texto **nome** , insira o nome do usuário como **B**.

    b. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.

    c. Em selecionar uma caixa de texto de nome de usuário do **BlueJeans para Azure**

    d. Na caixa de texto **criar uma senha** , insira sua senha.

    e. Na caixa de texto **da empresa** , insira sua empresa.

    f. Na caixa de texto **endereço de email** , insira o email do usuário, como `b.simon\@contoso.com`.

    g. Na caixa de texto **criar um BlueJeans para o Azure ad Meeting I. D** , insira sua ID de reunião.

    h. Na caixa de texto **escolha uma senha do moderador** , insira sua senha.

    i. Clique em **continuar**.

    ![Adicionar usuário](./media/bluejeans-tutorial/ic785887.png "Adicionar Utilizador")

    J. Clique em **Adicionar usuário**.

> [!NOTE]
> Você pode usar qualquer outro BlueJeans para ferramentas de criação de conta de usuário do Azure AD ou APIs fornecidas pelo BlueJeans para o Azure AD para provisionar contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco BlueJeans para o Azure AD no painel de acesso, você deverá entrar automaticamente no BlueJeans para o Azure AD para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o BlueJeans para o Azure AD com o Azure AD](https://aad.portal.azure.com/)

