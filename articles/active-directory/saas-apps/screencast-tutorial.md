---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com O screencast-O-O-O | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e O screencast-O-O-O.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc887e95b6fa6f8b17fbbb3dbaae5105385a07fa
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132151"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com O screencast-O-O

Neste tutorial, você aprenderá a integrar o screencast-O-O-O com o Azure Active Directory (Azure AD). Ao integrar o screencast-O-O ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao screencast-O-O-O.
* Habilite seus usuários a serem automaticamente conectados ao screencast-O-O-O com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do screencast-O-O.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Screencast-O-O-O é compatível com SSO iniciado por **SP**
* Screencast-O-O-O é compatível com O provisionamento **de usuário just in time**

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Adicionando screencast-O-O-o da Galeria

Para configurar a integração do screencast-O-Phone ao Azure AD, você precisa adicionar o screencast-O-Phone da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **screencast-O-O-** o na caixa de pesquisa.
1. Selecione **screencast-O-O** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Configurar e testar O logon único do Azure AD para O screencast-O-O

Configure e teste o SSO do Azure AD com O screencast-O-O usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no screencast-O-O-O.

Para configurar e testar o SSO do Azure AD com O screencast-O-O-O, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure o screencast-O-O SSO](#configure-screencast-o-matic-sso)** -para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do screencast-o-o-o](#create-screencast-o-matic-test-user)** -para ter um equivalente de B. Simon no screencast-O-O-O-r vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **screencast-O-O** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do screencast-O-O](mailto:support@screencast-o-matic.com) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar screencast-O-O-** o, copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao screencast-O-O-O.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **screencast-O-O**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-screencast-o-matic-sso"></a>Configurar O SSO do screencast-O-O

1. Para automatizar a configuração no screencast-O-Phone, você precisa instalar a **extensão do navegador de entrada segura de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar screencast-O-O** para direcionar você para o aplicativo screencast-O-o. A partir daí, forneça as credenciais de administrador para entrar no screencast-O-O. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-11.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o screencast-O-O manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do screencast-O-O como administrador e execute as seguintes etapas:

1. Clique em **assinatura**.

    ![A assinatura](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. Na seção **página de acesso** , clique em **configuração**.

    ![O acesso](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. Na **página Configurar acesso**, execute as etapas a seguir.

1. Na seção **URL de acesso** , digite InstanceName na caixa de texto especificada.

    ![O acesso](./media/screencast-tutorial/tutorial_screencast_access.png)

1. Selecione **exigir usuário de domínio** na seção **restrição de usuário SAML (opcional)** .

1. Em **carregar arquivo XML de metadados IDP**, clique em **escolher arquivo** para carregar os metadados que você baixou de portal do Azure.

1. Clique em **OK**.

    ![O acesso](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Criar usuário de teste do screencast-O-O-

Nesta seção, um usuário chamado Brenda Simon é criado no screencast-O-O. O screencast-O-O é compatível com O provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no screencast-O-O-O, um novo será criado após a autenticação. Se você precisar criar um usuário manualmente, entre em contato com [a equipe de suporte ao cliente do screencast-O-O](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco screencast-O-O no painel de acesso, você deverá entrar automaticamente no screencast-O-O-O para o qual configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente O screencast-O-O com O Azure AD](https://aad.portal.azure.com/)