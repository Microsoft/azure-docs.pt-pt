---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Salesforce | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63cc4b902c0bd0281228e23076be6e0a18461597
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241423"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Salesforce

Neste tutorial, você aprenderá a integrar o Salesforce ao Azure Active Directory (Azure AD). Ao integrar o Salesforce ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Salesforce.
* Habilite seus usuários a serem conectados automaticamente ao Salesforce com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Salesforce.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Salesforce dá suporte ao SSO iniciado pelo **SP**

* O Salesforce dá suporte ao provisionamento **de usuário just in time**

* O Salesforce dá suporte ao [provisionamento **automatizado** de usuários](salesforce-provisioning-tutorial.md)

* Agora, o aplicativo móvel do Salesforce pode ser configurado com o Azure AD para habilitar o SSO. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

## <a name="adding-salesforce-from-the-gallery"></a>Adicionando o Salesforce da Galeria

Para configurar a integração do Salesforce ao Azure AD, você precisará adicionar o Salesforce da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Salesforce** na caixa de pesquisa.
1. Selecione **Salesforce** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Configurar e testar o logon único do Azure AD para o Salesforce

Configure e teste o SSO do Azure AD com o Salesforce usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Salesforce.

Para configurar e testar o SSO do Azure AD com o Salesforce, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
2. **[Configurar o SSO do Salesforce](#configure-salesforce-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Salesforce](#create-salesforce-test-user)** – para ter um equivalente de B. Simon no Salesforce que esteja vinculado à representação de usuário do Azure AD.
3. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Salesforce, execute as seguintes etapas:

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Salesforce** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    a. Na caixa de texto **URL de logon** , digite o valor usando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Na caixa de texto **identificador** , digite o valor usando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do Salesforce](https://help.salesforce.com/support) para obter esses valores.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o Salesforce** , copie as URLs apropriadas de acordo com seu requisito.

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
   
    > [!NOTE]
    > Os atributos de usuário do Salesforce diferenciam maiúsculas de minúsculas para validação de SAML.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Salesforce.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Salesforce**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-salesforce-sso"></a>Configurar SSO do Salesforce

1. Abra uma nova guia no navegador e entre em sua conta de administrador do Salesforce.

2. Clique na **configuração** no **ícone configurações** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/configure1.png)

3. Role para baixo até as **configurações** no painel de navegação, clique em **identidade** para expandir a seção correspondente. Em seguida, clique em **configurações de logon único**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso.png)

4. Na página **configurações de logon único** , clique no botão **Editar** .

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Se não for possível habilitar as configurações de logon único para sua conta do Salesforce, talvez seja necessário entrar em contato com a [equipe de suporte ao cliente do Salesforce](https://help.salesforce.com/support).

5. Selecione **SAML habilitado**e, em seguida, clique em **salvar**.

      ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-enable-saml.png)

6. Para configurar suas configurações de logon único do SAML, clique em **novo do arquivo de metadados**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Clique em **escolher arquivo** para carregar o arquivo XML de metadados que você baixou do portal do Azure e clique em **criar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/xmlchoose.png)

8. Na página **configurações de logon único do SAML** , os campos são preenchidos automaticamente e clique em salvar.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/salesforcexml.png)

9. No painel de navegação à esquerda no Salesforce, clique em **configurações da empresa** para expandir a seção relacionada e clique em **meu domínio**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-my-domain.png)

10. Role para baixo até a seção **configuração de autenticação** e clique no botão **Editar** .

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. Na seção **configuração de autenticação** , verifique o **AzureSSO** como **serviço de autenticação** da sua configuração de SSO do SAML e, em seguida, clique em **salvar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se mais de um serviço de autenticação estiver selecionado, os usuários receberão uma solicitação para selecionar a qual serviço de autenticação ele deseja entrar enquanto inicia o logon único no ambiente do Salesforce. Se você não quiser que ele aconteça, **deixe todos os outros serviços de autenticação desmarcados**.

### <a name="create-salesforce-test-user"></a>Criar usuário de teste do Salesforce

Nesta seção, um usuário chamado B. Simon é criado no Salesforce. O Salesforce dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Salesforce, um novo será criado quando você tentar acessar o Salesforce. O Salesforce também dá suporte ao provisionamento automático de usuário. você pode encontrar mais detalhes [aqui](salesforce-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Salesforce no painel de acesso, você deverá ser conectado automaticamente ao Salesforce para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-salesforce-mobile"></a>Testar SSO para Salesforce (móvel)

1. Abra o aplicativo móvel do Salesforce. Na página de entrada, clique em **usar domínio personalizado**.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app1.png)

1. Na caixa de texto **domínio personalizado** , insira seu nome de domínio personalizado registrado e clique em **continuar**.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app2.png)

1. Insira suas credenciais do Azure AD para entrar no aplicativo Salesforce e clique em **Avançar**.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app3.png)

1. Na página **permitir acesso** , conforme mostrado abaixo, clique em **permitir** para conceder acesso ao aplicativo Salesforce.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app4.png)

1. Por fim, após entrar com êxito, a home page do aplicativo será exibida.

    ![aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app5.png) ![aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar provisionamento de usuário](salesforce-provisioning-tutorial.md)

- [Experimente o Salesforce com o Azure AD](https://aad.portal.azure.com)
