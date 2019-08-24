---
title: 'Tutorial: Integração do Azure Active Directory SSO (logon único) com o Harness | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Harness.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82367f62-173e-4e14-bf84-d8f611706086
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37a527e8cd83d292d8af8af5acd0c903c63081f2
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014077"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o Harness

Neste tutorial, você aprenderá a integrar o Harness ao Azure Active Directory (Azure AD). Ao integrar o Harness ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Harness.
* Habilite seus usuários a serem conectados automaticamente ao Harness com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Aproveite a assinatura habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Harness é compatível com o SSO iniciado por **SP e IDP**

## <a name="adding-harness-from-the-gallery"></a>Adicionando o Harness da Galeria

Para configurar a integração do Harness ao Azure AD, você precisará adicionar o Harness da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Harness** na caixa de pesquisa.
1. Selecione **estrutura** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Configurar e testar o logon único do Azure AD para o Harness

Configure e teste o SSO do Azure AD com o Harness usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Harness.

Para configurar e testar o SSO do Azure AD com o Harness, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO de Harness](#configure-harness-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Harness](#create-harness-test-user)** – para ter um equivalente de B. Simon no Harness que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Harness** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL:`https://app.harness.io/`

    > [!NOTE]
    > O valor da URL de resposta não é real. Você obterá a URL de resposta real na seção **configurar SSO do conjunto** , que é explicada posteriormente no tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o equipamento** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Harness.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Harness**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-harness-sso"></a>Configurar SSO de Harness

1. Abra uma nova janela do navegador da Web e entre no site da empresa do Harness como administrador.

1. Na parte superior direita da página, clique em **Gerenciamento** > de acesso de **segurança** >contínua**configurações de autenticação**.

    ![Configuração do Harness](./media/harness-tutorial/configure01.png)

1. Na seção **provedores de SSO** , clique em **+ adicionar provedores** >de SSO **SAML**.

    ![Configuração do Harness](./media/harness-tutorial/configure03.png)

1. No pop-up do **provedor SAML** , execute as seguintes etapas:

    ![Configuração do Harness](./media/harness-tutorial/configure02.png)

    a. Copie o **em seu provedor de SSO, habilite o logon baseado em SAML e, em seguida, insira a seguinte** instância de URL e cole-o na caixa de texto URL de resposta na seção **configuração básica do SAML** em portal do Azure.

    b. Na caixa de texto **nome de exibição** , digite seu nome de exibição.

    c. Clique em **escolher arquivo** para carregar o arquivo XML de metadados de Federação que você baixou do Azure AD.

    d. Clique em **Enviar**.

### <a name="create-harness-test-user"></a>Criar usuário de teste do Harness

Para permitir que os usuários do Azure AD entrem no Harness, eles devem ser provisionados no Harness. No Harness, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Harness como administrador.

1. Na parte superior direita da página, clique em **Gerenciamento** > de acesso de **segurança** >contínua**usuários**.

    ![Configuração do Harness](./media/harness-tutorial/configure04.png)

1. No lado direito da página, clique em **+ Adicionar usuário**.

    ![Configuração do Harness](./media/harness-tutorial/configure05.png)

1. No pop-up **Adicionar usuário** , execute as seguintes etapas:

    ![Configuração do Harness](./media/harness-tutorial/configure06.png)

    a. Na caixa de texto **Endereço (s) de email** , insira o email do `B.simon@contoso.com`usuário como.

    b. Selecione seus **grupos de usuários**.

    c. Clique em **Submit** (Submeter).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Harness no painel de acesso, você deverá ser conectado automaticamente ao conjunto para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Harness com o Azure AD](https://aad.portal.azure.com/)

