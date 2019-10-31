---
title: 'Tutorial: integração do Azure Active Directory com o Uberflip | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 6710344e73e3c0ea6f9b3491209689871cc14a4f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160950"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Tutorial: integração do Azure Active Directory com o Uberflip

Neste tutorial, você aprenderá a integrar o Uberflip com o Azure Active Directory (Azure AD).

A integração do Uberflip ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Uberflip.
* Você pode permitir que seus usuários sejam conectados automaticamente ao Uberflip (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central: o portal do Azure.

Para obter detalhes sobre a integração de aplicativos SaaS (software como serviço) com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Uberflip, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura do Uberflip com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

O Uberflip dá suporte aos seguintes recursos:

* SSO (logon único) iniciado por SP e iniciada pelo IDP.
* Provisionamento de usuário just-in-time.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Adicionar o Uberflip do Azure Marketplace

Para configurar a integração do Uberflip ao Azure AD, você precisará adicionar o Uberflip do Azure Marketplace à sua lista de aplicativos SaaS gerenciados:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No painel esquerdo, selecione **Azure Active Directory**.

   ![A opção Azure Active Directory](common/select-azuread.png)

1. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

   ![O painel aplicativos empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **+ novo aplicativo** na parte superior do painel.

   ![A opção novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, digite **Uberflip**. Nos resultados da pesquisa, selecione **Uberflip**e, em seguida, selecione **Adicionar** para adicionar o aplicativo.

   ![Uberflip na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Uberflip, com base em um usuário de teste chamado **B Simon**. Para que o logon único funcione, você precisa estabelecer um vínculo entre um usuário do Azure AD e um usuário relacionado no Uberflip.

Para configurar e testar o logon único do Azure AD com o Uberflip, você precisa concluir os seguintes blocos de construção:

1. **[Configure o logon único do Azure ad](#configure-azure-ad-single-sign-on)** para habilitar seus usuários a usar esse recurso.
1. **[Configure o logon único do Uberflip](#configure-uberflip-single-sign-on)** para definir as configurações de logon único no lado do aplicativo.
1. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
1. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Crie um usuário de teste do Uberflip](#create-an-uberflip-test-user)** para que haja um usuário chamado b. Simon em Uberflip que esteja vinculado ao usuário do Azure AD chamado b. Simon.
1. **[Teste o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Uberflip, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Uberflip** , selecione **logon único**.

    ![Configurar opção de logon único](common/select-sso.png)

1. No painel **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. No painel **Configurar logon único com SAML** , selecione **Editar** (o ícone de lápis) para abrir o painel **configuração básica do SAML** .

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. No painel **configuração básica do SAML** , execute uma das etapas a seguir, dependendo do modo de SSO que você deseja configurar:

   * Para configurar o aplicativo no modo SSO iniciado pelo IDP, na caixa **URL de resposta (URL de serviço do consumidor de asserção)** , insira uma URL usando o seguinte padrão:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Informações de logon único de domínio e URLs do Uberflip](common/both-replyurl.png)

     > [!NOTE]
     > Esse valor não é real. Atualize esse valor com a URL de resposta real. Para obter o valor real, entre em contato com a [equipe de suporte do Uberflip](mailto:support@uberflip.com). Você também pode consultar os padrões mostrados no painel **configuração básica do SAML** no portal do Azure.

   * Para configurar o aplicativo no modo SSO iniciado pelo SP, selecione **definir URLs adicionais**e, na caixa **URL de logon** , digite esta URL:

     `https://app.uberflip.com/users/login`

     ![Informações de logon único de domínio e URLs do Uberflip](common/both-signonurl.png)

1. No painel **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , selecione **baixar** para baixar o XML de metadados de **Federação** das opções especificadas e salvá-lo em seu computador.

   ![A opção de download de XML de metadados de Federação](common/metadataxml.png)

1. No painel **Configurar Uberflip** , copie a URL ou as URLs necessárias:

   * **URL de logon**
   * **Identificador do Azure AD**
   * **URL de logout**

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Configurar o logon único do Uberflip

Para configurar o logon único no lado do Uberflip, é necessário enviar o XML de metadados de Federação baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do Uberflip](mailto:support@uberflip.com). A equipe do Uberflip verificará se a conexão de SSO do SAML está definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você cria um usuário de teste chamado B. Simon no portal do Azure.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**  > **usuários**  >  todos os**usuários**.

    ![As opções usuários e "todos os usuários"](common/users.png)

1. Na parte superior da tela, selecione **+ novo usuário**.

    ![Nova opção de usuário](common/new-user.png)

1. No painel **usuário** , execute as seguintes etapas:

    ![O painel de usuário](common/user-properties.png)

    1. Na caixa **nome** , digite **BSimon**.
  
    1. Na caixa **nome de usuário** , digite **BSimon\@\<yourcompanydomain >\<extensão >** . Por exemplo, **BSimon\@contoso.com**.

    1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Uberflip.

1. Na portal do Azure, selecione **aplicativos empresariais** > **todos os aplicativos** > **Uberflip**.

    ![Painel aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **Uberflip**.

    ![Uberflip na lista de aplicativos](common/all-applications.png)

1. No painel esquerdo, em **gerenciar**, selecione **usuários e grupos**.

    ![A opção "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **+ Adicionar usuário**e, em seguida, selecione **usuários e grupos** no painel **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

1. No painel **usuários e grupos** , selecione **B Simon** na lista **usuários** e escolha **selecionar** na parte inferior do painel.

1. Se você estiver esperando um valor de função na Asserção SAML, no painel **selecionar função** , selecione a função apropriada para o usuário na lista. Na parte inferior do painel, escolha **selecionar**.

1. No painel **Adicionar atribuição** , selecione **atribuir**.

### <a name="create-an-uberflip-test-user"></a>Criar um usuário de teste do Uberflip

Um usuário chamado B. Simon agora é criado em Uberflip. Você não precisa fazer nada para criar esse usuário. O Uberflip dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Se um usuário chamado B. Simon ainda não existir no Uberflip, um novo será criado após a autenticação.

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o portal meus aplicativos.

Ao selecionar **Uberflip** no portal meus aplicativos, você deverá entrar automaticamente na assinatura do Uberflip para a qual você configurou o logon único. Para obter mais informações sobre o portal meus aplicativos, consulte [acessar e usar aplicativos no portal meus aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integração de aplicativos SaaS com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

* [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
