---
title: 'Tutorial: integração do Azure Active Directory com o Deskradar | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Deskradar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 5ff3e014-b79f-4a6c-bb0b-38462bea5d10
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd5e3d6f8763af8c14d189321c6129876f48ab85
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158324"
---
# <a name="tutorial-integrate-deskradar-with-azure-active-directory"></a>Tutorial: integrar o Deskradar ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Deskradar com o Azure Active Directory (Azure AD). Ao integrar o Deskradar ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Deskradar.
* Habilite seus usuários a serem conectados automaticamente ao Deskradar com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do Deskradar.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Deskradar dá suporte ao **SP e** ao SSO iniciado pelo IDP.

## <a name="adding-deskradar-from-the-gallery"></a>Adicionando o Deskradar da Galeria

Para configurar a integração do Deskradar ao Azure AD, você precisará adicionar o Deskradar da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Deskradar** na caixa de pesquisa.
1. Selecione **Deskradar** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Configure e teste o SSO do Azure AD com o Deskradar usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Deskradar.

Para configurar e testar o SSO do Azure AD com o Deskradar, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Deskradar](#configure-deskradar-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Deskradar](#create-deskradar-test-user)** – para ter um equivalente de Brenda Simon no Deskradar que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Deskradar** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://YOURDOMAIN.deskradar.cloud`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/consume`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Substitua **seudomínio** pelo domínio da instância Deskradar. Contate a [equipe de suporte ao cliente do Deskradar](mailto:support@deskradar.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo Deskradar espera que as asserções SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

1. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome | Atributo de origem|
    | ---------------| --------- |
    | firstName | User. excertoname |
    | lastName | User. sobrenome |
    | E-mail | User. UserPrincipalName |
    | | |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**.

    g. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar Deskradar** , copie as URLs apropriadas com base em seu requisito.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-deskradar-sso"></a>Configurar o SSO do Deskradar

1. Para automatizar a configuração no Deskradar, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Setup Deskradar** irá direcioná-lo para o aplicativo Deskradar. A partir daí, forneça as credenciais de administrador para entrar no Deskradar. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-7.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o Deskradar manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Deskradar como administrador e execute as seguintes etapas:

1. Abra o painel **equipe** clicando no ícone na barra lateral.

1. Alterne para a guia **autenticação** .

1. Na guia **SAML 2,0** , execute as seguintes etapas:

    ![Configuração do Deskradar](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. Habilite o método de autenticação **SAML** .

    b. Na caixa de texto **URL de SSO do SAML** , insira o valor da URL de **logon** copiado do portal do Azure.

    c. Na caixa de texto **emissor do provedor de identidade** , insira o valor do identificador do **Azure ad** que você copiou do portal do Azure.

1. Abra o arquivo de **certificado (Base64)** baixado com um editor de texto e copie e Cole seu conteúdo no campo **certificado público** em Deskradar.

    ![Configuração do Deskradar](./media/deskradar-tutorial/15-paste-certificate.jpg)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Deskradar.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Deskradar**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-deskradar-test-user"></a>Criar usuário de teste do Deskradar

Nesta seção, você criará um usuário chamado Brenda Simon no Deskradar. Trabalhe com a [equipe de suporte ao cliente do Deskradar](mailto:support@deskradar.com) para adicionar os usuários na plataforma do Deskradar. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco Deskradar no painel de acesso, você deverá entrar automaticamente no Deskradar para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)