---
title: 'Tutorial: integração do Azure Active Directory com o RunMyProcess | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c31a209e8521b24e7f604dbe630f689fca484e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68880383"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Tutorial: integrar o RunMyProcess ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o RunMyProcess com o Azure Active Directory (Azure AD). Ao integrar o RunMyProcess ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao RunMyProcess.
* Habilite seus usuários a serem conectados automaticamente ao RunMyProcess com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do RunMyProcess.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O RunMyProcess dá suporte ao SSO iniciado por **SP**

## <a name="adding-runmyprocess-from-the-gallery"></a>Adicionando o RunMyProcess da Galeria

Para configurar a integração do RunMyProcess ao Azure AD, você precisará adicionar o RunMyProcess da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **RunMyProcess** na caixa de pesquisa.
1. Selecione **RunMyProcess** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Configure e teste o SSO do Azure AD com o RunMyProcess usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no RunMyProcess.

Para configurar e testar o SSO do Azure AD com o RunMyProcess, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do RunMyProcess](#configure-runmyprocess-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste do RunMyProcess](#create-runmyprocess-test-user)** – para ter um equivalente de B. Simon em RunMyProcess que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **RunMyProcess** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do RunMyProcess](mailto:support@runmyprocess.com) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar RunMyProcess** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Configurar o SSO do RunMyProcess

1. Em uma janela diferente do navegador da Web, faça logon no seu locatário do RunMyProcess como administrador.

1. No painel de navegação esquerdo, clique em **conta** e selecione **configuração**.

    ![Configurar o logon único no lado do aplicativo](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Vá para a seção **método de autenticação** e execute as etapas abaixo:

    ![Configurar o logon único no lado do aplicativo](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Como **método**, selecione **SSO com Samlv2**.

    b. Na caixa de texto **redirecionamento de SSO** , Cole o valor da **URL de logon**copiado de portal do Azure.

    c. Na caixa de texto **redirecionamento de logout** , Cole o valor da **URL de logout**copiado de portal do Azure.

    d. Na caixa de texto **formato da ID de nome** , digite o valor do formato do identificador de **nome** como **urn: Oasis: names: TC: SAML: 1.1: NameID-Format: EmailAddress**.

    e. Abra o arquivo de certificado baixado em portal do Azure no bloco de notas, copie o conteúdo do arquivo de certificado e cole-o na caixa de texto **certificado** .

    f. Clique no ícone **salvar** .

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao RunMyProcess.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **RunMyProcess**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-runmyprocess-test-user"></a>Criar usuário de teste do RunMyProcess

Para permitir que os usuários do AD do Azure entrem no RunMyProcess, eles devem ser provisionados no RunMyProcess. No caso do RunMyProcess, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site da empresa do RunMyProcess como um administrador.

1. Clique em **conta** e selecione **usuários** no painel de navegação esquerdo e clique em **novo usuário**.

    ![Novo usuário](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Novo usuário")

1. Na seção **configurações do usuário** , execute as seguintes etapas:

    ![Perfil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Perfil")
  
    a. Digite o **nome** e o **email** de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas.

    b. Selecione um **idioma IDE**, um **idioma**e um **perfil**.

    c. Selecione **Enviar email de criação de conta para mim**.

    d. Clique em **Guardar**.

    > [!NOTE]
    > Você pode usar qualquer outra ferramenta de criação de conta de usuário RunMyProcess ou APIs fornecidas pelo RunMyProcess para provisionar Azure Active Directory contas de usuário.

### <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do RunMyProcess no painel de acesso, você deverá ser conectado automaticamente ao RunMyProcess para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)