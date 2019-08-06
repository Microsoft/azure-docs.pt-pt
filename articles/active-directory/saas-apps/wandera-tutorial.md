---
title: 'Tutorial: Integração de Azure Active Directory com o | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a4615e56-1deb-423e-ad19-2e74c0d6d17a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f5004571c849d90b7d811906684e66c10ee487
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825309"
---
# <a name="tutorial-integrate-wandera-with-azure-active-directory"></a>Tutorial: Integre o com Azure Active Directory

Neste tutorial, você aprenderá a integrar o ao Azure Active Directory (Azure AD). Ao integrar o ao Azure AD, você pode:

* Controle no Azure ad quem tem acesso ao.
* Habilite seus usuários a entrarem automaticamente no acentrar com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-wandera-from-the-gallery"></a>Adicionando o da Galeria

Para configurar a integração do ao Azure AD, você precisa adicionar o da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite na caixa de pesquisa.
1. Selecione a no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o onexpress usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no onit.

Para configurar e testar o SSO do Azure AD com o, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar](#configure-wandera-sso)** a conexão de entrada para SSO-para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste](#create-wandera-test-user)** do onit-para ter um equivalente de B. Simon no que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do Insider, localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de resposta real. Contate a [equipe de suporte ao cliente](https://www.wandera.com/about-wandera/contact/#supportsection) do backit para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para o **certificado de autenticação SAML** para editar as configurações.

    ![Opção de Assinatura](common/signing-option.png)

    1. Selecione a **opção de assinatura** como **assinar resposta SAML e asserção**.

    1. Selecione o **algoritmo de assinatura** como **SHA-256**.

1. Na seção **Configurar** o, copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-wandera-sso"></a>Configurar o SSO de

1. Para automatizar a configuração no backit, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar** o onit irá direcioná-lo para o aplicativo do. A partir daí, forneça as credenciais de administrador para entrar no. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-4.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o backr manualmente, abra uma nova janela do navegador da Web e entre no site da sua empresa do acesse-o como administrador e execute as seguintes etapas:

4. No canto superior direito da página, clique em **configurações** > **Administração** > **logon único** e marque a opção **habilitar SAML 2,0** para executar as etapas a seguir.

    ![Configuração de](./media/wandera-tutorial/config01.png)

    a. Clique em **ou insira manualmente os campos obrigatórios**.

    b. Na caixa de texto EntityId do **IDP** , Cole o valor do **identificador do Azure ad** copiado do portal do Azure.

    c. Abra o XML de metadados de Federação no bloco de notas, copie seu conteúdo e cole-o na caixa de texto **certificado X. 509 público do IDP** .

    d. Clique em **Guardar**.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecioneo.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-wandera-test-user"></a>Criar usuário de teste do onit

Nesta seção, você criará um usuário chamado B. Simon na sessão de entrada. Trabalhe com a [equipe de suporte](https://www.wandera.com/about-wandera/contact/#supportsection) do onit para adicionar os usuários na plataforma do. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do backit no painel de acesso, você deverá ser conectado automaticamente à sessão de entrada para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

