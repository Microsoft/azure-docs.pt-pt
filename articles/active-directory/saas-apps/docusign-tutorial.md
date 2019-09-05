---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com DocuSign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe1f018612e889f49993895d88cdaf9ad732b393
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306302"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com DocuSign

Neste tutorial, você aprenderá a integrar o DocuSign com o Azure Active Directory (Azure AD). Ao integrar o DocuSign ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao DocuSign.
* Habilite seus usuários a serem conectados automaticamente ao DocuSign com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do DocuSign.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O DocuSign dá suporte ao SSO iniciado por **SP**

* O DocuSign dá suporte ao provisionamento de usuário **just-in-time**

* O DocuSign dá suporte ao [provisionamento automático de usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial)

## <a name="adding-docusign-from-the-gallery"></a>Adicionando o DocuSign da Galeria

Para configurar a integração do DocuSign ao Azure AD, você precisará adicionar o DocuSign da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **DocuSign** na caixa de pesquisa.
1. Selecione **DocuSign** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Configurar e testar o logon único do Azure AD para o DocuSign

Configure e teste o SSO do Azure AD com o DocuSign usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no DocuSign.

Para configurar e testar o SSO do Azure AD com o DocuSign, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do DocuSign](#configure-docusign-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do DocuSign](#create-docusign-test-user)** – para ter um equivalente de B. Simon em DocuSign que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **DocuSign** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon e o identificador reais que são explicados posteriormente na seção **Exibir pontos de extremidade 2,0 do SAML** no tutorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar DocuSign** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao DocuSign.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **DocuSign**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-docusign-sso"></a>Configurar o SSO do DocuSign

1. Para automatizar a configuração no DocuSign, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Setup DocuSign** irá direcioná-lo para o aplicativo DocuSign. A partir daí, forneça as credenciais de administrador para entrar no DocuSign. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-5.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o DocuSign manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do DocuSign como administrador e execute as seguintes etapas:

4. No canto superior direito da página, clique no **logotipo** do perfil e, em seguida, clique em **ir para administrador**.
  
    ![Configurando o logon único][51]

5. Na página soluções de domínio, clique em **domínios**

    ![Configurando o logon único][50]

6. Na seção **domínios** , clique em **declarar domínio**.

    ![Configurando o logon único][52]

7. Na caixa de diálogo **declarar um domínio** , na TextBox **nome de domínio** , digite o domínio da empresa e clique em **reivindicação**. Verifique se você verificou o domínio e se o status está ativo.

    ![Configurando o logon único][53]

8. Na página soluções de domínio, clique em **provedores de identidade**.
  
    ![Configurando o logon único][54]

9. Na seção **provedores de identidade** , clique em **Adicionar provedor de identidade**. 

    ![Configurando o logon único][55]

10. Na página **configurações do provedor de identidade** , execute as seguintes etapas:

    ![Configurando o logon único][56]

    a. Na caixa de texto **nome** , digite um nome exclusivo para a sua configuração. Não use espaços.

    b. Na **caixa de texto emissor do provedor de identidade**, Cole o valor do identificador do **Azure ad**que você copiou do portal do Azure.

    c. Na caixa de texto **URL de logon do provedor de identidade** , Cole o valor da URL de **logon**copiado de portal do Azure.

    d. Na caixa de texto **URL de logout do provedor de identidade** , Cole o valor da URL de **logout**copiado de portal do Azure.

    e. Selecione **assinar solicitação Authn**.

    f. **Para enviar solicitação Authn por**, selecione **post**.

    g. Como **Enviar solicitação de logout por**, selecione **obter**.

    h. Na seção **mapeamento de atributo personalizado** , clique em **Adicionar novo mapeamento**.

    ![Configurando o logon único][62]

    i. Escolha o campo que você deseja mapear com a declaração do Azure AD. Neste exemplo, a Declaração **EmailAddress** é mapeada com o valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . É o nome de declaração padrão do Azure AD para declaração de email e, em seguida, clica em **salvar**.

    ![Configurando o logon único][57]

    > [!NOTE]
    > Use o **identificador de usuário** apropriado para mapear o usuário do Azure ad para o mapeamento de usuário do DocuSign. Selecione o campo apropriado e insira o valor apropriado com base nas configurações da sua organização.

    j. Na seção **certificados do provedor de identidade** , clique em **Adicionar certificado**e, em seguida, carregue o certificado que você baixou do portal do Azure AD e clique em **salvar**.

    ![Configurando o logon único][58]

    k. Na seção **provedores de identidade** , clique em **ações**e em **pontos de extremidade**.

    ![Configurando o logon único][59]

    l. Na seção **Exibir pontos de extremidade SAML 2,0** no **portal de administração do DocuSign**, execute as seguintes etapas:

    ![Configurando o logon único][60]

    * Copie a **URL do emissor do provedor de serviços**e cole-a na caixa de texto **identificador** na seção **configuração básica do SAML** no portal do Azure.

    * Copie a **URL de logon do provedor de serviços**e cole-a na caixa de texto **URL de logon** na seção **configuração básica do SAML** no portal do Azure.

    * Clique em **fechar**

### <a name="create-docusign-test-user"></a>Criar usuário de teste do DocuSign

Nesta seção, um usuário chamado B. Simon é criado em DocuSign. O DocuSign dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no DocuSign, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do DocuSign](https://support.docusign.com/).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do DocuSign no painel de acesso, você deverá ser conectado automaticamente ao DocuSign para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o DocuSign com o Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png