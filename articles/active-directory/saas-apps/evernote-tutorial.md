---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory com o Evernote | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55216e9eb4bd5cda2635564480751459f05e8309
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory com o Evernote

Neste tutorial, você aprenderá a integrar o Evernote ao Azure Active Directory (Azure AD). Ao integrar o Evernote ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Evernote.
* Habilite seus usuários a serem automaticamente conectados ao Evernote com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Evernote.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Evernote é compatível com o SSO iniciado por **SP e IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-evernote-from-the-gallery"></a>Adicionando o Evernote da Galeria

Para configurar a integração do Evernote ao Azure AD, você precisará adicionar o Evernote da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Evernote** na caixa de pesquisa.
1. Selecione o **Evernote** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Configurar e testar o logon único do Azure AD para o Evernote

Configure e teste o SSO do Azure AD com o Evernote usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Evernote.

Para configurar e testar o SSO do Azure AD com o Evernote, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Evernote](#configure-evernote-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Evernote](#create-evernote-test-user)** – para ter um equivalente de B. Simon no Evernote que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Evernote** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    Na caixa de texto **identificador** , digite uma URL:`https://www.evernote.com/saml2`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL:`https://www.evernote.com/Login.action`

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Para modificar as opções de **assinatura** , clique no botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** .

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Selecione a opção **assinar resposta SAML e declaração** para **assinatura**.

    b. Clique em **Guardar**

1. Na seção **Configurar o Evernote** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Evernote.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Evernote**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-evernote-sso"></a>Configurar o SSO do Evernote

1. Para automatizar a configuração no Evernote, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Evernote** irá direcioná-lo para o aplicativo Evernote. A partir daí, forneça as credenciais de administrador para entrar no Evernote. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o Evernote manualmente, abra uma nova janela do navegador da Web e entre no site da sua empresa do Evernote como administrador e execute as seguintes etapas:

4. Vá para o **' console de administração '**

    ![Administrador-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. No **' console de administração '** , vá para **' segurança '** e selecione **' logon único '**

    ![Configuração de SSO](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Configure os seguintes valores:

    ![Configuração de certificado](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Habilitar SSO:** O SSO está habilitado por padrão (clique em **desabilitar logon único** para remover o requisito de SSO)

    b. Cole o valor da **URL de logon** copiado do portal do Azure na caixa de texto **URL de solicitação HTTP do SAML** .

    c. Abra o certificado baixado do Azure AD em um bloco de notas e copie o conteúdo, incluindo "BEGIN CERTIFICATE" e "END CERTIFICATE", e cole-o na caixa de texto **certificado X. 509** . 

    d. clique em **salvar alterações**

### <a name="create-evernote-test-user"></a>Criar usuário de teste do Evernote

Para permitir que os usuários do AD do Azure entrem no Evernote, eles devem ser provisionados no Evernote.  
No caso do Evernote, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do Evernote como administrador.

2. Clique no **' console de administração '** .

    ![Administrador-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. No **' console de administração '** , vá para **' Adicionar usuários '** .

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Adicione membros da equipe** na caixa de texto **email** , digite o endereço de email da conta de usuário e clique em **convidar.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Depois que o convite for enviado, o titular da conta Azure Active Directory receberá um email para aceitar o convite.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Evernote no painel de acesso, você deverá ser conectado automaticamente ao Evernote para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Evernote com o Azure AD](https://aad.portal.azure.com/)

