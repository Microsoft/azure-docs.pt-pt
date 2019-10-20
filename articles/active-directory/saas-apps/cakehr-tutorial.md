---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o CakeHR | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o CakeHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c9bbdb1-ac47-4fb8-a1cc-1e647b0323a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0860411c95e48a16d75df4aeeedf3405a5b1835
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595028"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o CakeHR

Neste tutorial, você aprenderá a integrar o CakeHR com o Azure Active Directory (Azure AD). Ao integrar o CakeHR ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao CakeHR.
* Habilite seus usuários a serem conectados automaticamente ao CakeHR com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do CakeHR.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O CakeHR dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-cakehr-from-the-gallery"></a>Adicionando o CakeHR da Galeria

Para configurar a integração do CakeHR ao Azure AD, você precisará adicionar o CakeHR da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **CakeHR** na caixa de pesquisa.
1. Selecione **CakeHR** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Configurar e testar o logon único do Azure AD para o CakeHR

Configure e teste o SSO do Azure AD com o CakeHR usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no CakeHR.

Para configurar e testar o SSO do Azure AD com o CakeHR, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do CakeHR](#configure-cakehr-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do CakeHR](#create-cakehr-test-user)** – para ter um equivalente de B. Simon em CakeHR que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **CakeHR** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<yourcakedomain>.cake.hr/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e a URL de resposta reais. Contate a [equipe de suporte ao cliente do CakeHR](mailto:info@cake.hr) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na seção **certificado de autenticação SAML** , clique no botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** .

    ![Editar certificado de autenticação SAML](common/edit-certificate.png)

1. Na seção **certificado de autenticação SAML** , copie o valor da **impressão digital** e salve-o no bloco de notas.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na seção **Configurar CakeHR** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao CakeHR.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **CakeHR**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-cakehr-sso"></a>Configurar o SSO do CakeHR

1. Para automatizar a configuração no CakeHR, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o CakeHR** irá direcioná-lo para o aplicativo CakeHR. A partir daí, forneça as credenciais de administrador para entrar no CakeHR. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-5.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o CakeHR manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do CakeHR como administrador e execute as seguintes etapas:

1. No canto superior direito da página, clique em **perfil** e, em seguida, navegue até **configurações**.

    ![Configuração do CakeHR](./media/cakehr-tutorial/config01.png)

1. No lado esquerdo da barra de menus, clique em **integrações**  > **SSO do SAML** e execute as seguintes etapas:

    ![Configuração do CakeHR](./media/cakehr-tutorial/config02.png)

    a. Na caixa de texto **ID da entidade** , digite `cake.hr`.

    b. Na caixa de texto **URL de autenticação** , Cole o valor da **URL de logon**copiado de portal do Azure.

    c. Na caixa de texto **impressão digital de chave (formato SHA1)** , Cole o valor de **impressão digital** copiado de portal do Azure.

    d. Marque a caixa **habilitar logon único** .

    e. Clique em **Guardar**.

### <a name="create-cakehr-test-user"></a>Criar usuário de teste do CakeHR

Para permitir que os usuários do Azure AD entrem no CakeHR, eles devem ser provisionados no CakeHR. No CakeHR, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no CakeHR como um administrador de segurança.

2. No lado esquerdo da barra de menus, clique em **empresa**  > **Adicionar**.

    ![Configuração do CakeHR](./media/cakehr-tutorial/config03.png)

3. No pop-up **Adicionar novo funcionário** , execute as seguintes etapas:

     ![Configuração do CakeHR](./media/cakehr-tutorial/config04.png)

    a. Na caixa de texto **nome completo** , digite o nome do usuário, como B. Simon.

    b. Na caixa de texto **email de trabalho** , insira o email do usuário, como `B.Simon@contoso.com`.

    c. Clique em **criar conta**.

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do CakeHR no painel de acesso, você deverá ser conectado automaticamente ao CakeHR para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o CakeHR com o Azure AD](https://aad.portal.azure.com/)
