---
title: 'Tutorial: Azure Ative Directory integração individual de inscrição (SSO) com o Kit de Ferramentas Azure AD SAML  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Azure AD AD Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1aa2d58a40c623cc451ca30aaa9d75a4a6b3d4c0
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983883"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o Kit de Ferramentas Azure AD SAML

Neste tutorial, você vai aprender a integrar o Azure AD AD Toolkit com o Azure Ative Directory (Azure AD). Quando integrar o Kit de Ferramentas Azure AD SAML com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Kit de Ferramentas Azure AD SAML.
* Ative que os seus utilizadores sejam automaticamente inscritos no Kit de Ferramentas Azure AD SAML com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* A assinatura ativada por um único sinal (SSO) do Azure AD Toolkit (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Kit de ferramentas Azure AD SAML suporta **SP** iniciado SSO

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Adicionar kit de ferramentas Azure AD SAML da galeria

Para configurar a integração do Kit de Ferramentas Azure AD SAML no Azure AD, é necessário adicionar o Kit de Ferramentas Azure AD SAML da galeria à sua lista de aplicações geridas saaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **de galeria,** digite o Kit de **Ferramentas Azure AD SAML** na caixa de pesquisa.
1. Selecione **Azure AD AD Toolkit** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Configure e teste azure AD único sinal para Azure AD SAML Toolkit

Configure e teste Azure AD SSO com Kit de Ferramentas Azure AD SAML utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Kit de Ferramentas Azure AD SAML.

Para configurar e testar o Azure AD SSO com o Kit de Ferramentas Azure AD SAML, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure Azure AD SAML Toolkit SSO](#configure-azure-ad-saml-toolkit-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. Crie o utilizador de teste de **[Toolkit Azure AD SAML](#create-azure-ad-saml-toolkit-test-user)** - para ter uma contrapartida de B.Simon no Kit de Ferramentas Azure AD SAML que está ligado à representação do utilizador da AD Azure.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de ferramentas **Azure AD SAML,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações. Pode recuperar as definições utilizando a página de configuração SAML Toolkit SAML. 

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. No **Sign on URL** text box, escreva um URL: `https://samltoolkit.azurewebsites.net/SAML/Login/<3digitnumber>`

    b. Na caixa de texto **identificador (Id** da entidade), escreva um URL: `https://samltoolkit.azurewebsites.net`

    c. Na caixa de texto **URL resposta,** escreva um URL: `https://samltoolkit.azurewebsites.net/SAML/Consume/<3digitnumber>`

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize **certificado (bruto)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificateraw.png)

1. Na secção **de conjunto azure AD AD Toolkit,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on Azure, concedendo acesso ao Kit de Ferramentas Azure AD SAML.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Azure AD SAML Toolkit**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Configure Azure AD SAML Toolkit SSO

1. Abra uma nova janela do navegador web, caso não tenha registado no website do Azure AD SAML Toolkit, primeiro registe-se clicando no **Registo**. Se já se registou, inscreva-se no site da empresa Azure AD SAML Toolkit utilizando o sinal registado em credenciais.

    ![Registo de Toolkit Azure AD SAML](./media/saml-toolkit-tutorial/register.png)

1. Clique na **configuração SAML**.

    ![Configuração SAML Do kit de ferramentas Azure AD SAML](./media/saml-toolkit-tutorial/saml-configure.png)

1. Clique em **Criar**.

    ![Kit de ferramentas Azure AD SAML Criar SSO](./media/saml-toolkit-tutorial/createsso.png)

1. Na página de **Configuração SAML SSO,** execute os seguintes passos:

    ![Kit de ferramentas Azure AD SAML Criar SSO](./media/saml-toolkit-tutorial/fill-details.png)

    1. Na caixa de texto **login URL,** colá o valor URL de **Login,** que copiou do portal Azure.

    1. Na caixa de texto **Azure AD Identifier,** colá o valor do **Identificador AD Azure,** que copiou do portal Azure.

    1. Na caixa de texto **logout URL,** colá o valor URL de **Logout,** que copiou do portal Azure.

    1. Clique **em Escolher File** e faça upload do ficheiro Certificado **(Raw)** que descarregou a partir do portal Azure.

    1. Clique em **Criar**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Criar o utilizador de teste de toolkit Azure AD SAML

Nesta secção, um utilizador chamado B.Simon é criado no Kit de Ferramentas Azure AD SAML. O Kit de Ferramentas Azure AD SAML suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para você nesta seção. Se um utilizador já não existir no Azure AD SAML Toolkit, um novo é criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Azure AD SAML Toolkit no Painel de Acesso, deve ser automaticamente inscrito no Kit de Ferramentas Azure AD SAML para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Kit de Ferramentas Azure AD SAML com AD Azure](https://aad.portal.azure.com/)
