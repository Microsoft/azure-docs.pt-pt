---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com a margem de atraso | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e a margem de atraso.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ac464593e14a712cf0f137b5f3522d018d5874d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884528"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com a margem de atraso

Neste tutorial, você aprenderá a integrar a margem de atraso com o Azure Active Directory (Azure AD). Ao integrar a margem de atraso ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso à margem de atraso.
* Permita que os usuários sejam automaticamente conectados à margem de atraso com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* A margem de atraso dá suporte ao SSO iniciado pelo **SP**
* A margem de atraso dá suporte ao provisionamento **de usuário just in time**
* A margem de atraso dá suporte ao [provisionamento **automatizado** de usuários](slack-provisioning-tutorial.md)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-slack-from-the-gallery"></a>Adicionando a margem de atraso da Galeria

Para configurar a integração da margem de atraso no Azure AD, você precisa adicionar a margem de atraso da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite margem de **atraso** na caixa de pesquisa.
1. Selecione **margem de atraso** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Configurar e testar o logon único do Azure AD para a margem de atraso

Configure e teste o SSO do Azure AD com a margem de atraso usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na margem de atraso.

Para configurar e testar o SSO do Azure AD com a margem de atraso, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO de margem de atraso](#configure-slack-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste de margem de atraso](#create-slack-test-user)** – para ter um equivalente de B. Simon na margem de atraso vinculada à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos de **margem de atraso** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<your Slack company>.slack.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL: `https://slack.com`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente de margem de atraso](https://slack.com/help/contact) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar margem de atraso** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso à margem de atraso.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **margem de atraso**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-slack-sso"></a>Configurar o SSO de margem de atraso

1. Em uma janela diferente do navegador da Web, entre no site da empresa de margem de atraso como administrador.

2. Navegue até o nome da sua empresa de margem de atraso à esquerda, que, em nosso caso, foi configurada como **Microsoft Azure ad** e, em seguida, vá para **configurações da equipe** , conforme mostrado na captura de tela a seguir.

     ![Configurar o logon único no lado do aplicativo](./media/slack-tutorial/tutorial_slack_001.png)

3. Na seção **configurações da equipe** , clique na guia **autenticação** e, em seguida, clique em **alterar configurações**.

    ![Configurar o logon único no lado do aplicativo](./media/slack-tutorial/tutorial_slack_002.png)

4. Na caixa de diálogo **configurações de autenticação do SAML** , execute as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Na caixa de texto **ponto de extremidade SAML 2,0 (http)** , Cole o valor da **URL de logon**copiado de portal do Azure.

    b.  Na caixa de texto **emissor do provedor de identidade** , Cole o valor do identificador do **Azure ad**que você copiou do portal do Azure.

    c.  Abra o arquivo de certificado baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado público** .

    d. Defina as três configurações acima conforme apropriado para sua equipe de margem de atraso. Para obter mais informações sobre as configurações, encontre o **Guia de configuração de SSO da margem de atraso** aqui. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Clique em **salvar configuração**.

### <a name="create-slack-test-user"></a>Criar usuário de teste de margem de atraso

O objetivo desta seção é criar um usuário chamado B. Simon na margem de atraso. A margem de atraso dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há nenhum item de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar a margem de atraso, caso ele ainda não exista. A margem de atraso também dá suporte ao provisionamento automático de usuário. você pode encontrar mais detalhes [aqui](slack-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte da margem de atraso](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect é a ferramenta de sincronização que pode ser sincronizada localmente Active Directory identidades ao Azure AD e, em seguida, esses usuários sincronizados também podem usar os aplicativos como outros usuários de nuvem.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco de margem de atraso no painel de acesso, você deverá entrar automaticamente na margem de atraso para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a margem de atraso com o Azure AD](https://aad.portal.azure.com/)
