---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Spotinst | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5490ff6c6143dff258d74e013bb9d4c821aab625
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263290"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Spotinst

Neste tutorial, você aprenderá a integrar o Spotinst com o Azure Active Directory (Azure AD). Ao integrar o Spotinst ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Spotinst.
* Habilite seus usuários a serem conectados automaticamente ao Spotinst com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Spotinst.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Spotinst dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-spotinst-from-the-gallery"></a>Adicionando o Spotinst da Galeria

Para configurar a integração do Spotinst ao Azure AD, você precisará adicionar o Spotinst da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Spotinst** na caixa de pesquisa.
1. Selecione **Spotinst** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Configurar e testar o logon único do Azure AD para o Spotinst

Configure e teste o SSO do Azure AD com o Spotinst usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Spotinst.

Para configurar e testar o SSO do Azure AD com o Spotinst, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Spotinst](#configure-spotinst-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Spotinst](#create-spotinst-test-user)** – para ter um equivalente de B. Simon em Spotinst que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Spotinst** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    a. Marque **definir URLs adicionais**.

    b. Na caixa de texto **estado de retransmissão** , digite um valor: `<ID>`

1. Clique em **definir URLs adicionais** e execute as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite a url: `https://console.spotinst.com/auth/saml`

    > [!NOTE]
    > O valor do estado de retransmissão não é real. Você atualizará o valor do estado de retransmissão com o valor real do estado de retransmissão, que é explicado posteriormente no tutorial.

1. Clique em **Guardar**.

1. O aplicativo Spotinst espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo Spotinst espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | -----| --------------- |
    | E-mail | user.mail |
    | FirstName | user.givenname |
    | Apelido | User. sobrenome |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar Spotinst** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Spotinst.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Spotinst**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-spotinst-sso"></a>Configurar o SSO do Spotinst

1. Em uma janela diferente do navegador da Web, entre no Spotinst como um administrador de segurança.

2. Clique no **ícone de usuário** no lado superior direito da tela e clique em **configurações**.

    ![Configurações de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Clique na guia **segurança** na parte superior e, em seguida, selecione **provedores de identidade** e execute as seguintes etapas:

    ![Segurança do Spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Copie o valor do **estado de retransmissão** para sua instância e cole-o na caixa de texto **estado de retransmissão** na seção **configuração básica do SAML** em portal do Azure.

    b. Clique em **procurar** para carregar o arquivo XML de metadados que você baixou de portal do Azure

    c. Clique em **GUARDAR**.

### <a name="create-spotinst-test-user"></a>Criar usuário de teste do Spotinst

O objetivo desta seção é criar um usuário chamado Brenda Simon no Spotinst.

1. Se você tiver configurado o aplicativo no modo iniciado pelo **SP** , execute as seguintes etapas:

   a. Em uma janela diferente do navegador da Web, entre no Spotinst como um administrador de segurança.

   b. Clique no **ícone de usuário** no lado superior direito da tela e clique em **configurações**.

    ![Configurações de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Clique em **usuários** e selecione **Adicionar usuário**.

    ![Configurações de Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. Na seção Adicionar usuário, execute as seguintes etapas:

    ![Configurações de Spotinst](./media/spotinst-tutorial/adduser2.png)

    * Na caixa de texto **nome completo** , insira o nome completo do usuário, como **brendafernandes**.

    * Na caixa de texto **email** , insira o endereço de email do usuário, como `brittasimon\@contoso.com`.

    * Selecione os detalhes específicos da organização para a **função de organização, função de conta e contas**.

2. Se você tiver configurado o aplicativo no modo iniciado pelo **IDP** , não haverá nenhum item de ação para você nesta seção. O Spotinst dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Um novo usuário é criado durante uma tentativa de acessar o Spotinst, caso ele ainda não exista.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Spotinst no painel de acesso, você deverá ser conectado automaticamente ao Spotinst para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Spotinst com o Azure AD](https://aad.portal.azure.com/)

