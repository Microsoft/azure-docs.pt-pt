---
title: 'Tutorial: integração do Azure Active Directory com o Expensify | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9ed129bccbd763bf4459fa7818e0dba4e7e65b5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73156534"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Tutorial: integrar o Expensify ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Expensify com o Azure Active Directory (Azure AD). Ao integrar o Expensify ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Expensify.
* Habilite seus usuários a serem conectados automaticamente ao Expensify com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Expensify.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Expensify dá suporte ao SSO iniciado por **SP**

## <a name="adding-expensify-from-the-gallery"></a>Adicionando o Expensify da Galeria

Para configurar a integração do Expensify ao Azure AD, você precisará adicionar o Expensify da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Expensify** na caixa de pesquisa.
1. Selecione **Expensify** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-expensify"></a>Configurar e testar o logon único do Azure AD para o Expensify

Configure e teste o SSO do Azure AD com o Expensify usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Expensify.

Para configurar e testar o SSO do Azure AD com o Expensify, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
2. **[Configurar o SSO do Expensify](#configure-expensify-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Expensify](#create-expensify-test-user)** – para ter um equivalente de B. Simon em Expensify que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Expensify** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL: `https://www.expensify.com/authentication/saml/login`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL: `https://www.expensify.com`

    c. b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`

    > [!NOTE]
    > O valor da URL de resposta não é real. Atualize esse valor com a URL de resposta real. Contate a [equipe de suporte ao cliente do Expensify](mailto:help@expensify.com) para obter esse valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize **metadados XML** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/metadataxml.png)

1. Na seção **Configurar Expensify** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Expensify.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Expensify**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-expensify-sso"></a>Configurar o SSO do Expensify

Para habilitar o SSO no Expensify, primeiro você precisa habilitar o **controle de domínio** no aplicativo. Você pode habilitar o controle de domínio no aplicativo por meio das etapas listadas [aqui](https://help.expensify.com/domain-control). Para obter suporte adicional, trabalhe com a [equipe de suporte ao cliente do Expensify](mailto:help@expensify.com). Depois que o controle de domínio estiver habilitado, siga estas etapas:

![Configurar logon único](./media/expensify-tutorial/tutorial_expensify_51.png)

1. Faça logon em seu aplicativo Expensify.

2. No painel esquerdo, clique em **configurações** e navegue até **SAML**.

3. Alterne a opção de **logon do SAML** como **habilitada**.

4. Abra os metadados de Federação baixados do Azure AD no bloco de notas, copie o conteúdo e cole-o na caixa de texto **metadados do provedor de identidade** .

### <a name="create-expensify-test-user"></a>Criar usuário de teste do Expensify

Nesta seção, você criará um usuário chamado B. Simon em Expensify. Trabalhe com a [equipe de suporte ao cliente do Expensify](mailto:help@expensify.com) para adicionar os usuários na plataforma do Expensify.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Expensify no painel de acesso, você deverá ser conectado automaticamente ao Expensify para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)