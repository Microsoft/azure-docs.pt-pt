---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Templafy | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Templafy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 60c6153e-2d9d-4a56-809d-4a7a9d677c43
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52130ac90f9faec19eedf77da425645e35259269
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081603"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-templafy"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Templafy

Neste tutorial, você aprenderá a integrar o Templafy com o Azure Active Directory (Azure AD). Ao integrar o Templafy ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Templafy.
* Habilite seus usuários a serem conectados automaticamente ao Templafy com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Templafy.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Templafy dá suporte ao SSO iniciado por **SP**
* O Templafy dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-templafy-from-the-gallery"></a>Adicionando o Templafy da Galeria

Para configurar a integração do Templafy ao Azure AD, você precisará adicionar o Templafy da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Templafy** na caixa de pesquisa.
1. Selecione **Templafy** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-templafy"></a>Configurar e testar o logon único do Azure AD para o Templafy

Configure e teste o SSO do Azure AD com o Templafy usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Templafy.

Para configurar e testar o SSO do Azure AD com o Templafy, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Templafy](#configure-templafy-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Templafy](#create-templafy-test-user)** – para ter um equivalente de B. Simon em Templafy que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Templafy** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<CLIENTSUBDOMAIN>.templafy.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Templafy](mailto:support@templafy.com) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo Templafy espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além de acima, o aplicativo Templafy espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem| Espaço de Nomes  |
    | ---------------| --------------- | --------- |
    | givenName | user.givenname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | Apelido | User. sobrenome | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | EmailAddress | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`
    | StreetAddress | user.streetaddress | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | city | User. City | `http://schemas.templafy.com/2016/06/identity/claims`|
    | PostalCode | User. PostalCode | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | EstadoOuProvíncia | user.state | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | país | User. Country | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | JobTitle | user.jobtitle | `http://schemas.templafy.com/2016/06/identity/claims`|
    | Departamento | user.department | `http://schemas.templafy.com/2016/06/identity/claims`|
    | PhoneNumber | User. telephoneNumber | `http://schemas.templafy.com/2016/06/identity/claims` |
    | facsimilenumber | user.facsimiletelephonenumber | `http://schemas.templafy.com/2016/06/identity/claims`|
    | sufixo | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | nameidentifier | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Templafy.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Templafy**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-templafy-sso"></a>Configurar o SSO do Templafy

Para configurar o logon único no lado do **Templafy** , é necessário enviar a **URL de metadados de Federação do aplicativo** para a equipe de suporte do [Templafy](mailto:support@templafy.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-templafy-test-user"></a>Criar usuário de teste do Templafy

Nesta seção, um usuário chamado Brenda Simon é criado em Templafy. O Templafy dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Templafy, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Templafy no painel de acesso, você deverá ser conectado automaticamente ao Templafy para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Templafy com o Azure AD](https://aad.portal.azure.com/)