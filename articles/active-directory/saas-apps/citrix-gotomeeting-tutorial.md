---
title: 'Tutorial: integração do Azure Active Directory com o GoToMeeting | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2835fda7b709ded9fac5503d4ba0bf4b8bdd5ec
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290670"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-gotomeeting"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o GoToMeeting

Neste tutorial, você aprenderá a integrar o GoToMeeting com o Azure Active Directory (Azure AD). Ao integrar o GoToMeeting ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao GoToMeeting.
* Habilite seus usuários a serem conectados automaticamente ao GoToMeeting com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do GoToMeeting.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O GoToMeeting dá suporte ao SSO iniciado pelo **IDP** .
* Depois de configurar o GoToMeeting, você pode impor controles de sessão, que protegem vazamento e pós-infiltração dos dados confidenciais de sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-gotomeeting-from-the-gallery"></a>Adicionando o GoToMeeting da Galeria

Para configurar a integração do GoToMeeting ao Azure AD, você precisará adicionar o GoToMeeting da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **GoToMeeting** na caixa de pesquisa.
1. Selecione **GoToMeeting** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-gotomeeting"></a>Configurar e testar o logon único do Azure AD para o GoToMeeting

Configure e teste o SSO do Azure AD com o GoToMeeting usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no GoToMeeting.

Para configurar e testar o SSO do Azure AD com o GoToMeeting, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do GoToMeeting](#configure-gotomeeting-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do GoToMeeting](#create-gotomeeting-test-user)** – para ter um equivalente de B. Simon em GoToMeeting que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **GoToMeeting** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://authentication.logmeininc.com/saml/sp`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://authentication.logmeininc.com/saml/acs`

    c. Clique em **definir URLs adicionais** e configure as URLs abaixo

    d. **URL de logon** (Mantenha isso em branco)

    e. Na caixa de texto **relaystate** , digite uma URL usando o seguinte padrão:

   - Para o aplicativo GoToMeeting, use `https://global.gotomeeting.com`

   - Para GoToTraining, use `https://global.gototraining.com`

   - Para GoToWebinar, use `https://global.gotowebinar.com` 

   - Para GoToAssist, use `https://app.gotoassist.com`

     > [!NOTE]
     > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do GoToMeeting](https://go.microsoft.com/fwlink/?linkid=845985) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na seção **Configurar GoToMeeting** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão


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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao GoToMeeting.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **GoToMeeting**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-gotomeeting-sso"></a>Configurar o SSO do GoToMeeting

1. Em uma janela de navegador diferente, faça logon em seu [GoToMeeting Organization Center](https://organization.logmeininc.com/). Você será solicitado a confirmar que o IdP foi atualizado.

2. Habilite a caixa de seleção "meu provedor de identidade foi atualizado com o novo domínio". Clique em **concluído** quando terminar.

### <a name="create-gotomeeting-test-user"></a>Criar usuário de teste do GoToMeeting

Nesta seção, um usuário chamado Brenda Simon é criado em GoToMeeting. O GoToMeeting dá suporte ao provisionamento just-in-time, que é habilitado por padrão.

Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no GoToMeeting, um novo será criado quando você tentar acessar o GoToMeeting.

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do GoToMeeting](https://support.logmeininc.com/gotomeeting).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do GoToMeeting no painel de acesso, você deverá ser conectado automaticamente ao GoToMeeting para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o GoToMeeting com o Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o GoToMeeting com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
