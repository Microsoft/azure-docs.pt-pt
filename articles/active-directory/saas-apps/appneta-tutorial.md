---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o monitor de desempenho do Appneta performance | Microsoft Docs'
description: Saiba como configurar o logon único entre o monitor de desempenho do Azure Active Directory e do Appneta performance.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac9e1d32e0280bcf053578aa102cc6fd200a4b2
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561239"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o monitor de desempenho do Appneta performance

Neste tutorial, você aprenderá a integrar o monitor de desempenho Appneta performance com o Azure Active Directory (Azure AD). Ao integrar o monitor de desempenho Appneta performance com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao monitor de desempenho do Appneta performance.
* Habilite seus usuários a serem conectados automaticamente ao monitor de desempenho do Appneta performance com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do monitor de desempenho do Appneta performance.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O monitor de desempenho Appneta performance dá suporte ao SSO iniciado por **SP**


* O monitor de desempenho Appneta performance dá suporte ao provisionamento **de usuário just in time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Adicionando o Appneta performance Performance Monitor da Galeria

Para configurar a integração do monitor de desempenho do Appneta performance ao Azure AD, você precisará adicionar o Appneta performance Performance Monitor da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **appneta performance Performance Monitor** na caixa de pesquisa.
1. Selecione **Monitor de desempenho do appneta performance** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-appneta-performance-monitor"></a>Configurar e testar o logon único do Azure AD para o monitor de desempenho Appneta performance

Configure e teste o SSO do Azure AD com o monitor de desempenho Appneta performance usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no monitor de desempenho do Appneta performance.

Para configurar e testar o SSO do Azure AD com o Appneta performance Performance Monitor, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do monitor de desempenho do appneta performance](#configure-appneta-performance-monitor-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do monitor de desempenho do appneta performance](#create-appneta-performance-monitor-test-user)** – para ter um equivalente de B. Simon no monitor de desempenho appneta performance que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **appneta performance Performance Monitor** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<subdomain>.pm.appneta.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite um valor: `PingConnect`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize esse valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Appneta performance Performance Monitor](mailto:support@appneta.com) para obter esse valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo de monitor de desempenho Appneta performance espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/edit-attribute.png)

1. Além de acima, o aplicativo de monitor de desempenho Appneta performance espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de origem|
    | --------| ----------------|
    | firstName| user.givenname|
    | lastName| User. sobrenome|
    | e-mail| user.userprincipalname|
    | nome| user.userprincipalname|
    | grupos  | user.assignedroles |
    | telemóvel| User. telephoneNumber |
    | title| user.jobtitle|
    | | |

    > [!NOTE]
    > **grupos** refere-se ao grupo de segurança no appneta performance que é mapeado para uma **função** no Azure AD. Consulte [este](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) documento, que explica como criar funções personalizadas no Azure AD.

    1. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    1. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    1. Deixe o **namespace** em branco.

    1. Selecione origem como **atributo**.

    1. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    1. Clique em **Ok**

    1. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o monitor de desempenho do appneta performance** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao monitor de desempenho Appneta performance.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Appneta performance Performance Monitor**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-appneta-performance-monitor-sso"></a>Configurar o SSO do monitor de desempenho Appneta performance

Para configurar o logon único no lado do **Monitor de desempenho do appneta performance** , é necessário enviar o **XML de metadados de Federação** baixado e as URLs copiadas apropriadas de portal do Azure para a equipe de suporte do monitor de desempenho do [appneta performance](mailto:support@appneta.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-appneta-performance-monitor-test-user"></a>Criar usuário de teste do monitor de desempenho do Appneta performance

Nesta seção, um usuário chamado Brenda Simon é criado no monitor de desempenho Appneta performance. O monitor de desempenho Appneta performance dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no monitor de desempenho Appneta performance, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do monitor de desempenho do appneta performance](mailto:support@appneta.com).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Appneta performance Performance Monitor no painel de acesso, você deverá ser automaticamente conectado ao monitor de desempenho do Appneta performance para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o monitor de desempenho Appneta performance com o Azure AD](https://aad.portal.azure.com/)

