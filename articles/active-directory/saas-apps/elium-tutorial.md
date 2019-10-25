---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Elium | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791230"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Elium

Neste tutorial, você aprenderá a integrar o Elium com o Azure Active Directory (Azure AD). Ao integrar o Elium ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Elium.
* Habilite seus usuários a serem conectados automaticamente ao Elium com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Elium.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Elium dá suporte ao **SP e** ao SSO iniciado pelo IDP
* O Elium dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-elium-from-the-gallery"></a>Adicionando o Elium da Galeria

Para configurar a integração do Elium ao Azure AD, você precisará adicionar o Elium da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Elium** na caixa de pesquisa.
1. Selecione **Elium** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Configurar e testar o logon único do Azure AD para o Elium

Configure e teste o SSO do Azure AD com o Elium usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Elium.

Para configurar e testar o SSO do Azure AD com o Elium, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Elium](#configure-elium-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Elium](#create-elium-test-user)** – para ter um equivalente de B. Simon em Elium que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Elium** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Esses valores não são reais. Você obterá esses valores do **arquivo de metadados do SP** baixável em `https://<platform-domain>.elium.com/login/saml2/metadata`, que é explicado mais adiante neste tutorial.

1. O aplicativo Elium espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo Elium espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | ---------------| ----------------|
    | e-mail   |User. mail |
    | first_name| User. excertoname |
    | last_name| User. sobrenome|
    | job_title| User. JobTitle|
    | corporativa| User. CompanyName|

    > [!NOTE]
    > Essas são as declarações padrão. **Somente a declaração de email é necessária**. Para provisionamento de JIT, somente a declaração de email é obrigatória. Outras declarações personalizadas podem variar de uma plataforma de cliente para outra plataforma de cliente.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/metadataxml.png)

1. Na seção **Configurar Elium** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Elium.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Elium**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-elium-sso"></a>Configurar o SSO do Elium

1. Para automatizar a configuração no Elium, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o Elium** irá direcioná-lo para o aplicativo Elium. A partir daí, forneça as credenciais de administrador para entrar no Elium. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o Elium manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Elium como administrador e execute as seguintes etapas:

1. Clique no **perfil do usuário** no canto superior direito e selecione **Administração**.

    ![Configurar logon único](./media/elium-tutorial/user1.png)

1. Selecione a guia **segurança** .

    ![Configurar logon único](./media/elium-tutorial/user2.png)

1. Role para baixo até a seção **SSO (logon único)** e execute as seguintes etapas:

    ![Configurar logon único](./media/elium-tutorial/user3.png)

    a. Copie o valor de **verificar se a autenticação do SAML2 funciona para sua conta** e cole-a na caixa de texto **URL de logon** na seção **configuração básica do SAML** no portal do Azure.

    > [!NOTE]
    > Depois de configurar o SSO, você sempre poderá acessar a página de logon remoto padrão na seguinte URL: `https://<platform_domain>/login/regular/login` 

    b. Marque a caixa de seleção **habilitar Federação de SAML2** .

    c. Selecione a caixa de seleção **provisionamento JIT** .

    d. Abra os **metadados do SP** clicando no botão **baixar** .

    e. Pesquise a ID da **entidade** no arquivo de **metadados do SP** , copie o valor **EntityId** e cole-o na caixa de texto **identificador** na seção **configuração básica do SAML** no portal do Azure. 

    ![Configurar logon único](./media/elium-tutorial/user4.png)

    f. Procure o **AssertionConsumerService** no arquivo de **metadados do SP** , copie o valor do **local** e cole-o na caixa de texto **URL de resposta** na seção **configuração básica do SAML** no portal do Azure.

    ![Configurar logon único](./media/elium-tutorial/user5.png)

    g. Abra o arquivo de metadados baixado de portal do Azure no bloco de notas, copie o conteúdo e cole-o na caixa de texto **metadados do IDP** .

    h. Clique em **Guardar**.

### <a name="create-elium-test-user"></a>Criar usuário de teste do Elium

Nesta seção, um usuário chamado B. Simon é criado em Elium. O Elium dá suporte ao **provisionamento just-in-time**, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Elium, um novo será criado quando você tentar acessar o Elium.

> [!Note]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Elium no painel de acesso, você deverá ser conectado automaticamente ao Elium para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Elium com o Azure AD](https://aad.portal.azure.com/)