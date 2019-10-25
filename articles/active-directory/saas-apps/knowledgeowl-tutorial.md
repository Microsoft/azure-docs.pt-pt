---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o KnowledgeOwl | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc7d481b757a76ba65e0c78a93bde1bc58ace7cc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791644"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o KnowledgeOwl

Neste tutorial, você aprenderá a integrar o KnowledgeOwl com o Azure Active Directory (Azure AD). Ao integrar o KnowledgeOwl ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao KnowledgeOwl.
* Habilite seus usuários a serem conectados automaticamente ao KnowledgeOwl com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do KnowledgeOwl.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O KnowledgeOwl dá suporte ao **SP e** ao SSO iniciado pelo IDP
* O KnowledgeOwl dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-knowledgeowl-from-the-gallery"></a>Adicionando o KnowledgeOwl da Galeria

Para configurar a integração do KnowledgeOwl ao Azure AD, você precisará adicionar o KnowledgeOwl da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **KnowledgeOwl** na caixa de pesquisa.
1. Selecione **KnowledgeOwl** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Configurar e testar o logon único do Azure AD para o KnowledgeOwl

Configure e teste o SSO do Azure AD com o KnowledgeOwl usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no KnowledgeOwl.

Para configurar e testar o SSO do Azure AD com o KnowledgeOwl, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do KnowledgeOwl](#configure-knowledgeowl-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do KnowledgeOwl](#create-knowledgeowl-test-user)** – para ter um equivalente de B. Simon em KnowledgeOwl que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **KnowledgeOwl** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:
    
    | | |
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

    > [!NOTE]
    > Esses valores não são reais. Você precisará atualizar esse valor do identificador real, da URL de resposta e da URL de logon, que é explicada posteriormente no tutorial.

1. O aplicativo KnowledgeOwl espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo KnowledgeOwl espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem | Namespace |
    | ------------ | -------------------- | -----|
    | ssoid | User. mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize **certificado (bruto)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificateraw.png)

1. Na seção **Configurar KnowledgeOwl** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao KnowledgeOwl.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **KnowledgeOwl**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-knowledgeowl-sso"></a>Configurar o SSO do KnowledgeOwl

1. Em uma janela diferente do navegador da Web, entre no site da empresa do KnowledgeOwl como um administrador.

1. Clique em **configurações** e, em seguida, selecione **segurança**.

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Role até **integração de SSO do SAML** e execute as seguintes etapas:

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

    a. Selecione **habilitar SSO do SAML**.

    b. Copie o valor da **ID da entidade SP** e cole-o no **identificador (ID da entidade)** na seção **configuração básica do SAML** no portal do Azure.

    c. Copie o valor da **URL de logon do SP** e cole-o nas caixas de **entrada URL de logon e URL de resposta** na seção **configuração básica do SAML** no portal do Azure.

    d. Na caixa de texto **EntityId do IDP** , Cole o valor do **identificador do Azure ad** copiado do portal do Azure.

    e. Na caixa de texto **URL de logon do IDP** , Cole o valor da URL de **logon** copiado do portal do Azure.

    f. Na caixa de texto **URL de logout do IDP** , Cole o valor da URL de **logout** copiado do portal do Azure

    g. Carregue o certificado baixado do portal do Azure clicando no **carregar certificado IDP**.

    h. Clique em **mapear atributos SAML** para mapear atributos e execute as seguintes etapas:

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Insira `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` na caixa de texto **ID de SSO**
    * Insira `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` na caixa de texto **nome de usuário/email** .
    * Insira `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` na caixa de texto **nome** .
    * Insira `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` na caixa de texto **sobrenome** .
    * Clicar em **Guardar**

    i. Clique em **Guardar** na parte inferior da página.

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>Criar usuário de teste do KnowledgeOwl

Nesta seção, um usuário chamado B. Simon é criado em KnowledgeOwl. O KnowledgeOwl dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no KnowledgeOwl, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do KnowledgeOwl no painel de acesso, você deverá ser conectado automaticamente ao KnowledgeOwl para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o KnowledgeOwl com o Azure AD](https://aad.portal.azure.com/)