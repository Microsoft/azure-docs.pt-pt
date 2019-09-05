---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory com o JAMF pro | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o JAMF pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f0d067351ab2e24b103624c8534ac8d6f2e757
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305443"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jamf-pro"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o JAMF pro

Neste tutorial, você aprenderá a integrar o JAMF pro ao Azure Active Directory (Azure AD). Ao integrar o JAMF pro ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao JAMF pro.
* Habilite seus usuários a serem conectados automaticamente ao JAMF pro com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do JAMF pro.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O JAMF pro é compatível com o SSO iniciado por **SP e IDP**

## <a name="adding-jamf-pro-from-the-gallery"></a>Adicionando o JAMF pro da Galeria

Para configurar a integração do JAMF pro com o Azure AD, você precisará adicionar o JAMF pro da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **JAMF pro** na caixa de pesquisa.
1. Selecione **JAMF pro** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jamf-pro"></a>Configurar e testar o logon único do Azure AD para o JAMF pro

Configure e teste o SSO do Azure AD com o JAMF pro usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no JAMF pro.

Para configurar e testar o SSO do Azure AD com o JAMF pro, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do JAMF pro](#configure-jamf-pro-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do JAMF pro](#create-jamf-pro-test-user)** – para ter um equivalente de B. Simon no JAMF pro que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **JAMF pro** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://<subdomain>.jamfcloud.com/saml/SSO`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Você obterá o valor do identificador real da seção de **logon único** no portal do JAMF pro, que é explicada posteriormente no tutorial. Você pode extrair o valor de **subdomínio** real do valor do identificador e usar essas informações de **subdomínio** na URL de logon e na URL de resposta. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao JAMF pro.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **JAMF pro**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-jamf-pro-sso"></a>Configurar o SSO do JAMF pro

1. Para automatizar a configuração no JAMF pro, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o JAMF pro** irá direcioná-lo para o aplicativo JAMF pro. A partir daí, forneça as credenciais de administrador para entrar no JAMF pro. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o JAMF pro manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do JAMF pro como administrador e execute as seguintes etapas:

4. Clique no **ícone configurações** no canto superior direito da página.

    ![Configuração do JAMF pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Clique em **logon único**.

    ![Configuração do JAMF pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na página **logon único,** execute as seguintes etapas:

    ![Configuração do JAMF pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Verifique a **autenticação habilitar logon único**.

    b. Selecione **outro** como uma opção na lista suspensa **provedor de identidade** .

    c. Na caixa de texto **outro provedor** , insira **Azure ad**.

    d. Copie o valor **ID da entidade** e cole-o na caixa de texto **identificador (ID da entidade)** na seção **configuração básica do SAML** em portal do Azure.

    > [!NOTE]
    > Aqui `<SUBDOMAIN>` , parte, você precisa usar esse valor para concluir a URL de logon e a URL de resposta na seção de **configuração básica do SAML** em portal do Azure.

    e. Selecione a **URL de metadados** como uma opção da lista suspensa origem de metadados do provedor de **identidade** e, na caixa de texto a seguir, Cole o valor da URL de metadados de **Federação do aplicativo** que você copiou do portal do Azure.

7. Na mesma página, role para baixo até a seção **mapeamento de usuário** e execute as seguintes etapas: 

    ![JAMF pro single](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Selecione a opção **NameID** para **mapeamento de usuário do provedor de identidade**. Por padrão, essa configuração é definida como **NameID** , mas você pode definir um atributo personalizado.

    b. Selecione **email** para **mapeamento de usuário do JAMF pro**. Os atributos SAML do JAMF pro Maps enviados pelo IdP das seguintes maneiras: por usuários e por grupos. Quando um usuário tenta acessar o JAMF pro, por padrão, o JAMF pro Obtém informações sobre o usuário do provedor de identidade e faz a correspondência com as contas de usuário do JAMF pro. Se a conta de usuário de entrada não existir no JAMF pro, a correspondência de nome de grupo ocorrerá.

    c. Cole o valor `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` na caixa de texto **nome do atributo do grupo do provedor de identidade** .

    d. Ao selecionar **permitir que os usuários ignorem a autenticação de logon único** , os usuários não serão redirecionados para a página de entrada do provedor de identidade para autenticação, mas poderão entrar no JAMF pro diretamente em vez disso. Quando um usuário tenta acessar o JAMF pro por meio do provedor de identidade, ocorre a autenticação SSO iniciada pelo IdP e a autorização.

    e. Clique em **Guardar**.

### <a name="create-jamf-pro-test-user"></a>Criar usuário de teste do JAMF pro

Para permitir que os usuários do Azure AD entrem no JAMF pro, eles devem ser provisionados no JAMF pro. No caso do JAMF pro, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site da empresa do JAMF pro como administrador.

2. Clique no **ícone configurações** no canto superior direito da página.

    ![Adicionar funcionário](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Clique em **contas de usuário do JAMF Pro & grupos**.

    ![Adicionar funcionário](./media/jamfprosamlconnector-tutorial/user1.png)

4. Clique em **Novo**.

    ![Adicionar funcionário](./media/jamfprosamlconnector-tutorial/user2.png)

5. Selecione **criar conta padrão**.

    ![Adicionar funcionário](./media/jamfprosamlconnector-tutorial/user3.png)

6. No conprocedimento **nova conta** , execute as seguintes etapas:

    ![Adicionar funcionário](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Na caixa de texto **nome de usuário** , digite o nome completo de brendafernandes.

    b. Selecione as opções apropriadas de acordo com sua organização para **nível de acesso**, **conjunto de privilégios**e status de **acesso**.

    c. Na caixa de texto **nome completo** , digite o nome completo do Brenda Simon.

    d. Na caixa de texto **endereço de email** , digite o endereço de email da conta Brenda Simon.

    e. Na caixa de texto **senha** , digite a senha do usuário.

    f. Na caixa de texto **verificar senha** , digite a senha do usuário.

    g. Clique em **Guardar**.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco JAMF pro no painel de acesso, você deverá entrar automaticamente no JAMF pro para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o JAMF pro com o Azure AD](https://aad.portal.azure.com/)

