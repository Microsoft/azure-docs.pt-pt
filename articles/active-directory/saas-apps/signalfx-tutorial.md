---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o SignalFx | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea81f0046d7f73d845ed49325a3d621e6b7735e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443271"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o SignalFx

Neste tutorial, você aprenderá a integrar o SignalFx com o Azure Active Directory (Azure AD). Ao integrar o SignalFx ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SignalFx.
* Habilite seus usuários a serem conectados automaticamente ao SignalFx com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SignalFx.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* SignalFx dá suporte ao SSO iniciado pelo **IDP**
* O SignalFx dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-signalfx-from-the-gallery"></a>Adicionando o SignalFx da Galeria

Para configurar a integração do SignalFx ao Azure AD, você precisará adicionar o SignalFx da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SignalFx** na caixa de pesquisa.
1. Selecione **SignalFx** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-signalfx"></a>Configurar e testar o logon único do Azure AD para o SignalFx

Configure e teste o SSO do Azure AD com o SignalFx usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SignalFx.

Para configurar e testar o SSO do Azure AD com o SignalFx, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do SignalFx](#configure-signalfx-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do SignalFx](#create-signalfx-test-user)** – para ter um equivalente de B. Simon em SignalFx que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SignalFx** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL: `https://api.signalfx.com/v1/saml/metadata`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE]
    > O valor anterior não é um valor real. Você atualiza o valor com a URL de resposta real, que é explicada posteriormente no tutorial.

1. O aplicativo SignalFx espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo SignalFx espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de origem|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | Usuário. email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | User. sobrenome    |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar SignalFx** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao SignalFx.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SignalFx**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-signalfx-sso"></a>Configurar o SSO do SignalFx

1. Entre no site da empresa do SignalFx como administrador.

1. No SignalFx, na parte superior, clique em **integrações** para abrir a página integrações.

    ![Integração do SignalFx](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Clique no bloco **Azure Active Directory** na seção **serviços de logon** .

    ![SAML do SignalFx](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Clique em **nova integração** e, na guia **instalar** , execute as seguintes etapas:

    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. No tipo de caixa de texto **nome** , um novo nome de integração, como o **SSO do SAML do OurOrgName**.

    b. Copie o valor da **ID de integração** e acrescente à **URL de resposta** no lugar de `<integration ID>` na caixa de texto URL de **resposta** da seção **configuração básica do SAML** em portal do Azure.

    c. Clique em **carregar arquivo** para carregar o **certificado codificado em Base64** baixado de portal do Azure na caixa de texto **certificado** .

    d. Na caixa de texto **URL do emissor** , Cole o valor do **identificador do Azure ad**copiado do portal do Azure.

    e. Na caixa de texto **URL de metadados** , Cole a **URL de logon** que você copiou do portal do Azure.

    f. Clique em **Guardar**.

### <a name="create-signalfx-test-user"></a>Criar usuário de teste do SignalFx

O objetivo desta seção é criar um usuário chamado Brenda Simon no SignalFx. O SignalFx dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há nenhum item de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o SignalFx, caso ele ainda não exista.

Quando um usuário entra no SignalFx do SSO do SAML pela primeira vez, a [equipe de suporte do SignalFx](mailto:kmazzola@signalfx.com) envia um email contendo um link para o qual eles devem clicar para se autenticar. Isso ocorrerá apenas na primeira vez que o usuário entrar; tentativas de logon subsequentes não exigirão validação de email.

> [!Note]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do SignalFx](mailto:kmazzola@signalfx.com)

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do SignalFx no painel de acesso, você deverá ser conectado automaticamente ao SignalFx para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SignalFx com o Azure AD](https://aad.portal.azure.com/)