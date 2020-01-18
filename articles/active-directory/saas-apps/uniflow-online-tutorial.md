---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o uniFLOW online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o uniFLOW online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f26af813fcd4032aabce2305ac8845307d1fca65
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262134"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o uniFLOW online

Neste tutorial, você aprenderá a integrar o uniFLOW online ao Azure Active Directory (Azure AD). Ao integrar o uniFLOW online ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao uniFLOW online.
* Habilite seus usuários para entrar no uniFLOW online com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* locatário do uniFLOW online.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* o uniFLOW online dá suporte ao SSO iniciado por **SP**

## <a name="adding-uniflow-online-from-the-gallery"></a>Adicionando o uniFLOW online da Galeria

Para configurar a integração do uniFLOW online ao Azure AD, você precisará adicionar o uniFLOW online da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **uniFLOW online** na caixa de pesquisa.
1. Selecione **UniFLOW online** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Configurar e testar o logon único do Azure AD para o uniFLOW online

Configure e teste o SSO do Azure AD com o uniFLOW online usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no uniFLOW online.

Para configurar e testar o SSO do Azure AD com o uniFLOW online, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
   1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
   1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO online do uniFLOW](#configure-uniflow-online-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Entre no UniFLOW online usando o usuário de teste criado](#sign-in-to-uniflow-online-using-the-created-test-user)** – para testar a entrada do usuário no lado do aplicativo.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **uniFLOW online** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do UniFLOW online](mailto:support@nt-ware.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure ou consulte a URL de resposta exibida no locatário online do uniFLOW.

1. o aplicativo uniFLOW online espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo uniFLOW online espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de origem|
    | -----------| --------------- |
    | displayName | user.displayname |
    | apelido | User. onpremisessamaccountname |

   > [!NOTE]
   > O atributo `user.onpremisessamaccountname` conterá um valor somente se os usuários do Azure AD forem sincronizados de um Active Directory do Windows local.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao uniFLOW online.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **UniFLOW online**.
1. Na página Visão geral do aplicativo, vá para a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

   ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

> [!NOTE]
> Para permitir que todos os usuários acessem o aplicativo sem atribuição manual, vá para a seção **gerenciar** e selecione **Propriedades**. Em seguida, altere o parâmetro **necessário de atribuição de usuário** para **não**.

## <a name="configure-uniflow-online-sso"></a>Configurar SSO do uniFLOW online

1. Em uma janela diferente do navegador da Web, entre no site do uniFLOW online como administrador.

1. No painel de navegação à esquerda, selecione a guia **usuário** .

    ![Configuração online do uniFLOW](./media/uniflow-online-tutorial/configure1.png)

1. Clique em **provedores de identidade**.

    ![Configuração online do uniFLOW](./media/uniflow-online-tutorial/configure2.png)

1. Clique em **Adicionar provedor de identidade**.

    ![Configuração online do uniFLOW](./media/uniflow-online-tutorial/configure3.png)

1. Na seção **Adicionar provedor de identidade** , execute as seguintes etapas:


    ![Configuração online do uniFLOW](./media/uniflow-online-tutorial/configure4.png)

    a. Insira o nome de exibição ex: *AZUREAD SSO*.

    b. Para **tipo de provedor**, selecione opção **WS-redirecionada** no menu suspenso.

    c. Para o **tipo WS-Redirecionado**, selecione a opção **Azure Active Directory** na lista suspensa.

    d. Clique em **Guardar**.

1. Na guia **geral** , execute as seguintes etapas:

    ![Configuração online do uniFLOW](./media/uniflow-online-tutorial/configure5.png)

    a. Insira o nome de exibição ex: *AZUREAD SSO*.

    b. Selecione a opção **da URL** para os **metadados de Federação do ADFS**.

    c. Na caixa de texto **URL de metadados de Federação** , Cole o valor da URL de metadados de Federação do **aplicativo** copiado do portal do Azure.

    d. Selecione **provedor de identidade** como **habilitado**.

    e. Selecione **registro de usuário automático** como **ativado**.

    f. Clique em **Guardar**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Entre no uniFLOW online usando o usuário de teste criado

1. Em uma janela diferente do navegador da Web, vá para a URL do uniFLOW online para seu locatário.

1. Selecione o provedor de identidade criado anteriormente para entrar por meio de sua instância do Azure AD.

1. Entre usando o usuário de teste.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o uniFLOW online com o Azure AD](https://aad.portal.azure.com/)
