---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com a plataforma de gerenciamento de propriedades Yuhu | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e a plataforma de gerenciamento de propriedades Yuhu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5f7846fe-49f8-48b4-abda-a3719cb18c15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0f15dfa36c24c7a84e254b110e9cceae54c8786
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533245"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-yuhu-property-management-platform"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com a plataforma de gerenciamento de propriedades Yuhu

Neste tutorial, você aprenderá a integrar a plataforma de gerenciamento de propriedade Yuhu com o Azure Active Directory (Azure AD). Ao integrar a plataforma de gerenciamento de propriedades do Yuhu ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso à plataforma de gerenciamento de propriedades Yuhu.
* Habilite seus usuários a serem conectados automaticamente à plataforma de gerenciamento de propriedades do Yuhu com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) da plataforma de gerenciamento de propriedades Yuhu.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* A plataforma de gerenciamento de propriedade Yuhu dá suporte ao SSO iniciado por **SP**

## <a name="adding-yuhu-property-management-platform-from-the-gallery"></a>Adicionando a plataforma de gerenciamento de propriedades Yuhu da Galeria

Para configurar a integração da plataforma de gerenciamento de propriedades do Yuhu ao Azure AD, você precisará adicionar a plataforma de gerenciamento de propriedades do Yuhu da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite plataforma de **Gerenciamento de propriedade Yuhu** na caixa de pesquisa.
1. Selecione a **plataforma de gerenciamento de propriedade Yuhu** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-yuhu-property-management-platform"></a>Configurar e testar o logon único do Azure AD para a plataforma de gerenciamento de propriedades Yuhu

Configure e teste o SSO do Azure AD com a plataforma de gerenciamento de propriedades Yuhu usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na plataforma de gerenciamento de propriedades Yuhu.

Para configurar e testar o SSO do Azure AD com a plataforma de gerenciamento de propriedades Yuhu, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO da plataforma de gerenciamento de propriedades do Yuhu](#configure-yuhu-property-management-platform-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste da Yuhu Property Management Platform](#create-yuhu-property-management-platform-test-user)** – para ter um equivalente de B. Simon na plataforma de gerenciamento de propriedades Yuhu que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Yuhu Property Management Platform** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.yuhu.io/companies`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `yuhu-<ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente da plataforma de gerenciamento de propriedades Yuhu](mailto:hello@yuhu.io) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo Yuhu da plataforma de gerenciamento de propriedades espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo da plataforma de gerenciamento de propriedades Yuhu espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | firstName | user.givenname |
    | lastName | User. sobrenome ||
    | e-mail | user.mail |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize **certificado (bruto)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificateraw.png)

1. Na seção **Configurar plataforma de gerenciamento de propriedade Yuhu** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso à plataforma de gerenciamento de propriedades Yuhu.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Yuhu Propriedade plataforma de gerenciamento**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-yuhu-property-management-platform-sso"></a>Configurar SSO da plataforma de gerenciamento de propriedades Yuhu

Para configurar o logon único no lado da **plataforma de gerenciamento de propriedades Yuhu** , é necessário enviar o **certificado (bruto)** baixado e as URLs copiadas apropriadas de portal do Azure para a [equipe de suporte da plataforma de gerenciamento de propriedades do Yuhu](mailto:hello@yuhu.io). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-yuhu-property-management-platform-test-user"></a>Criar usuário de teste da plataforma de gerenciamento de propriedades Yuhu

Nesta seção, você criará um usuário chamado B. Simon na plataforma de gerenciamento de propriedades Yuhu. Trabalhe com a [equipe de suporte do Yuhu Property Management Platform](mailto:hello@yuhu.io) para adicionar os usuários na plataforma de plataforma de gerenciamento de propriedades do Yuhu. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco da plataforma de gerenciamento de propriedade Yuhu no painel de acesso, você deverá ser conectado automaticamente à plataforma de gerenciamento de propriedades do Yuhu para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a plataforma de gerenciamento de propriedade Yuhu com o Azure AD](https://aad.portal.azure.com/)