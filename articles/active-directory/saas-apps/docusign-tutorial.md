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
ms.openlocfilehash: 8f02492dd7c9563c8c0002a63f0b105bd0be8b14
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345566"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com DocuSign

Neste tutorial, você aprenderá a integrar o DocuSign com o Microsoft Azure Active Directory (Azure AD). Ao integrar o DocuSign ao Azure AD, você pode:

* Use o Azure AD para controlar quem tem acesso ao DocuSign.
* Habilite a entrada automática no DocuSign para seus usuários por meio de suas contas do Azure AD.
* Gerencie suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS (software como serviço) com o Azure AD, consulte [logon único para aplicativos no Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do DocuSign que é habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste para verificar se:

* O DocuSign dá suporte ao SSO iniciado pelo SP (Service Provider).

* O DocuSign dá suporte ao provisionamento de usuário *just-in-time* .

* O DocuSign dá suporte ao [provisionamento automático de usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial).

## <a name="adding-docusign-from-the-gallery"></a>Adicionando o DocuSign da Galeria

Para configurar a integração do DocuSign ao Azure AD, você deve adicionar o DocuSign da Galeria à sua lista de aplicativos SaaS gerenciados:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou usando uma conta Microsoft pessoal.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Vá para **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **DocuSign** na caixa de pesquisa.
1. Selecione **DocuSign** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Configurar e testar o logon único do Azure AD para o DocuSign

Configure e teste o SSO do Azure AD com o DocuSign usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você deve estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente no DocuSign.

Para configurar e testar o SSO do Azure AD com o DocuSign, conclua os seguintes blocos de construção:

1. [Configure o SSO do Azure ad](#configure-azure-ad-sso) para que os usuários possam usar esse recurso.
    1. [Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B. Simon.
    1. [Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user) para habilitar B. Simon para usar o logon único do Azure AD.
1. [Configure o SSO do DocuSign](#configure-docusign-sso) para configurar as configurações de logon único no lado do aplicativo.
1. [Crie um usuário de teste do DocuSign](#create-docusign-test-user) para gerar um equivalente de B. Simon em DocuSign que esteja vinculado à representação do usuário no Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Para habilitar o SSO do Azure AD no portal do Azure, siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **DocuSign** , localize a seção **gerenciar** e, em seguida, selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , selecione o ícone de caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , siga estas etapas:

    a. Na caixa **URL de logon** , insira uma URL usando o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Na caixa **identificador (ID da entidade)** , insira uma URL usando o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Esses valores entre colchetes são espaços reservados. Substitua-os pelos valores na URL de entrada e no identificador reais. Esses detalhes são explicados na seção "Exibir pontos de extremidade SAML 2,0" mais adiante neste tutorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** . Selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar DocuSign** , copie a URL (ou URLs) apropriada com base em seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste chamado B. Simon no portal do Azure.

1. No painel esquerdo da portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Na parte superior da tela, selecione **novo usuário**.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **nome** , digite **B. Simon**.  
   1. No campo **nome de usuário** , digite `<username>@<companydomain>.<extension>`. Por exemplo: `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa **senha** .
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você concederá a B. Simon acesso ao DocuSign para que esse usuário possa usar o logon único do Azure.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **DocuSign**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, na caixa de diálogo **Adicionar atribuição** , selecione **usuários e grupos**.

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista **usuários** e, em seguida, pressione o botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, pressione o botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , selecione o botão **atribuir** .

## <a name="configure-docusign-sso"></a>Configurar o SSO do DocuSign

1. Para automatizar a configuração no DocuSign, você deve instalar a extensão do navegador de entrada seguro meus aplicativos selecionando **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecione **Setup DocuSign**. Você é direcionado para o aplicativo DocuSign. A partir daí, forneça as credenciais de administrador para entrar no DocuSign. A extensão do navegador configura automaticamente o aplicativo e automatiza as etapas 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o DocuSign manualmente, abra uma nova janela do navegador da Web e entre em seu site de empresa do DocuSign como administrador.

4. No canto superior direito da página, selecione o logotipo do perfil e, em seguida, selecione **ir para administrador**.
  
    ![Ir para o administrador em perfil][51]

5. Na página soluções de domínio, selecione **domínios**.

    ![Soluções/domínios de domínio][50]

6. Na seção **domínios** , selecione **domínio de declaração**.

    ![Opção de domínio de declaração][52]

7. Na caixa de diálogo **declarar um domínio** , na caixa **nome de domínio** , digite o domínio da empresa e, em seguida, selecione **declaração**. Verifique se você verificou o domínio e se seu status está ativo.

    ![Declaração de uma caixa de diálogo de nome de domínio/domínio][53]

8. Na página soluções de domínio, selecione **provedores de identidade**.
  
    ![Opção de provedores de identidade][54]

9. Na seção **provedores de identidade** , selecione **Adicionar provedor de identidade**.

    ![Adicionar opção de provedor de identidade][55]

10. Na página **configurações do provedor de identidade** , siga estas etapas:

    ![Campos de configurações do provedor de identidade][56]

    a. Na caixa **nome** , digite um nome exclusivo para a sua configuração. Não use espaços.

    b. Na **caixa emissor do provedor de identidade**, Cole o valor do identificador do **Azure ad** que você copiou do portal do Azure.

    c. Na caixa **URL de logon do provedor de identidade** , Cole o valor da **URL de logon** que você copiou do portal do Azure.

    d. Na caixa **URL de logout do provedor de identidade** , Cole o valor da **URL de logout**, que você copiou do portal do Azure.

    e. Selecione **assinar solicitação Authn**.

    f. Para **Enviar solicitação Authn por**, selecione **post**.

    g. Para **Enviar solicitação de logout por**, selecione **obter**.

    h. Na seção **mapeamento de atributo personalizado** , selecione **Adicionar novo mapeamento**.

       ![Interface do usuário de mapeamento de atributo personalizado][62]

    i. Escolha o campo que você deseja mapear para a declaração do Azure AD. Neste exemplo, a Declaração **EmailAddress** é mapeada com o valor de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`. Esse é o nome de declaração padrão do Azure AD para a declaração de email. Selecione **salvar**.

       ![Campos de mapeamento de atributo personalizado][57]

       > [!NOTE]
       > Use o **identificador de usuário** apropriado para mapear o usuário do Azure ad para o mapeamento de usuário do DocuSign. Selecione o campo apropriado e insira o valor apropriado com base nas configurações da sua organização.

    j. Na seção **certificados do provedor de identidade** , selecione **Adicionar certificado**, carregue o certificado que você baixou do portal do Azure AD e selecione **salvar**.

       ![Certificados do provedor de identidade/adicionar certificado][58]

    k. Na seção **provedores de identidade** , selecione **ações**e, em seguida, selecione **pontos de extremidade**.

       ![Provedores de identidade/pontos de extremidade][59]

    l. Na seção **Exibir pontos de extremidade SAML 2,0** do portal de administração do DocuSign, siga estas etapas:
       1. Copie a **URL do emissor do provedor de serviços**e cole-a na caixa **identificador** na seção **configuração básica do SAML** no portal do Azure.

       1. Copie a **URL de logon do provedor de serviços**e cole-a na caixa **URL de logon** na seção **configuração básica do SAML** no portal do Azure.

       1. Selecione **fechar**.

       ![Exibir pontos de extremidade SAML 2,0][60]

### <a name="create-docusign-test-user"></a>Criar usuário de teste do DocuSign

Nesta seção, um usuário chamado B. Simon é criado em DocuSign. O DocuSign dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no DocuSign, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do DocuSign](https://support.docusign.com/).

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco DocuSign no painel de acesso, você deverá ser conectado automaticamente à instância do DocuSign para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar aplicativos SaaS com o Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e o logon único no Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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
