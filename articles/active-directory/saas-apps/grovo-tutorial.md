---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com Grovo | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Grovo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fb462905d1f8700214a0c4fc0c2d0bcb3e70a0f
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163518"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-grovo"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com Grovo

Neste tutorial, você aprenderá a integrar o Grovo com o Azure Active Directory (Azure AD). Ao integrar o Grovo ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Grovo.
* Habilite seus usuários a serem conectados automaticamente ao Grovo com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Grovo.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Grovo dá suporte ao **SP e** ao SSO iniciado pelo IDP
* O Grovo dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-grovo-from-the-gallery"></a>Adicionando o Grovo da Galeria

Para configurar a integração do Grovo ao Azure AD, você precisará adicionar o Grovo da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Grovo** na caixa de pesquisa.
1. Selecione **Grovo** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-grovo"></a>Configurar e testar o logon único do Azure AD para o Grovo

Configure e teste o SSO do Azure AD com o Grovo usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Grovo.

Para configurar e testar o SSO do Azure AD com o Grovo, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Grovo](#configure-grovo-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Grovo](#create-grovo-test-user)** – para ter um equivalente de B. Simon em Grovo que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Grovo** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:`https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **estado** de retransmissão, digite uma URL usando o seguinte padrão:`https://<subdomain>.grovo.com`

1. Clique em **definir URLs adicionais** e execute as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta, a URL de logon e o estado de retransmissão reais. Contate a [equipe de suporte ao cliente do Grovo](https://www.grovo.com/contact-us) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar Grovo** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Grovo.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Grovo**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-grovo-sso"></a>Configurar o SSO do Grovo

1. Em uma janela diferente do navegador da Web, entre no Grovo como administrador.

2. Vá para**integrações**do **administrador** > .
 
    ![Configuração do Grovo](./media/grovo-tutorial/tutorial_grovo_admin.png) 

3. Clique em **Configurar** na seção **SAML 2,0 iniciada pelo SP** .

    ![Configuração do Grovo](./media/grovo-tutorial/tutorial_grovo_setup.png)

4. Na janela pop-up **SAML 2,0 iniciada pelo SP** , execute as seguintes etapas:

    ![Configuração do Grovo](./media/grovo-tutorial/tutorial_grovo_saml.png)

    a. Na caixa de texto **ID da entidade** , Cole o valor do **identificador do Azure ad**que você copiou do portal do Azure.

    b. Na caixa de texto **ponto de extremidade de serviço de logon único** , Cole o valor da **URL de logon**copiado de portal do Azure.

    c. Selecione **Associação de ponto de extremidade de serviço de logon único** como `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Abra o **certificado codificado em base64** baixado em portal do Azure no bloco de notas, Cole-o na caixa de texto **chave pública** .

    e. Clique em **Seguinte**.

### <a name="create-grovo-test-user"></a>Criar usuário de teste do Grovo

Nesta seção, um usuário chamado B. Simon é criado em Grovo. O Grovo dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Grovo, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Grovo](https://www.grovo.com/contact-us).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Grovo no painel de acesso, você deverá ser conectado automaticamente ao Grovo para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Grovo com o Azure AD](https://aad.portal.azure.com/)