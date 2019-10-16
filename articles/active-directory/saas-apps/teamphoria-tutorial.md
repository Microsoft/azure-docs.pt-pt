---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Teamphoria | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2631b34f5658c9d4f76ca26d378bc63fe59ad156
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373255"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Teamphoria

Neste tutorial, você aprenderá a integrar o Teamphoria com o Azure Active Directory (Azure AD). Ao integrar o Teamphoria ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Teamphoria.
* Habilite seus usuários a serem conectados automaticamente ao Teamphoria com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Teamphoria.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Teamphoria dá suporte ao SSO iniciado por **SP**

## <a name="adding-teamphoria-from-the-gallery"></a>Adicionando o Teamphoria da Galeria

Para configurar a integração do Teamphoria ao Azure AD, você precisará adicionar o Teamphoria da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Teamphoria** na caixa de pesquisa.
1. Selecione **Teamphoria** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Configurar e testar o logon único do Azure AD para o Teamphoria

Configure e teste o SSO do Azure AD com o Teamphoria usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Teamphoria.

Para configurar e testar o SSO do Azure AD com o Teamphoria, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Teamphoria](#configure-teamphoria-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Teamphoria](#create-teamphoria-test-user)** – para ter um equivalente de B. Simon em Teamphoria que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Teamphoria** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Teamphoria](https://www.teamphoria.com/) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar Teamphoria** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Teamphoria.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Teamphoria**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-teamphoria-sso"></a>Configurar o SSO do Teamphoria

1. Para automatizar a configuração no Teamphoria, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Teamphoria** irá direcioná-lo para o aplicativo Teamphoria. A partir daí, forneça as credenciais de administrador para entrar no Teamphoria. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o Teamphoria manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Teamphoria como administrador e execute as seguintes etapas:

4. Vá para a opção **configurações de administrador** na barra de ferramentas à esquerda e, na guia Configurar, clique em **logon único** para abrir a janela configuração de SSO.

    ![Configurar logon único](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Clique na opção **Adicionar novo provedor de identidade** no canto superior direito para abrir o formulário para adicionar as configurações de SSO.

    ![Configurar logon único](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Insira os detalhes nos campos conforme descrito abaixo-

    ![Configurar logon único](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **Nome de exibição**: Insira o nome de exibição do plug-in na página do administrador.

    b. **Nome do botão**: o nome da guia que será exibida na página de logon para fazer logon via SSO.

    c. **Certificado**: Abra o certificado baixado anteriormente na portal do Azure no bloco de notas, copie o conteúdo do mesmo e cole-o aqui na caixa.

    d. **Ponto de entrada**: Cole a **URL de logon** copiada anteriormente da portal do Azure.

    e. Alterne a opção para **ativado** e clique em **salvar**.

### <a name="create-teamphoria-test-user"></a>Criar usuário de teste do Teamphoria

Para permitir que os usuários do AD do Azure entrem no Teamphoria, eles devem ser provisionados no Teamphoria. No caso do Teamphoria, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site da empresa do Teamphoria como um administrador.

1. Clique em configurações de **administrador** na barra de ferramentas à esquerda e, na guia **gerenciar** , clique em **usuários** para abrir a página de administração para usuários.

    ![Adicionar funcionário](./media/teamphoria-tutorial/admin_manage_users.png)

1. Clique na opção de **convite manual** .

    ![Convidar pessoas](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Nessa página, execute a ação a seguir.

    ![Convidar pessoas](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Na caixa de texto **endereço de email** , insira o **endereço de email** do usuário, como B. Simon.

    b. Na caixa de texto **nome** , insira o nome do usuário, como **B**.

    c. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.

    d. Clique em **convidar 1 usuário**. O usuário precisa aceitar o convite para ser criado no sistema.

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Teamphoria no painel de acesso, você deverá ser conectado automaticamente ao Teamphoria para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Teamphoria com o Azure AD](https://aad.portal.azure.com/)

