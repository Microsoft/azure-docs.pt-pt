---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com o kit de ferramentas SAML do Azure AD | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o kit de ferramentas de SAML do Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83e5790a036a93c947a79bc2848e7be6febf30b6
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122054"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o kit de ferramentas do Azure AD SAML

Neste tutorial, você aprenderá a integrar o kit de ferramentas do Azure AD SAML ao Azure Active Directory (Azure AD). Ao integrar o kit de ferramentas de SAML do Azure AD ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao kit de ferramentas de SAML do Azure AD.
* Habilite seus usuários a entrar automaticamente no kit de ferramentas de SAML do Azure AD com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Azure AD SAML Toolkit.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O kit de ferramentas do Azure AD SAML dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Adicionando o kit de ferramentas de SAML do Azure AD da Galeria

Para configurar a integração do kit de ferramentas de SAML do Azure AD ao Azure AD, você precisa adicionar o Azure AD SAML Toolkit da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite kit de **ferramentas de SAML do Azure ad** na caixa de pesquisa.
1. Selecione **Kit de ferramentas de SAML do Azure ad** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Configurar e testar o logon único do Azure AD para o kit de ferramentas do Azure AD SAML

Configure e teste o SSO do Azure AD com o kit de ferramentas de SAML do Azure AD usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no kit de ferramentas de SAML do Azure AD.

Para configurar e testar o SSO do Azure AD com o kit de ferramentas de SAML do Azure AD, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Azure ad SAML Toolkit](#configure-azure-ad-saml-toolkit-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Azure ad SAML Toolkit](#create-azure-ad-saml-toolkit-test-user)** – para ter um equivalente de B. Simon no kit de ferramentas de SAML do Azure AD que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **do Azure ad SAML Toolkit** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL:`https://samltoolkit.azurewebsites.net/`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL:`https://samltoolkit.azurewebsites.net`

    c. Na caixa de texto **URL de resposta** , digite uma URL:`https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize **certificado (bruto)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificateraw.png)

1. Na seção **Configurar o kit de ferramentas do Azure ad SAML** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao kit de ferramentas de SAML do Azure AD.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Kit de ferramentas de SAML do Azure ad**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Configurar SSO do Azure AD SAML Toolkit

1. Abra uma nova janela do navegador da Web, se você não tiver se registrado no site do kit de ferramentas do Azure AD SAML, primeiro registre-se clicando no **registro**. Se você já tiver registrado, entre em seu site de empresa do Azure AD SAML Toolkit usando as credenciais de logon registradas.

    ![Registro do kit de ferramentas do Azure AD SAML](./media/saml-toolkit-tutorial/register.png)

1. Clique na **configuração do SAML**.

    ![Configuração SAML do kit de ferramentas do Azure AD SAML](./media/saml-toolkit-tutorial/saml-configure.png)

1. Clique em **Criar**.

    ![Azure AD SAML Toolkit-criar SSO](./media/saml-toolkit-tutorial/createsso.png)

1. Na página **configuração de SSO do SAML** , execute as seguintes etapas:

    ![Azure AD SAML Toolkit-criar SSO](./media/saml-toolkit-tutorial/fill-details.png)

    1. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon** copiado do portal do Azure.

    1. Na caixa de texto **identificador do Azure ad** , Cole o valor do **identificador do Azure ad** copiado do portal do Azure.

    1. Na caixa de texto **URL de logout** , Cole o valor da **URL de logout** copiado do portal do Azure.

    1. Clique em **escolher arquivo** e carregue o arquivo de **certificado (bruto)** que você baixou do portal do Azure.

    1. Clique em **Criar**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Criar usuário de teste do kit de ferramentas do Azure AD SAML

Nesta seção, um usuário chamado B. Simon é criado no kit de ferramentas de SAML do Azure AD. O kit de ferramentas de SAML do Azure AD dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no kit de ferramentas de SAML do Azure AD, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do kit de ferramentas do Azure AD SAML no painel de acesso, você deverá ser conectado automaticamente ao kit de ferramentas do Azure AD SAML para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o kit de ferramentas SAML do Azure AD com o Azure AD](https://aad.portal.azure.com/)