---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o PureCloud by Genesys | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PureCloud by Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfaa5a4ebb8bc633dc49db08698aec31de9436d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373129"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o PureCloud by Genesys

Neste tutorial, você aprenderá a integrar o PureCloud by Genesys ao Azure Active Directory (Azure AD). Ao integrar o PureCloud by Genesys ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao PureCloud by Genesys.
* Habilite seus usuários a serem conectados automaticamente ao PureCloud by Genesys com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do PureCloud by Genesys.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* PureCloud by Genesys dá suporte ao **SP e** ao SSO iniciado pelo IDP

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Adicionando o PureCloud by Genesys da Galeria

Para configurar a integração do PureCloud by Genesys ao Azure AD, você precisará adicionar o PureCloud by Genesys da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **PureCloud by Genesys** na caixa de pesquisa.
1. Selecione **PureCloud by Genesys** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Configurar e testar o logon único do Azure AD para o PureCloud by Genesys

Configure e teste o SSO do Azure AD com o PureCloud by Genesys usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no PureCloud by Genesys.

Para configurar e testar o SSO do Azure AD com o PureCloud by Genesys, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o PureCloud by Genesys SSO](#configure-purecloud-by-genesys-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** – para ter um equivalente de B. Simon no PureCloud by Genesys que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **PureCloud by Genesys** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL de acordo com a sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Na caixa de texto **URL de resposta** , digite uma URL de acordo com a sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL de acordo com a sua região:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. O aplicativo PureCloud by Genesys espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo PureCloud by Genesys espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | E-mail | User. userprinicipalname |
    | OrganizationName | `Your organization name` |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar PureCloud por Genesys** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao PureCloud by Genesys.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **PureCloud by Genesys**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-purecloud-by-genesys-sso"></a>Configurar PureCloud por SSO do Genesys

1. Em uma janela diferente do navegador da Web, entre no PureCloud by Genesys como administrador.

1. Clique em **admin** na parte superior e navegue até **logon único** em **integrações**.

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Alterne para a guia **ADFS/AD do Azure (Premium)** e execute as seguintes etapas:

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Clique em **procurar** para carregar o certificado codificado em base 64 que você baixou do portal do Azure para o **certificado do ADFS**.

    b. Na caixa de texto **URI do emissor do ADFS** , Cole o valor do identificador do **Azure ad** que você copiou do portal do Azure.

    c. Na caixa de texto **URI de destino** , Cole o valor da **URL de logon** que você copiou do portal do Azure.

    d. Para o valor do **identificador** de terceira parte confiável, você precisa ir para a Portal do Azure, na página de integração de aplicativos do **PureCloud by Genesys** , clique na guia **Propriedades** e copie o valor da **ID do aplicativo** . Cole-o na caixa de texto **identificador de terceira parte confiável** . 

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Clicar em **Guardar**

### <a name="create-purecloud-by-genesys-test-user"></a>Criar PureCloud por usuário de teste do Genesys

Para permitir que os usuários do Azure AD entrem no PureCloud by Genesys, eles devem ser provisionados no PureCloud by Genesys. No PureCloud by Genesys, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no PureCloud by Genesys como administrador.

1. Clique em **admin** na parte superior e navegue para **pessoas** em **pessoas & permissões**.

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Na página pessoas, clique em **Adicionar pessoa**.

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure04.png)

1. No pop-up **adicionar pessoas ao organização** , execute as seguintes etapas:

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Na caixa de texto **nome completo** , digite o nome do usuário, como **B. Simon**.

    b. Na caixa de texto **email** , insira o email do usuário, como **b.Simon\@contoso.com**.

    c. Clique em **Criar**.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco PureCloud by Genesys no painel de acesso, você deverá entrar automaticamente no PureCloud by Genesys para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o PureCloud by Genesys com o Azure AD](https://aad.portal.azure.com/)