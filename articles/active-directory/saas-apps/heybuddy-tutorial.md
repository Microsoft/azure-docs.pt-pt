---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o HeyBuddy | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HeyBuddy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d51b5af6-018e-4678-9a3f-b70438394f67
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47fde1a72d651dd79de9d5d32fb4874c62ae1e39
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638651"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-heybuddy"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o HeyBuddy

Neste tutorial, você aprenderá a integrar o HeyBuddy com o Azure Active Directory (Azure AD). Ao integrar o HeyBuddy ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao HeyBuddy.
* Habilite seus usuários a serem conectados automaticamente ao HeyBuddy com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do HeyBuddy.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O HeyBuddy dá suporte ao SSO iniciado por **SP**


* O HeyBuddy dá suporte ao provisionamento **de usuário just in time**


## <a name="adding-heybuddy-from-the-gallery"></a>Adicionando o HeyBuddy da Galeria

Para configurar a integração do HeyBuddy ao Azure AD, você precisará adicionar o HeyBuddy da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **HeyBuddy** na caixa de pesquisa.
1. Selecione **HeyBuddy** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-heybuddy"></a>Configurar e testar o logon único do Azure AD para o HeyBuddy

Configure e teste o SSO do Azure AD com o HeyBuddy usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no HeyBuddy.

Para configurar e testar o SSO do Azure AD com o HeyBuddy, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do HeyBuddy](#configure-heybuddy-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do HeyBuddy](#create-heybuddy-test-user)** – para ter um equivalente de B. Simon em HeyBuddy que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **HeyBuddy** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://api.heybuddy.com/auth/<ENTITY ID>`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `YourCompanyInstanceofHeyBuddy`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais (ID da entidade). O `Entity ID` na URL de logon é gerado automaticamente para cada organização. Contate a [equipe de suporte ao cliente do HeyBuddy](mailto:support@heybuddy.com) para obter esses valores.

1. O aplicativo HeyBuddy espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/edit-attribute.png)

1. Além de acima, o aplicativo EZOfficeInventory espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.

    | Nome |  Atributo de origem|
    | -------- | --------- |
    | Funções  | user.assignedroles |
    | | |
    
    > [!NOTE]
    > Consulte este [link](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) sobre como configurar e configurar as funções para o aplicativo.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao HeyBuddy.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **HeyBuddy**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-heybuddy-sso"></a>Configurar o SSO do HeyBuddy

Para configurar o logon único no lado do **HeyBuddy** , é necessário enviar a **URL de metadados de Federação do aplicativo** para a equipe de suporte do [HeyBuddy](mailto:support@heybuddy.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-heybuddy-test-user"></a>Criar usuário de teste do HeyBuddy

Nesta seção, um usuário chamado Brenda Simon é criado em HeyBuddy. O HeyBuddy dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no HeyBuddy, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do HeyBuddy](mailto:support@heybuddy.com).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do HeyBuddy no painel de acesso, você deverá ser conectado automaticamente ao HeyBuddy para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o HeyBuddy com o Azure AD](https://aad.portal.azure.com/)

