---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com workteam | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b3b9e48ed5c02834f1e6575ca79d49ff2e0bd7
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172233"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com workteam

Neste tutorial, você aprenderá a integrar o workteam com o Azure Active Directory (Azure AD). Ao integrar o workteam ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao workteam.
* Habilite seus usuários a serem conectados automaticamente ao workteam com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do workteam.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O workteam dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-workteam-from-the-gallery"></a>Adicionando o workteam da Galeria

Para configurar a integração do workteam ao Azure AD, você precisará adicionar o workteam da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **workteam** na caixa de pesquisa.
1. Selecione **workteam** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workteam"></a>Configurar e testar o logon único do Azure AD para o workteam

Configure e teste o SSO do Azure AD com o workteam usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no workteam.

Para configurar e testar o SSO do Azure AD com o workteam, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do workteam](#configure-workteam-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do workteam](#create-workteam-test-user)** – para ter um equivalente de B. Simon em workteam que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **workteam** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração** básica do SAML, o aplicativo é pré-configurado no modo iniciado pelo **IDP** e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL:`https://app.workte.am`

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar workteam** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao workteam.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **workteam**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="configure-workteam-sso"></a>Configurar o SSO do workteam

1. Para automatizar a configuração no workteam, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Setup workteam** irá direcioná-lo para o aplicativo workteam. A partir daí, forneça as credenciais de administrador para entrar no workteam. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o workteam manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do workteam como administrador e execute as seguintes etapas:

4. No canto superior direito, clique no **logotipo do perfil** e, em seguida, clique em **configurações da organização**. 

    ![Configurações de workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

5. Na seção **autenticação** , clique no **logotipo configurações**.

     ![Workteam Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

6. Na página **configurações do SAML** , execute as seguintes etapas:

     ![SAML do workteam](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Selecione o **IDP do SAML** como **AD do Azure**.

    b. Na caixa de texto **URL do serviço de logon único do SAML** , Cole o valor da URL de **logon**copiado do portal do Azure.

    c. Na caixa de texto **ID da entidade SAML** , Cole o valor do **identificador do Azure ad**copiado do portal do Azure.

    d. No bloco de notas, abra o **certificado codificado base 64** que você baixou do portal do Azure, copie seu conteúdo e, em seguida, Cole-o na caixa **certificado de autenticação SAML (Base64)** .

    e. Clique em **OK**.

### <a name="create-workteam-test-user"></a>Criar usuário de teste do workteam

Para permitir que os usuários do Azure AD entrem no workteam, eles devem ser provisionados no workteam. No workteam, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no workteam como um administrador de segurança.

2. Na parte superior da página **configurações da organização** , clique em **usuários** e em **novo usuário**.

    ![Usuário workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Na página **novo funcionário** , execute as seguintes etapas:

    ![Novo usuário do workteam](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Na caixa de texto **nome** , insira o nome do usuário, como **B. Simon**.

    b. Na caixa de texto **email** , insira o email do usuário `B.Simon\@contoso.com`como.

    c. Clique em **OK**.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do workteam no painel de acesso, você deverá ser conectado automaticamente ao workteam para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o workteam com o Azure AD](https://aad.portal.azure.com/)

