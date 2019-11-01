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
ms.openlocfilehash: 779328f4c21afb4392663e6f8840749ea505c529
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242432"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o PureCloud by Genesys

Neste tutorial, você aprenderá a integrar o PureCloud by Genesys ao Azure Active Directory (Azure AD). Depois de fazer isso, você pode:

* Use o Azure AD para controlar quais usuários podem acessar o PureCloud by Genesys.
* Habilite seus usuários a serem conectados automaticamente ao PureCloud by Genesys com suas contas do Azure AD.
* Gerencie suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do PureCloud by Genesys.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O PureCloud by Genesys dá suporte a **SP e**a SSO iniciados pelo IDP.

> [!NOTE]
> Como a ID desse aplicativo é um valor de cadeia de caracteres fixa, apenas uma instância pode ser configurada em um locatário.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Adicionando o PureCloud by Genesys da Galeria

Para configurar a integração do PureCloud by Genesys ao Azure AD, você deve adicionar o PureCloud by Genesys da Galeria à sua lista de aplicativos SaaS gerenciados. Para tal, siga estes passos:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou usando uma conta Microsoft pessoal.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Vá para **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **PureCloud by Genesys** na caixa de pesquisa.
1. Selecione **PureCloud by Genesys** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Configurar e testar o logon único do Azure AD para o PureCloud by Genesys

Configure e teste o SSO do Azure AD com o PureCloud by Genesys usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você deve estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no PureCloud by Genesys.

Para configurar e testar o SSO do Azure AD com o PureCloud by Genesys, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
    1. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
    1. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure o PureCloud by SSO do Genesys](#configure-purecloud-by-genesys-sso)** para configurar as configurações de logon único no lado do aplicativo.
    1. **[Crie um usuário de teste do PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** para ter um equivalente de B. Simon em PureCloud by Genesys que esteja vinculado à representação de usuário do Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Para habilitar o SSO do Azure AD no portal do Azure, siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **PureCloud by Genesys** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , selecione o ícone de caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP**, insira os valores para os seguintes campos:

    a. Na caixa **identificador** , insira uma URL que corresponda à sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Na caixa **URL de resposta** , insira uma URL que corresponda à sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Selecione **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa **URL de logon** , insira uma URL que corresponda à sua região:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. O aplicativo PureCloud by Genesys espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão:

    ![imagem](common/default-attributes.png)

1. Além disso, o aplicativo PureCloud by Genesys espera que mais alguns atributos sejam passados de volta na resposta SAML, conforme mostrado na tabela a seguir. Esses atributos também são preenchidos previamente, mas você pode examiná-los conforme necessário.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | E-mail | User. userprinicipalname |
    | OrganizationName | `Your organization name` |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar PureCloud por Genesys** , copie a URL (ou URLs) apropriada com base em seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste chamado B. Simon no portal do Azure:

1. No painel esquerdo da portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , digite o nome de usuário no seguinte formato: username@companydomain.extension. Por exemplo: `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa **senha** .
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você configurará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao PureCloud by Genesys.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **PureCloud by Genesys**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, escolha o botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, escolha o botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , selecione o botão **atribuir** .

## <a name="configure-purecloud-by-genesys-sso"></a>Configurar PureCloud por SSO do Genesys

1. Em uma janela diferente do navegador da Web, entre no PureCloud by Genesys como administrador.

1. Selecione **administrador** na parte superior e, em seguida, vá para **logon único** em **integrações**.

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Alterne para a guia **ADFS/AD do Azure (Premium)** e siga estas etapas:

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Selecione **procurar** para carregar o certificado codificado em base 64 que você baixou do portal do Azure para o **certificado do ADFS**.

    b. Na caixa **URI do emissor do ADFS** , Cole o valor do **identificador do Azure ad** que você copiou do portal do Azure.

    c. Na caixa **URI de destino** , Cole o valor da **URL de logon** que você copiou do portal do Azure.

    d. Para o valor do **identificador de terceira parte confiável** , vá para o portal do Azure e, em seguida, na página de integração do aplicativo **PureCloud by Genesys** , selecione a guia **Propriedades** e copie o valor da **ID do aplicativo** . Cole-o na caixa identificador de terceira **parte confiável** .

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Selecione **Guardar**.

### <a name="create-purecloud-by-genesys-test-user"></a>Criar PureCloud por usuário de teste do Genesys

Para permitir que os usuários do Azure AD entrem no PureCloud by Genesys, eles devem ser provisionados no PureCloud by Genesys. No PureCloud by Genesys, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, siga estas etapas:**

1. Faça logon no PureCloud by Genesys como administrador.

1. Selecione **administrador** na parte superior e vá para **pessoas** em **pessoas & permissões**.

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Na página **pessoas** , selecione **Adicionar pessoa**.

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Na caixa de diálogo **adicionar pessoas à organização** , siga estas etapas:

    ![Configurar logon único](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Na caixa **nome completo** , digite o nome de um usuário. Por exemplo: **B. Simon**.

    b. Na caixa **email** , insira o email do usuário. Por exemplo: **b. simon\@contoso.com**.

    c. Selecione **Criar**.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco **PureCloud by Genesys** no painel de acesso, você deverá ser conectado automaticamente à conta do PureCloud by Genesys para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o PureCloud by Genesys com o Azure AD](https://aad.portal.azure.com/)