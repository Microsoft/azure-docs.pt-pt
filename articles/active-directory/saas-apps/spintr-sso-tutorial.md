---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Spintr SSO  Integração de diretório so ativo) com spintr SSO  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Spintr SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 42907586-845b-40d9-9a6b-34af2fc8175b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/08/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b3b8a62e90ed086fc3caa11c1a9f6a99d0bec86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850119"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spintr-sso"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Spintr SSO

Neste tutorial, você vai aprender a integrar spintr SSO com O Diretório Ativo Azure (Azure AD). Quando integrar o Spintr SSO com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Spintr SSO.
* Ative que os seus utilizadores sejam automaticamente inscritos no Spintr SSO com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Spintr SSO única subscrição ativada por Spintr SSO (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Spintr SSO suporta **SP** iniciado SSO
* Spintr SSO suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-spintr-sso-from-the-gallery"></a>Adicionando Spintr SSO da galeria

Para configurar a integração do Spintr SSO no Azure AD, é necessário adicionar spintr SSO da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **galeria,** digite **Spintr SSO** na caixa de pesquisa.
1. Selecione **Spintr SSO** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spintr-sso"></a>Configure e teste Azure AD único sign-on para Spintr SSO

Configure e teste Azure AD SSO com Spintr SSO utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Spintr SSO.

Para configurar e testar o Azure AD SSO com spintr SSO, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure Spintr SSO](#configure-spintr-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador de teste Spintr SSO](#create-spintr-sso-test-user)** - para ter uma contrapartida de B.Simon em Spintr SSO que esteja ligada à representação do utilizador da AD Azure.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Spintr SSO,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL: `https://signin.spintr.me`

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na secção **Spintr SSO configurar,** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Spintr SSO.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Spintr SSO**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-spintr-sso"></a>Configure Spintr SSO

Para configurar um único sinal no lado **spintr SSO,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [spintr SSO equipe](mailto:support@spintr.me)de suporte . Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-spintr-sso-test-user"></a>Criar o utilizador de teste Spintr SSO

Nesta secção, um utilizador chamado B.Simon é criado em Spintr SSO. Spintr SSO suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para você nesta seção. Se um utilizador já não existir no Spintr SSO, um novo é criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Spintr SSO no Painel de Acesso, deve ser automaticamente inscrito no Spintr SSO para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Spintr SSO com Azure AD](https://aad.portal.azure.com/)