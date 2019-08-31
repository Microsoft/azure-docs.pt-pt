---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com SKYSITE | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SKYSITE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 04c6a47a-1730-4acf-bc5c-a04daccff9b3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adbf57e0446820959113ba4f27cc2f93fd20fdd5
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173449"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-skysite"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com SKYSITE

Neste tutorial, você aprenderá a integrar o SKYSITE com o Azure Active Directory (Azure AD). Ao integrar o SKYSITE ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SKYSITE.
* Habilite seus usuários a serem conectados automaticamente ao SKYSITE com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SKYSITE.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* SKYSITE dá suporte ao SSO iniciado pelo **IDP**

* O SKYSITE dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-skysite-from-the-gallery"></a>Adicionando o SKYSITE da Galeria

Para configurar a integração do SKYSITE ao Azure AD, você precisará adicionar o SKYSITE da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SKYSITE** na caixa de pesquisa.
1. Selecione **SKYSITE** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-skysite"></a>Configurar e testar o logon único do Azure AD para o SKYSITE

Configure e teste o SSO do Azure AD com o SKYSITE usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SKYSITE.

Para configurar e testar o SSO do Azure AD com o SKYSITE, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do SKYSITE](#configure-skysite-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do SKYSITE](#create-skysite-test-user)** – para ter um equivalente de B. Simon em SKYSITE que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SKYSITE** , clique na **guia Propriedades** e execute a seguinte etapa: 

    ![Propriedades de logon único](./media/skysite-tutorial/config05.png)

    * Copie a **URL de acesso do usuário** e você precisa colá-la na **seção configurar SSO do SKYSITE**, que é explicada posteriormente no tutorial.

1. Na página de integração de aplicativos do **SKYSITE** , navegue até **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração** básica do SAML, o aplicativo é pré-configurado no modo iniciado pelo **IDP** e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. O aplicativo SKYSITE espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![image](common/edit-attribute.png)

1. Além de acima, o aplicativo SKYSITE espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **atributos de usuário & declarações** na caixa de diálogo **declarações de grupo (versão prévia)**  , execute as seguintes etapas:

    a. Clique na **caneta** ao lado dos **grupos retornados na declaração**.

    ![image](./media/skysite-tutorial/config01.png)

    ![image](./media/skysite-tutorial/config02.png)

    b. Selecione **todos os grupos** na lista de botões de opção.

    c. Selecione o **atributo de origem** da ID do **grupo**.

    d. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar SKYSITE** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao SKYSITE.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SKYSITE**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="configure-skysite-sso"></a>Configurar o SSO do SKYSITE

1. Abra uma nova janela do navegador da Web e entre no site da empresa do SKYSITE como administrador e execute as seguintes etapas:

4. Clique em **configurações** no lado superior direito da página e, em seguida, navegue até **configuração da conta**.

    ![Configuração](./media/skysite-tutorial/config03.png)

5. Alterne para a guia **SSO (logon único)** , execute as seguintes etapas:

    ![Configuração](./media/skysite-tutorial/config04.png)

    a. Na caixa de texto **URL de entrada do provedor de identidade** , Cole o valor da URL de acesso do **usuário**copiado da guia **Propriedades** em portal do Azure.

    b. Clique em **carregar certificado**para carregar o certificado codificado em base64 que você baixou do portal do Azure.

    c. Clique em **Guardar**.

### <a name="create-skysite-test-user"></a>Criar usuário de teste do SKYSITE

Nesta seção, um usuário chamado Brenda Simon é criado em SKYSITE. O SKYSITE dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no SKYSITE, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do SKYSITE no painel de acesso, você deverá ser conectado automaticamente ao SKYSITE para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SKYSITE com o Azure AD](https://aad.portal.azure.com/)

