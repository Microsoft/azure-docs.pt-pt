---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o OpenAthens | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fc2272a81672ea613bdcbe17c5381e99cafbff
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053200"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o OpenAthens

Neste tutorial, você aprenderá a integrar o OpenAthens com o Azure Active Directory (Azure AD). Ao integrar o OpenAthens ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao OpenAthens.
* Habilite seus usuários a serem conectados automaticamente ao OpenAthens com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do OpenAthens.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* OpenAthens dá suporte ao SSO iniciado pelo **IDP**
* O OpenAthens dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-openathens-from-the-gallery"></a>Adicionando o OpenAthens da Galeria

Para configurar a integração do OpenAthens ao Azure AD, você precisará adicionar o OpenAthens da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **OpenAthens** na caixa de pesquisa.
1. Selecione **OpenAthens** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Configurar e testar o logon único do Azure AD para o OpenAthens

Configure e teste o SSO do Azure AD com o OpenAthens usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no OpenAthens.

Para configurar e testar o SSO do Azure AD com o OpenAthens, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do OpenAthens](#configure-openathens-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do OpenAthens](#create-openathens-test-user)** – para ter um equivalente de B. Simon em OpenAthens que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **OpenAthens** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , carregue o **arquivo de metadados do provedor de serviços**, as etapas para as quais são mencionadas mais adiante neste tutorial.

    a. Clique em **carregar arquivo de metadados**.

    ![openathens carregar metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e clique em **carregar**.

    ![Openathens procurar metadados de carregamento](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, o valor do **identificador** obter automaticamente preenchido na caixa de texto seção de **configuração básica do SAML** :

    ![Informações de logon único de domínio e URLs do OpenAthens](common/idp-identifier.png)

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/metadataxml.png)

1. Na seção **Configurar OpenAthens** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao OpenAthens.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **OpenAthens**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-openathens-sso"></a>Configurar o SSO do OpenAthens

1. Em uma janela diferente do navegador da Web, entre no site da empresa do OpenAthens como um administrador.

1. Selecione **conexões** na lista na guia **Gerenciamento** .

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Selecione **SAML 1.1/2.0**e, em seguida, selecione o botão **Configurar** .

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. Para adicionar a configuração, selecione o botão **procurar** para carregar o arquivo Metadata. XML que você baixou do portal do Azure e, em seguida, selecione **Adicionar**.

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Execute as etapas a seguir na guia **detalhes** .

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Em **mapeamento de nome de exibição**, selecione **usar atributo**.

    b. Na caixa de texto **atributo de nome de exibição** , insira o valor `http://schema.microsoft.com/identity/claims/displayname`.

    c. Em **mapeamento de usuário exclusivo**, selecione **usar atributo**.

    d. Na caixa de texto **atributo de usuário exclusivo** , insira o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Em **status**, marque todas as três caixas de seleção.

    f. Em **criar contas locais**, selecione **automaticamente**.

    g. Selecione **salvar alterações**.

    h. Na guia **</>** terceira parte confiável, copie a **URL de metadados** e abra-a no navegador para baixar o arquivo XML de **metadados do SP** . Carregue este arquivo de metadados do SP na seção de **configuração básica do SAML** no Azure AD.

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Criar usuário de teste do OpenAthens

Nesta seção, um usuário chamado Brenda Simon é criado em OpenAthens. O OpenAthens dá suporte ao **provisionamento de usuário just-in-time**, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no OpenAthens, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do OpenAthens no painel de acesso, você deverá ser conectado automaticamente ao OpenAthens para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o OpenAthens com o Azure AD](https://aad.portal.azure.com/)