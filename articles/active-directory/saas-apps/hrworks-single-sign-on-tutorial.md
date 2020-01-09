---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o logon único do HRworks | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o logon único do HRworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b20ce5f754333aec78513e32901b0608f8bee3b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638767"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o logon único do HRworks

Neste tutorial, você aprenderá a integrar o logon único do HRworks com o Azure Active Directory (Azure AD). Ao integrar o logon único do HRworks ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao logon único do HRworks.
* Habilite seus usuários a serem conectados automaticamente ao logon único do HRworks com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do HRworks logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O logon único do HRworks dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Adicionando o logon único do HRworks por meio da Galeria

Para configurar a integração do logon único do HRworks ao Azure AD, você precisará adicionar o logon único do HRworks por meio da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **logon único do HRworks** na caixa de pesquisa.
1. Selecione **logon único do HRworks** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Configurar e testar o logon único do Azure AD para o logon único do HRworks

Configure e teste o SSO do Azure AD com o logon único do HRworks usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no logon único do HRworks.

Para configurar e testar o SSO do Azure AD com o logon único do HRworks, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO de logon único do HRworks](#configure-hrworks-single-sign-on-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do logon único do HRworks](#create-hrworks-single-sign-on-test-user)** – para ter um equivalente de B. Simon no logon único do HRworks que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **logon único do HRworks** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente de logon único do HRworks](mailto:nadja.sommerfeld@hrworks.de) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar logon único do HRworks** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao logon único do HRworks.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **logon único do HRworks**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-hrworks-single-sign-on-sso"></a>Configurar SSO de logon único do HRworks

1. Para automatizar a configuração no logon único do HRworks, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o logon único do HRworks** , você será direcionado para o aplicativo de logon único do HRworks. A partir daí, forneça as credenciais de administrador para entrar no logon único do HRworks. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-4.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o logon único do HRworks manualmente, abra uma nova janela do navegador da Web e entre no site da empresa de logon único do HRworks como administrador e execute as seguintes etapas:

1. Clique em **administrador** > **noções básicas** > **segurança** > **logon único** no lado esquerdo da barra de menus e execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Marque a caixa **usar logon único** .

    b. Selecione **metadados XML** como **método de entrada de metadados**.

    c. Selecione o **identificador NameID individual** como o **valor para NameID**.

    d. No bloco de notas, abra o XML de metadados que você baixou do portal do Azure, copie seu conteúdo e cole-o na caixa de texto **metadados** .

    e. Clique em **Guardar**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Criar usuário de teste do logon único do HRworks

Para habilitar os usuários do Azure AD, entre no HRworks logon único, eles devem ser provisionados no logon único do HRworks. No logon único do HRworks, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no HRworks logon único como administrador.

1. Clique em **administrador** > **pessoas** > **pessoas** > **nova pessoa** do lado esquerdo da barra de menus.

     ![Configurar o início de sessão único](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. No pop-up, clique em **Avançar**.

    ![Configurar o início de sessão único](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. No pop-up **criar nova pessoa com o país para os termos legais** , preencha os respectivos detalhes como **nome**, **sobrenome** e clique em **criar**.

    ![Configurar o início de sessão único](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco logon único do HRworks no painel de acesso, você deverá entrar automaticamente no logon único do HRworks para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o logon único do HRworks com o Azure AD](https://aad.portal.azure.com/)