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
ms.openlocfilehash: 91c8a2fb278515306848f46206db67b7f37ea2ac
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034248"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Tutorial: Integração do SSO do Azure Active Directory com o JAMF pro

Neste tutorial, você aprenderá a integrar o JAMF pro ao Azure Active Directory (Azure AD). Ao integrar o JAMF pro ao Azure AD, você pode:

* Use o Azure AD para controlar quem tem acesso ao JAMF pro.
* Conecte seus usuários automaticamente ao JAMF pro com suas contas do Azure AD.
* Gerencie suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do JAMF pro que é habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O JAMF pro dá suporte ao SSO iniciado por **SP** e iniciado pelo **IDP** .

## <a name="add-jamf-pro-from-the-gallery"></a>Adicionar o JAMF pro da Galeria

Para configurar a integração do JAMF pro com o Azure AD, você precisará adicionar o JAMF pro da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou sua conta Microsoft pessoal.
1. No painel esquerdo, selecione o serviço **Azure Active Directory** .
1. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , insira *JAMF pro* na caixa de pesquisa.
1. Selecione **JAMF pro** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Configurar e testar o SSO no Azure AD para JAMF pro

Configure e teste o SSO do Azure AD com o JAMF pro usando um usuário de teste chamado B. Simon. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no JAMF pro.

Nesta seção, você configurará e testará o SSO do Azure AD com o JAMF pro.

1. [Configure o SSO no Azure ad](#configure-sso-in-azure-ad) para que os usuários possam usar esse recurso.
    1. [Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user) para testar o SSO do Azure AD com a conta B. Simon.
    1. [Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user) para que B. Simon possa usar o SSO no Azure AD.
1. [Configure o SSO no JAMF pro](#configure-sso-in-jamf-pro) para definir as configurações de SSO no lado do aplicativo.
    1. [Crie um usuário de teste do JAMF pro](#create-a-jamf-pro-test-user) para ter um equivalente de B. Simon no JAMF pro que esteja vinculado à representação do usuário no Azure AD.
1. [Teste a configuração de SSO](#test-the-sso-configuration) para verificar se a configuração funciona.

## <a name="configure-sso-in-azure-ad"></a>Configurar o SSO no Azure AD

Nesta seção, você habilitará o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **JAMF pro** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , selecione o ícone de caneta para a **configuração básica do SAML** para editar as configurações.

   ![Edite a página de configuração básica do SAML.](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo **iniciado pelo IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , insira uma URL que usa a seguinte fórmula:`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Na caixa de texto **URL de resposta** , insira uma URL que usa a seguinte fórmula:`https://<subdomain>.jamfcloud.com/saml/SSO`

1. Selecione **definir URLs adicionais**. Se você quiser configurar o aplicativo no modo **iniciado pelo SP** , na caixa de texto **URL de logon** , insira uma URL que usa a seguinte fórmula:`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Você obterá o valor do identificador real de * a seção de **logon único** no portal do JAMF pro, que é explicada posteriormente no tutorial. Você pode extrair o valor de subdomínio real do valor do identificador e usar essas informações de subdomínio como URL de logon e URL de resposta. Você também pode consultar as fórmulas mostradas na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , vá para a seção **certificado de autenticação SAML** , selecione o botão **copiar** para copiar a URL de **metadados de Federação do aplicativo**e, em seguida, salve-o em seu computador.

    ![O link de download do certificado de autenticação SAML](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você cria um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , digite [nome] @ [companydomain]. [extensão]. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você concede a B. Simon acesso ao JAMF pro.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **JAMF pro**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![Selecionar usuários e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![Selecione o botão Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, selecione o botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário. Em seguida, selecione o botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , selecione o botão **atribuir** .

## <a name="configure-sso-in-jamf-pro"></a>Configurar o SSO no JAMF pro 

1. Para automatizar a configuração no JAMF pro, instale a **extensão do navegador de entrada seguro meus aplicativos** selecionando **instalar a extensão**.

    ![Página de extensão do navegador de entrada segura de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecione **Configurar o JAMF pro**. Quando o aplicativo JAMF Pro for aberto, forneça as credenciais de administrador para entrar. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas 3 a 7.

    ![Página configuração de instalação no JAMF pro](common/setup-sso.png)

3. Para configurar o JAMF pro manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do JAMF pro como administrador. Em seguida, execute as etapas a seguir.

4. Selecione o **ícone de configurações** no canto superior direito da página.

    ![Selecione o ícone de configurações no JAMF pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Selecione **logon único**.

    ![Selecionar logon único no JAMF pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na página **logon único** , execute as etapas a seguir.

    ![A página de logon único no JAMF pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Marque a caixa de seleção **habilitar autenticação de logon único** .

    b. Selecione **outro** como uma opção no menu suspenso **provedor de identidade** .

    c. No campo **outro provedor** , insira **Azure ad**.

    d. Copie o valor **ID da entidade** e cole-o no campo **identificador (ID da entidade)** na seção **configuração básica do SAML** no portal do Azure.

    > [!NOTE]
    > Use o valor no `<SUBDOMAIN>` campo para concluir a URL de logon e a URL de resposta na seção de **configuração básica do SAML** no portal do Azure.

    e. Selecione **URL de metadados** no menu suspenso **fonte de metadados do provedor de identidade** . No campo que aparece, Cole o valor da **URL de metadados de Federação do aplicativo** que você copiou do portal do Azure.

7. Na mesma página, role para baixo até a seção **mapeamento de usuário** . Em seguida, execute as etapas a seguir.   

    ![A seção mapeamento de usuário da página logon único no JAMF pro.](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Selecione a opção **NameID** para **mapeamento de usuário do provedor de identidade**. Por padrão, essa opção é definida como **NameID**, mas você pode definir um atributo personalizado.

    b. Selecione **email** para **mapeamento de usuário do JAMF pro**. JAMF pro mapeia os atributos SAML enviados pelo IdP primeiro pelos usuários e, em seguida, por grupos. Quando um usuário tenta acessar o JAMF pro, o JAMF pro Obtém informações sobre o usuário do provedor de identidade e faz a correspondência com todas as contas de usuário do JAMF pro. Se a conta de usuário de entrada não for encontrada, o JAMF pro tentará fazer a correspondência por nome de grupo.

    c. Cole o valor `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` no campo **nome do atributo do grupo do provedor de identidade** .

    d. Selecione **permitir que os usuários ignorem a autenticação de logon único**. Como resultado, os usuários não serão redirecionados para a página de entrada do provedor de identidade para autenticação e poderão entrar no JAMF pro diretamente. Quando um usuário tenta acessar o JAMF pro por meio do provedor de identidade, ocorre a autenticação SSO iniciada pelo IdP e a autorização.

    e. Selecione **Guardar**.

### <a name="create-a-jamf-pro-test-user"></a>Criar um usuário de teste do JAMF pro

Para que os usuários do AD do Azure entrem no JAMF pro, eles devem ser provisionados no JAMF pro. O provisionamento no JAMF pro é uma tarefa manual.

Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Entre no site da empresa do JAMF pro como administrador.

2. Selecione o ícone de **configurações** no canto superior direito da página.

    ![O ícone de configurações no JAMF pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Selecione **contas de usuário do JAMF Pro & grupos**.

    ![O ícone contas de usuário do JAMF pro & grupos nas configurações do JAMF pro](./media/jamfprosamlconnector-tutorial/user1.png)

4. Selecione **Novo**.

    ![Página de configurações do sistema & contas de usuário do JAMF pro](./media/jamfprosamlconnector-tutorial/user2.png)

5. Selecione **criar conta padrão**.

    ![A opção Criar conta padrão na página grupos de & de contas de usuário do JAMF pro](./media/jamfprosamlconnector-tutorial/user3.png)

6. Na caixa de diálogo **nova conta** , execute as etapas a seguir.

    ![Novas opções de configuração de conta nas configurações do sistema JAMF pro](./media/jamfprosamlconnector-tutorial/user4.png)

    a. No campo **nome** de usuário, `Britta Simon`insira, o nome completo do usuário de teste.

    b. Selecione as opções de **nível de acesso**, **conjunto de privilégios**e **status de acesso** que estão de acordo com sua organização.

    c. No campo **nome completo** , digite `Britta Simon`.

    d. No campo **endereço de email** , insira o endereço de email da conta de Brenda Simon.

    e. No campo **senha** , insira a senha do usuário.

    f. No campo **verificar senha** , insira a senha do usuário novamente.

    g. Selecione **Guardar**.

## <a name="test-the-sso-configuration"></a>Testar a configuração de SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco JAMF pro no painel de acesso, você deve entrar automaticamente na conta do JAMF pro para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integração de aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Logon único para aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o JAMF pro com o Azure AD](https://aad.portal.azure.com/)

