---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com o tableau Server | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b07c6d6317d8ca284008d271f507b965414bfbc1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257506"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o tableau Server

Neste tutorial, você aprenderá a integrar o tableau Server ao Azure Active Directory (Azure AD). Ao integrar o tableau Server ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao tableau Server.
* Habilite seus usuários a serem conectados automaticamente ao tableau Server com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do tableau Server.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O servidor tableau dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-tableau-server-from-the-gallery"></a>Adicionando o tableau Server da Galeria

Para configurar a integração do tableau Server ao Azure AD, você precisará adicionar o tableau Server da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **tableau Server** na caixa de pesquisa.
1. Selecione **tableau Server** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Configurar e testar o logon único do Azure AD para o tableau Server

Configure e teste o SSO do Azure AD com o tableau Server usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no tableau Server.

Para configurar e testar o SSO do Azure AD com o tableau Server, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do tableau Server](#configure-tableau-server-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do tableau Server](#create-tableau-server-test-user)** – para ter um equivalente de B. Simon no tableau Server que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **tableau Server** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://azure.<domain name>.link`

    b. Na caixa **identificador** , digite uma URL usando o seguinte padrão:`https://azure.<domain name>.link`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Os valores anteriores não são valores reais. Atualize os valores com a URL e o identificador reais na página de configuração do servidor tableau, que é explicada posteriormente no tutorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o servidor do tableau** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao tableau Server.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **tableau Server**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-tableau-server-sso"></a>Configurar SSO do tableau Server

1. Para que o SSO seja configurado para seu aplicativo, você precisa entrar no seu locatário do tableau Server como administrador.

2. Na guia **configuração** , selecione **identidade do usuário & acesso**e, em seguida, selecione a guia método de **autenticação** .

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na página **configuração** , execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Para o **método de autenticação**, selecione SAML.

    b. Marque a caixa de seleção **habilitar autenticação SAML para o servidor**.

    c. URL de retorno do servidor tableau — a URL que os usuários do tableau Server acessarão, como <http://tableau_server>. O `http://localhost` uso do não é recomendado. Não há suporte para o uso de uma URL com uma barra `http://tableau_server/`à direita (por exemplo,). Copie a **URL de retorno do servidor tableau** e cole-a na caixa de texto **URL de logon** na seção **configuração básica do SAML** no portal do Azure

    d. ID da entidade SAML — a ID da entidade identifica exclusivamente a instalação do servidor tableau para o IdP. Você pode inserir a URL do servidor do tableau novamente aqui, se desejar, mas não precisa ser a URL do servidor do tableau. Copie a **ID da entidade SAML** e cole-a na caixa de texto **identificador** na seção **configuração básica do SAML** no portal do Azure

    e. Clique no **arquivo baixar metadados XML** e abra-o no aplicativo editor de texto. Localize a URL do serviço do consumidor de asserção com http post e o índice 0 e copie a URL. Agora, Cole-o na caixa de texto **URL de resposta** na seção **configuração básica do SAML** no portal do Azure

    f. Localize o arquivo de metadados de Federação baixado de portal do Azure e, em seguida, carregue-o no **arquivo de metadados do IDP SAML**.

    g. Insira os nomes dos atributos que o IdP usa para manter os nomes de usuário, nomes de exibição e endereços de email.

    h. Clique em **Guardar**

    > [!NOTE]
    > O cliente precisa carregar qualquer certificado na configuração de SSO do SAML do tableau Server e ele será ignorado no fluxo de SSO. Se precisar de ajuda para configurar o SAML no tableau Server, consulte este artigo [Configurar SAML](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm).

### <a name="create-tableau-server-test-user"></a>Criar usuário de teste do tableau Server

O objetivo desta seção é criar um usuário chamado B. Simon no tableau Server. Você precisa provisionar todos os usuários no servidor tableau.

Esse nome de usuário deve corresponder ao valor que você configurou no atributo personalizado de **nome**de usuário do Azure AD. Com o mapeamento correto, a integração deve funcionar Configurando o logon único do Azure AD.

> [!NOTE]
> Se precisar criar um usuário manualmente, você precisará entrar em contato com o administrador do servidor tableau em sua organização.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do tableau Server no painel de acesso, você deverá ser conectado automaticamente ao servidor do tableau para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o tableau Server com o Azure AD](https://aad.portal.azure.com/)