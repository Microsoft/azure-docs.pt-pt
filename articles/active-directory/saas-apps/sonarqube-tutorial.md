---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com Sonarqube | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sonarqube.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b01665e7-c3d0-4393-9286-d5bcf8cf6add
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b9e28f986f28bde6e46319ddb404d424c100726
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174646"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com Sonarqube

Neste tutorial, você aprenderá a integrar o Sonarqube com o Azure Active Directory (Azure AD). Ao integrar o Sonarqube ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Sonarqube.
* Habilite seus usuários a serem conectados automaticamente ao Sonarqube com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Sonarqube.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Sonarqube dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-sonarqube-from-the-gallery"></a>Adicionando o Sonarqube da Galeria

Para configurar a integração do Sonarqube ao Azure AD, você precisará adicionar o Sonarqube da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Sonarqube** na caixa de pesquisa.
1. Selecione **Sonarqube** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sonarqube"></a>Configurar e testar o logon único do Azure AD para o Sonarqube

Configure e teste o SSO do Azure AD com o Sonarqube usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Sonarqube.

Para configurar e testar o SSO do Azure AD com o Sonarqube, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Sonarqube](#configure-sonarqube-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Sonarqube](#create-sonarqube-test-user)** – para ter um equivalente de B. Simon em Sonarqube que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Sonarqube** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL:

    * **Para o ambiente de produção**

        `https://servicessonar.corp.microsoft.com/`

    * **Para o ambiente de desenvolvimento**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar Sonarqube** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Sonarqube.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Sonarqube**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-sonarqube-sso"></a>Configurar o SSO do Sonarqube

1. Abra uma nova janela do navegador da Web e entre no site da empresa do Sonarqube como um administrador.

2. Instale o plug-in SAML do sonarqube Market Place.

3. Na parte superior esquerda da página, clique em **admin** e, em seguida, navegue até **SAML**.

4. Na página **SAML** , execute as seguintes etapas:

    ![Configuração do Sonarqube](./media/sonarqube-tutorial/config01.png)

    a. Alterne a opção **habilitado** para **Sim**.

    b. Na caixa de texto **ID do aplicativo** , digite o nome como **sonarqube**.

    c. Na caixa de texto **nome do provedor** , digite o nome como **SAML**.

    d. Na caixa de texto **ID do provedor** , Cole o valor do **identificador do Azure ad**que você copiou do portal do Azure.

    e. Na caixa de texto **URL de logon do SAML** , Cole o valor da URL de **logon**copiado de portal do Azure.

    f. Abra o certificado codificado em base64 no bloco de notas, copie seu conteúdo e cole-o na caixa de texto **certificado do provedor** .

    g. Na caixa de texto **atributo de logon de usuário** do SAML `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`, insira o valor.

    h. Na caixa de texto **atributo de nome de usuário** do SAML `http://schemas.microsoft.com/identity/claims/displayname`, insira o valor.

    i. Na caixa de texto **atributo de email do usuário SAML** , `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`Insira o valor.

    j. Clique em **Guardar**.

### <a name="create-sonarqube-test-user"></a>Criar usuário de teste do Sonarqube

Nesta seção, você criará um usuário chamado B. Simon em Sonarqube. Trabalhe com a [equipe de suporte ao cliente do Sonarqube](https://www.sonarsource.com/support/) para adicionar os usuários na plataforma do Sonarqube. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Sonarqube no painel de acesso, você deverá ser conectado automaticamente ao Sonarqube para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Sonarqube com o Azure AD](https://aad.portal.azure.com/)

