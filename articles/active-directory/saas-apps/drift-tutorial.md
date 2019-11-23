---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com descompasso | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e descompasso.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39dcbb95-c192-448c-86a1-cedede1c0972
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cd749ef66ee62f6d89d949cef7ce800bc46d59a
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554366"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-drift"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com descompasso

Neste tutorial, você aprenderá a integrar o descompasso com o Azure Active Directory (Azure AD). Ao integrar o descompasso ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao descompasso.
* Permita que os usuários sejam automaticamente conectados ao descompasso com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do descompasso habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O descompasso dá suporte ao SP iniciado pelo **IDP**
* O descompasso dá suporte ao provisionamento **de usuário just-in-time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-drift-from-the-gallery"></a>Adicionando descompasso da Galeria

Para configurar a integração do descompasso ao Azure AD, você precisa adicionar descompasso da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **descompasso** na caixa de pesquisa.
1. Selecione **descompasso** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-drift"></a>Configurar e testar o logon único do Azure AD para descompasso

Configure e teste o SSO do Azure AD com descompasso usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no descompasso.

Para configurar e testar o SSO do Azure AD com descompasso, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar SSO de descompasso](#configure-drift-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do descompasso](#create-drift-test-user)** – para ter um equivalente de B. Simon em descompasso vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **descompasso** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** o aplicativo é pré-configurado no modo de iniciado pelo **IDP** e as URLs necessárias já estão preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

    a. Clique em **definir URLs adicionais**.
 
    b. Na caixa de texto **estado de retransmissão** , digite uma URL: `https://app.drift.com` 

    c. Se você quiser configurar o aplicativo no modo iniciado pelo **SP** , execute a seguinte etapa:

    d. Na caixa de texto **URL de logon** , digite uma URL: `https://start.drift.com`

6. Seu aplicativo de descompasso espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

7. Além de acima, o aplicativo descompasso espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito. 

    | Nome | Atributo de origem|
    | ---------------| --------------- |    
    | Nome | user.displayname |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar descompasso** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao descompasso.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **descompasso**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-drift-sso"></a>Configurar SSO de descompasso

1. Para automatizar a configuração dentro do descompasso, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar descompasso** , você será direcionado para o aplicativo descompasso. A partir daí, forneça as credenciais de administrador para entrar no descompasso. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-4.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o descompasso manualmente, abra uma nova janela do navegador da Web e entre no site da empresa descompasso como administrador e execute as seguintes etapas:

4. No lado esquerdo da barra de menus, clique no **ícone de configurações** > **configurações do aplicativo** > **autenticação** e execute as seguintes etapas:

    ![O link do administrador](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Carregue o **XML de metadados de Federação** que você baixou do portal do Azure, na caixa de texto **carregar arquivo de metadados do provedor de identidade** .

    b. Depois de carregar o arquivo de metadados, os valores restantes serão preenchidos automaticamente na página.

    c. Clique em **habilitar SAML**.

### <a name="create-drift-test-user"></a>Criar usuário de teste do descompasso

Nesta seção, um usuário chamado Brenda Simon é criado em descompasso. O descompasso dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir em descompasso, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do descompasso](mailto:integrations@drift.com).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco descompasso no painel de acesso, você deverá ser automaticamente conectado ao descompasso para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o descompasso com o Azure AD](https://aad.portal.azure.com/)

