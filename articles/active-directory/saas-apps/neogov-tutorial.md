---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o NEOGOV | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NEOGOV.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6e4481af-54f1-4689-80d0-bd02a749ef53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b0954cac84a791367c5f9af18d3e760a27db11
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081700"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-neogov"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o NEOGOV

Neste tutorial, você aprenderá a integrar o NEOGOV com o Azure Active Directory (Azure AD). Ao integrar o NEOGOV ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao NEOGOV.
* Habilite seus usuários a serem conectados automaticamente ao NEOGOV com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do NEOGOV.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* NEOGOV dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-neogov-from-the-gallery"></a>Adicionando o NEOGOV da Galeria

Para configurar a integração do NEOGOV ao Azure AD, você precisará adicionar o NEOGOV da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **NEOGOV** na caixa de pesquisa.
1. Selecione **NEOGOV** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-neogov"></a>Configurar e testar o logon único do Azure AD para o NEOGOV

Configure e teste o SSO do Azure AD com o NEOGOV usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no NEOGOV.

Para configurar e testar o SSO do Azure AD com o NEOGOV, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do NEOGOV](#configure-neogov-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do NEOGOV](#create-neogov-test-user)** – para ter um equivalente de B. Simon em NEOGOV que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **NEOGOV** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:

    | Ambiente | Padrão de URL |
    | -- | -- |
    | Produção | `https://www.neogov.com/` |
    | AppDomain | `https://www.uat.neogov.net/` |
    | | |

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:

    | Ambiente | Padrão de URL |
    | -- | -- |
    | Produção | `https://login.neogov.com/authentication/saml/consumer` |
    | AppDomain | `https://login.uat.neogov.net/authentication/saml/consumer` |
    | | |

1. O aplicativo NEOGOV espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo NEOGOV espera que **nameidentifier** seja mapeado com **User. ObjectId**, portanto, você precisa editar o mapeamento de atributo clicando no ícone de **edição** e alterando o mapeamento de atributo.

    ![image](common/edit-attribute.png)

1. Além de acima, o aplicativo NEOGOV espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de origem|
    | -------|--------- |
    | mail | user.mail |

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao NEOGOV.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **NEOGOV**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-neogov-sso"></a>Configurar o SSO do NEOGOV

Para configurar o logon único no lado do **NEOGOV** , é necessário enviar a **URL de metadados de Federação do aplicativo** para a equipe de suporte do [NEOGOV](mailto:itops@neogov.net). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-neogov-test-user"></a>Criar usuário de teste do NEOGOV

Nesta seção, você criará um usuário chamado B. Simon em NEOGOV. Trabalhe com a [equipe de suporte do NEOGOV](mailto:itops@neogov.net) para adicionar os usuários na plataforma do NEOGOV. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do NEOGOV no painel de acesso, você deverá ser conectado automaticamente ao NEOGOV para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o NEOGOV com o Azure AD](https://aad.portal.azure.com/)