---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o AcquireIO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AcquireIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17cc6df651a82b416e670ee5ca5683c428eac6b5
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596273"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o AcquireIO

Neste tutorial, você aprenderá a integrar o AcquireIO com o Azure Active Directory (Azure AD). Ao integrar o AcquireIO ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao AcquireIO.
* Habilite seus usuários a serem conectados automaticamente ao AcquireIO com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do AcquireIO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* AcquireIO dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-acquireio-from-the-gallery"></a>Adicionando o AcquireIO da Galeria

Para configurar a integração do AcquireIO ao Azure AD, você precisará adicionar o AcquireIO da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **AcquireIO** na caixa de pesquisa.
1. Selecione **AcquireIO** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Configurar e testar o logon único do Azure AD para o AcquireIO

Configure e teste o SSO do Azure AD com o AcquireIO usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no AcquireIO.

Para configurar e testar o SSO do Azure AD com o AcquireIO, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do AcquireIO](#configure-acquireio-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do AcquireIO](#create-acquireio-test-user)** – para ter um equivalente de B. Simon em AcquireIO que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **AcquireIO** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > O valor não é real. Você obterá a URL de resposta real, que será explicada posteriormente na seção **Configurar AcquireIO** do tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar AcquireIO** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao AcquireIO.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **AcquireIO**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-acquireio-sso"></a>Configurar o SSO do AcquireIO

1. Para automatizar a configuração no AcquireIO, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar AcquireIO**, que direciona você para o aplicativo AcquireIO. A partir daí, forneça as credenciais de administrador para entrar no AcquireIO. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o AcquireIO manualmente, em uma janela diferente do navegador da Web, entre no AcquireIO como administrador.

1. No lado esquerdo do menu, clique em **App Store**.

     ![Configuração do AcquireIO](./media/acquireio-tutorial/config01.png)

1. Role para baixo até **Active Directory** e clique em **instalar**.

    ![Configuração do AcquireIO](./media/acquireio-tutorial/config02.png)

1. No pop-up Active Directory, execute as seguintes etapas:

    ![Configuração do AcquireIO](./media/acquireio-tutorial/config03.png)

    a. Clique em **copiar** para copiar a URL de resposta para sua instância e cole-a na caixa de texto **URL de resposta** na seção **configuração básica do SAML** em portal do Azure.

    b. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon**copiado de portal do Azure.

    c. Abra o certificado codificado em base64 no bloco de notas, copie seu conteúdo e cole-o na caixa de texto **certificado X. 509** .

    d. Clique em **conectar agora**.

### <a name="create-acquireio-test-user"></a>Criar usuário de teste do AcquireIO

Para permitir que os usuários do Azure AD entrem no AcquireIO, eles devem ser provisionados no AcquireIO. No AcquireIO, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Em uma janela diferente do navegador da Web, entre no AcquireIO como um administrador.

1. No lado esquerdo do menu, clique em **perfis** e navegue até **Adicionar perfil**.

     ![Configuração do AcquireIO](./media/acquireio-tutorial/config04.png)

1. No pop-up **Adicionar cliente** , execute as seguintes etapas:

    ![Configuração do AcquireIO](./media/acquireio-tutorial/config05.png)

    a. Na caixa de texto **nome** , digite o nome do usuário, como **B. Simon**.

    b. Na caixa de texto **email** , insira o email do usuário, como **B.simon@contoso.com** .

    c. Clique em **Enviar**.

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do AcquireIO no painel de acesso, você deverá ser conectado automaticamente ao AcquireIO para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o AcquireIO com o Azure AD](https://aad.portal.azure.com/)
