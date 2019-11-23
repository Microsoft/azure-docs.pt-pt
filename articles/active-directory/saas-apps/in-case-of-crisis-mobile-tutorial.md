---
title: 'Tutorial: integração do Azure Active Directory SSO (logon único) com no caso de crise-celular | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e em caso de crise-móvel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6afa98e9-a123-4cbb-add6-afcefc1242e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bff484e4ca3a1579067501be134d71da72abfa
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72266088"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-in-case-of-crisis---mobile"></a>Tutorial: integração do Azure Active Directory SSO (logon único) com no caso de crise-móvel

Neste tutorial, você aprenderá a integrar o em caso de crise-móvel com o Azure Active Directory (Azure AD). Ao integrar no caso de crise-móvel com o Azure AD, você pode:

* Controle no Azure AD que tem acesso ao em caso de crise-móvel.
* Habilite seus usuários a entrarem automaticamente no caso de crise-celular com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* No caso de uma assinatura habilitada para SSO (logon único) móvel de crise.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* No caso de crise-o Mobile dá suporte ao SSO iniciado pelo **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-in-case-of-crisis---mobile-from-the-gallery"></a>Adição em caso de crise-móvel da Galeria

Para configurar a integração do no caso de crise-celular ao Azure AD, você precisa adicionar no caso de crise-Mobile da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **no caso de crise-móvel** na caixa de pesquisa.
1. Selecione **no caso de crise – móvel** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-in-case-of-crisis---mobile"></a>Configurar e testar o logon único do Azure AD para no caso de crise-móvel

Configure e teste o SSO do Azure AD com o no caso de crise-móvel usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no caso de crise-móvel.

Para configurar e testar o SSO do Azure AD com o no caso de crise-móvel, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar no caso de crise-SSO móvel](#configure-in-case-of-crisis---mobile-sso)** -para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar no caso de um usuário de teste do crise-Mobile](#create-in-case-of-crisis---mobile-test-user)** – para ter um equivalente de B. Simon no caso de crise-móvel que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos de **crise-móvel** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** o aplicativo é pré-configurado no modo de iniciado pelo **IDP** e as URLs necessárias já estão preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize **certificado (bruto)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificateraw.png)

1. Navegue até a seção **gerenciar** no lado esquerdo da página, clique na **guia Propriedades** e, em seguida, copie a **URL de acesso do usuário** e salve-a no computador.

    ![Propriedades de logon único](./media/in-case-of-crisis-mobile-tutorial/properties.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao no caso de crise-móvel.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **no caso de crise-móvel**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-in-case-of-crisis---mobile-sso"></a>Configurar no caso de crise-SSO móvel

Para configurar o logon único no **no caso do lado do crise-móvel** , você precisa enviar o **certificado (bruto)** baixado e a **URL de acesso do usuário** copiada do portal do Azure para o [no caso da equipe de suporte de crises-móveis](https://www.rockdovesolutions.com/features/enterprise-ready). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-in-case-of-crisis---mobile-test-user"></a>Criar no caso de crise – usuário de teste móvel

Nesta seção, você criará um usuário chamado Brenda Simon no caso de crise-móvel. Trabalhe com o [em caso de crise – equipe de suporte móvel](https://www.rockdovesolutions.com/features/enterprise-ready) para adicionar os usuários no caso de uma plataforma móvel de crise. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no no caso do bloco de crises móveis no painel de acesso, você deverá ser conectado automaticamente ao no caso de crise-móvel para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente no caso de crise-móvel com o Azure AD](https://aad.portal.azure.com/)

