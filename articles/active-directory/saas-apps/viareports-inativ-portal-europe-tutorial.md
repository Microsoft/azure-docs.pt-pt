---
title: 'Tutorial: Integração de Azure Active Directory com o portal de Inativ do Viareport (Europa) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o portal de Inativ do Viareport (Europa).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 249a61c6-a32e-40cc-a46a-268b89652f74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4a7e8359d3c4b80a4dc29a4845749ea690b57fc
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480511"
---
# <a name="tutorial-integrate-viareports-inativ-portal-europe-with-azure-active-directory"></a>Tutorial: Integrar o portal Inativ da Viareport (Europa) com Azure Active Directory

Neste tutorial, você aprenderá a integrar o portal do Inativ da Viareport (Europa) ao Azure Active Directory (Azure AD). Ao integrar o portal do Inativ da Viareport (Europa) ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao portal do Inativ (Europa) da Viareport.
* Habilite seus usuários a entrar automaticamente no portal do Inativ da Viareport (Europa) com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Viareport portal Inativ (Europa).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O portal Inativ da Viareport (Europa) dá suporte ao SP iniciado pelo **IDP**

## <a name="adding-viareports-inativ-portal-europe-from-the-gallery"></a>Adicionando o portal do Inativ da Viareport (Europa) da Galeria

Para configurar a integração do portal do Inativ (Europa) do Viareport ao Azure AD, você precisa adicionar o portal de Inativ do Viareport (Europa) da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **portal do Inativ da Viareport (Europa)** na caixa de pesquisa.
1. Selecione o **portal do Inativ da Viareport (Europa)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o portal do Inativ da Viareport (Europa) usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no portal de Inativ da Viareport (Europa).

Para configurar e testar o SSO do Azure AD com o portal do Inativ (Europa) do Viareport, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do portal do Inativ (Europa) do Viareport](#configure-viareports-inativ-portal-europe-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste da Europa (Inativ Portal) do Viareport](#create-viareports-inativ-portal-europe-test-user)** – para ter um equivalente de B. Simon no portal Inativ da Viareport (Europa) que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **da Europa (Inativ Portal) do Viareport** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://inativ.viareport.com/SSO/<tenant_id>/callback`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://inativ.viareport.com/SSO/<tenant_id>/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do Viareport portal do Inativ (Europa)](mailto:ycezard@viareport.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-viareports-inativ-portal-europe-sso"></a>Configurar o SSO do portal do Inativ da Viareport (Europa)

Para configurar o logon único no lado **do portal do Inativ (Europa) do Viareport** , é necessário enviar a **URL de metadados de Federação do aplicativo** para a [equipe de suporte do portal Inativ da Viareport (Europa)](mailto:ycezard@viareport.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.
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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao portal do Inativ (Europa) da Viareport.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione o **portal do Inativ da Viareport (Europa)** .
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-viareports-inativ-portal-europe-test-user"></a>Criar usuário de teste da Europa (Inativ Portal) do Viareport

Nesta seção, você criará um usuário chamado B. Simon no portal de Inativ da Viareport (Europa). Trabalhe com a [equipe de suporte do portal de Inativ da Viareport (Europa)](mailto:ycezard@viareport.com) para adicionar os usuários na plataforma do portal Inativ da Viareport (Europa). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Inativ (Europa) do Viareport no painel de acesso, você deve entrar automaticamente no portal de Inativ do Viareport (Europa) para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

