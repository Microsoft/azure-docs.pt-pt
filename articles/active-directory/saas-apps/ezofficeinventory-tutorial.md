---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o EZOfficeInventory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EZOfficeInventory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e8594f7c-dc2f-446f-9c25-676fe49ff3af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ebfe5f75d5d8546e0f5e8ad6f8c5d0063e5bda2
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376449"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o EZOfficeInventory

Neste tutorial, você aprenderá a integrar o EZOfficeInventory com o Azure Active Directory (Azure AD). Ao integrar o EZOfficeInventory ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao EZOfficeInventory.
* Habilite seus usuários a serem conectados automaticamente ao EZOfficeInventory com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do EZOfficeInventory.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O EZOfficeInventory dá suporte ao SSO iniciado por **SP**

* O EZOfficeInventory dá suporte ao provisionamento **de usuário just in time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-ezofficeinventory-from-the-gallery"></a>Adicionando o EZOfficeInventory da Galeria

Para configurar a integração do EZOfficeInventory ao Azure AD, você precisará adicionar o EZOfficeInventory da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **EZOfficeInventory** na caixa de pesquisa.
1. Selecione **EZOfficeInventory** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>Configurar e testar o logon único do Azure AD para o EZOfficeInventory

Configure e teste o SSO do Azure AD com o EZOfficeInventory usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no EZOfficeInventory.

Para configurar e testar o SSO do Azure AD com o EZOfficeInventory, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do EZOfficeInventory](#configure-ezofficeinventory-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do EZOfficeInventory](#create-ezofficeinventory-test-user)** – para ter um equivalente de B. Simon em EZOfficeInventory que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **EZOfficeInventory** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do EZOfficeInventory](mailto:support@ezofficeinventory.com) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo EZOfficeInventory espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/edit-attribute.png)

1. Além de acima, o aplicativo EZOfficeInventory espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | First_name | User. excertoname |
    | Last_name | User. sobrenome |
    | E-mail | User. mail |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar EZOfficeInventory** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao EZOfficeInventory.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **EZOfficeInventory**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-ezofficeinventory-sso"></a>Configurar o SSO do EZOfficeInventory

1. Abra uma nova janela do navegador da Web e entre no site da empresa do EZOfficeInventory como um administrador.

2. No canto superior direito da página, clique em **perfil** and e navegue até **configurações**@no__t **-3 Complementos**.

    ![Configuração do EZOfficeInventory](./media/ezofficeinventory-tutorial/configure01.png)

3. Role para baixo até a seção **integração SAML** , execute as seguintes etapas:

    ![Configuração do EZOfficeInventory](./media/ezofficeinventory-tutorial/configure02.png)

    a. Marque a opção **habilitado** .

    b. Na caixa de texto **URL do provedor de identidade** , Cole o valor da URL de **logon** copiado do portal do Azure.

    c. Abra o certificado codificado em base64 no bloco de notas, copie seu conteúdo e cole-o na caixa de texto **certificado do provedor de identidade** .

    d. Na caixa de texto **texto do botão de logon** , digite o texto do botão de logon.

    e. Na caixa de texto **nome** , digite **first_name**.

    f. Na caixa de texto **sobrenome** , insira **last_name**.

    g. Na caixa de texto **email** , insira o **email**.

    h. Selecione sua função de acordo com seu requisito da **função EZOfficeInventory por padrão** .

    i. Clique em **Atualizar**.

### <a name="create-ezofficeinventory-test-user"></a>Criar usuário de teste do EZOfficeInventory

Nesta seção, um usuário chamado Brenda Simon é criado em EZOfficeInventory. O EZOfficeInventory dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no EZOfficeInventory, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do EZOfficeInventory no painel de acesso, você deverá ser conectado automaticamente ao EZOfficeInventory para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o EZOfficeInventory com o Azure AD](https://aad.portal.azure.com/)

