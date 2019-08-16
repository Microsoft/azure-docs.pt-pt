---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com ScaleX Enterprise | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adf62a6edf0a53248ccde30c08aed709e60f1957
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559118"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com ScaleX Enterprise

Neste tutorial, você aprenderá a integrar o ScaleX Enterprise ao Azure Active Directory (Azure AD). Ao integrar o ScaleX Enterprise ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao ScaleX Enterprise.
* Habilite seus usuários a entrar automaticamente no ScaleX Enterprise com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do ScaleX Enterprise.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* ScaleX Enterprise dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Adicionando o ScaleX Enterprise por meio da Galeria

Para configurar a integração do ScaleX Enterprise ao Azure AD, você precisará adicionar o ScaleX Enterprise por meio da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **ScaleX Enterprise** na caixa de pesquisa.
1. Selecione **ScaleX Enterprise** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Configurar e testar o logon único do Azure AD para o ScaleX Enterprise

Configure e teste o SSO do Azure AD com o ScaleX Enterprise usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ScaleX Enterprise.

Para configurar e testar o SSO do Azure AD com o ScaleX Enterprise, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar ScaleX Enterprise SSO](#configure-scalex-enterprise-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste do ScaleX Enterprise](#create-scalex-enterprise-test-user)** – para ter um equivalente de B. Simon no ScaleX Enterprise que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ScaleX Enterprise** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:`https://platform.rescale.com/saml2/<company id>/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://platform.rescale.com/saml2/<company id>/acs/`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte do ScaleX cliente corporativo](https://info.rescale.com/contact_sales) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Seu aplicativo ScaleX Enterprise espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **EmailAddress** é mapeado com **User. mail**. O aplicativo ScaleX Enterprise espera que o **EmailAddress** seja mapeado com **User. UserPrincipalName**, portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar ScaleX Enterprise** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-scalex-enterprise-sso"></a>Configurar o SSO corporativo do ScaleX

1. Para configurar o logon único no lado do **ScaleX Enterprise** , entre no site da empresa ScaleX Enterprise como administrador.

1. Clique no menu no canto superior direito e selecione **Administração da Contoso**.

    > [!NOTE]
    > A contoso é apenas um exemplo. Esse deve ser o nome real da sua empresa.

    ![Configurar o início de sessão único](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Selecione **integrações** no menu superior e selecione **logon único**.

    ![Configurar o início de sessão único](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Preencha o formulário da seguinte maneira:

    ![Configurar o início de sessão único](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Selecione **criar qualquer usuário que possa se autenticar com o SSO**

    b. **SAML do provedor de serviço**: Cole o valor ***urn: Oasis: names: TC: SAML: 2.0: NameID-Format: persistent***

    c. **Nome do campo de email do provedor de identidade na resposta do ACS**: Cole o valor`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **ID da entidade EntityDescriptor do provedor de identidade:** Cole o valor do **identificador do Azure ad** copiado do portal do Azure.

    e. **URL do SingleSignOnService do provedor de identidade:** Cole a **URL de logon** da portal do Azure.

    f. **Certificado X509 público do provedor de identidade:** Abra o certificado X509 baixado do Azure no bloco de notas e cole o conteúdo nesta caixa. Verifique se não há quebras de linha no meio do conteúdo do certificado.

    g. Marque as seguintes caixas de seleção: **Habilitado, criptografe NameID e Sign AuthnRequests.**

    h. Clique em **Atualizar configurações de SSO** para salvar as configurações.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao ScaleX Enterprise.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **ScaleX Enterprise**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-scalex-enterprise-test-user"></a>Criar usuário de teste do ScaleX Enterprise

Para permitir que os usuários do AD do Azure entrem no ScaleX Enterprise, eles devem ser provisionados no ScaleX Enterprise. No caso do ScaleX Enterprise, o provisionamento é uma tarefa automática e nenhuma etapa manual é necessária. Qualquer usuário que puder se autenticar com credenciais de SSO será automaticamente provisionado no lado da ScaleX.

### <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco ScaleX Enterprise no painel de acesso, você deverá ser conectado automaticamente à ScaleX Enterprise para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)