---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com inspeção por cores | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e observar por cores.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d0810c393cf9b6060f456c5ff01d03673b62a9
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861299"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com inspeção por cores

Neste tutorial, você aprenderá a integrar o Watch por cores com o Azure Active Directory (Azure AD). Ao integrar o Watch por cores com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Watch por cores.
* Habilite seus usuários a serem conectados automaticamente para assistir por cores com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Observe a assinatura habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Inspecionar por cores dá suporte ao SP iniciado pelo **IDP**

## <a name="adding-watch-by-colors-from-the-gallery"></a>Adicionando inspeção por cores da Galeria

Para configurar a integração do Watch by Colors ao Azure AD, você precisará adicionar a inspeção por cores da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **inspeção por cores** na caixa de pesquisa.
1. Selecione **inspecionar por cores** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Configurar e testar o logon único do Azure AD para inspeção por cores

Configure e teste o SSO do Azure AD com o Watch por cores usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em assistir por cores.

Para configurar e testar o SSO do Azure AD com o Watch por cores, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO de inspeção por cores](#configure-watch-by-colors-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste inspeção por cores](#create-watch-by-colors-test-user)** – para ter um equivalente de B. Simon em inspeção por cores que esteja vinculada à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **inspecionar por cores** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração** básica do SAML, o aplicativo é pré-configurado no modo iniciado pelo **IDP** e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL:`https://app.colorscorporation.com/login`

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Watch por cores.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **inspecionar por cores**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-watch-by-colors-sso"></a>Configurar o SSO de inspeção por cores

1. Para automatizar a configuração em inspeção por cores, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar Inspeção por cores** para direcionar você ao aplicativo inspecionar por cores. A partir daí, forneça as credenciais de administrador para entrar em observação por cores. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-5.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar a inspeção por cores manualmente, abra uma nova janela do navegador da Web e entre no site da empresa Watch with Colors como administrador e execute as seguintes etapas:

4. No canto superior direito da página, clique em **perfil** > **configurações** > de conta**SSO (logon único)** .

    ![Inspecionar por configuração de cores](./media/watch-by-colors-tutorial/config01.png)

5. Na página **SSO (logon único)** , execute as seguintes etapas:

    ![Inspecionar por configuração de cores](./media/watch-by-colors-tutorial/config02.png)

    a. Alterne **habilitar SAML** para **ativado**.

    b. Na caixa de texto **URL** , Cole a **URL de metadados de Federação**copiada de portal do Azure.

    c. Clique em **importar**, os campos a seguir serão preenchidos automaticamente na página.

    d. Clique em **Guardar**.

### <a name="create-watch-by-colors-test-user"></a>Criar usuário de teste inspeção por cores

Para permitir que os usuários do Azure AD entrem para assistir por cores, eles devem ser provisionados em inspeção por cores. Em inspeção por cores, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre para assistir por cores como um administrador de segurança.

1. No canto superior direito da página, clique em **perfil** > **usuários** > **Adicionar usuário**.

    ![Inspecionar por configuração de cores](./media/watch-by-colors-tutorial/config03.png)

1. Na página **detalhes do usuário** , execute as seguintes etapas:

    ![Inspecionar por configuração de cores](./media/watch-by-colors-tutorial/config04.png)

    a. Na caixa de texto **nome** , insira o nome do usuário como **B**.

    b. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.

    c. Na caixa de texto **email** , insira o email do usuário `B.Simon@contoso.com`como.

    d. Na caixa de texto **senha** , digite a senha.

    e. Selecione **permissões de conta** de acordo com sua organização.

    f. Clique em **Guardar**.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco inspecionar por cores no painel de acesso, você deverá ser conectado automaticamente à inspeção por cores para as quais você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente assistir por cores com o Azure AD](https://aad.portal.azure.com/)

