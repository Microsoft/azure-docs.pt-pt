---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Qmarkets Idea & gerenciamento de inovação | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Qmarkets Idea & gerenciamento de inovação.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5ca125c7-f778-4a2d-a573-7cebe1ff634d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d3e3f86d761a686993e6ecf32718aa2e15dac92
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536289"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-qmarkets-idea--innovation-management"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Qmarkets Idea & gerenciamento de inovação

Neste tutorial, você aprenderá a integrar o Qmarkets Idea & o gerenciamento de inovação com o Azure Active Directory (AD do Azure). Ao integrar o Qmarkets Idea & o gerenciamento de inovação com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Qmarkets Idea & o gerenciamento de inovação.
* Habilite seus usuários a entrar automaticamente no Qmarkets Idea & o gerenciamento de inovação com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Qmarkets Idea & assinatura habilitada para SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.



* Qmarkets Idea & o gerenciamento de inovação dá suporte ao **SP e** ao SSO iniciado pelo IDP
* Qmarkets Idea & o gerenciamento de inovação dá suporte ao provisionamento **de usuário just-in-time**


## <a name="adding-qmarkets-idea--innovation-management-from-the-gallery"></a>Adicionando Qmarkets Idea & inovação da Galeria

Para configurar a integração do Qmarkets Idea & o gerenciamento de inovação ao Azure AD, você precisará adicionar o Qmarkets Idea & gerenciamento de inovação da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Qmarkets Idea & gerenciamento de inovação** na caixa de pesquisa.
1. Selecione **Qmarkets ideia & gerenciamento de inovação** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-qmarkets-idea--innovation-management"></a>Configurar e testar o logon único do Azure AD para Qmarkets Idea & gerenciamento de inovação

Configure e teste o SSO do Azure AD com o Qmarkets Idea & o gerenciamento de inovação usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Qmarkets Idea & o gerenciamento de inovação.

Para configurar e testar o SSO do Azure AD com o Qmarkets Idea & o gerenciamento de inovação, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o Qmarkets Idea & o SSO de gerenciamento de inovação](#configure-qmarkets-idea--innovation-management-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar Qmarkets Idea & usuário de teste de gerenciamento de inovação](#create-qmarkets-idea--innovation-management-test-user)** – para ter um equivalente de B. Simon em Qmarkets Idea & gerenciamento de inovação vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Qmarkets Idea & inovação** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<app_url>/sso/saml2/metadata/qmarkets_sp_<endpoint_id>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<app_url>/sso/saml2/acs/qmarkets_sp_<endpoint_id>`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<app_url>/sso/saml2/endpoint/qmarkets_sp_<endpoint_id>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte do cliente Qmarkets Idea & inovação](mailto:support@qmarkets.net) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Qmarkets Idea & o gerenciamento de inovação.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Qmarkets Idea & gerenciamento de inovação**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-qmarkets-idea--innovation-management-sso"></a>Configurar o SSO de gerenciamento de Qmarkets Idea & inovação

Para configurar o logon único no lado do **Qmarkets Idea & inovação** , você precisa enviar a URL de **metadados de Federação do aplicativo** para [Qmarkets Idea & equipe de suporte de gerenciamento de inovação](mailto:support@qmarkets.net). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-qmarkets-idea--innovation-management-test-user"></a>Criar Qmarkets Idea & usuário de teste de gerenciamento de inovação

Nesta seção, um usuário chamado Brenda Simon é criado no Qmarkets Idea & o gerenciamento de inovação. Qmarkets Idea & o gerenciamento de inovação dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Qmarkets Idea & o gerenciamento de inovação, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Qmarkets Idea & de gerenciamento de inovação no painel de acesso, você deverá ser conectado automaticamente à ideia Qmarkets & gerenciamento de inovação para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Qmarkets Idea & o gerenciamento de inovação com o Azure AD](https://aad.portal.azure.com/)

