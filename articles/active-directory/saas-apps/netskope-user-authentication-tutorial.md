---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com autenticação de usuário do Netskope | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a autenticação de usuário Netskope.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9bb71cd-aaf9-4403-aca5-c5a349ec562a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33af932e405552cf9d8f5aaf6d42cbd095607b0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085374"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com a autenticação de usuário do Netskope

Neste tutorial, você aprenderá a integrar a autenticação de usuário Netskope com o Azure Active Directory (Azure AD). Ao integrar a autenticação de usuário do Netskope ao Azure AD, você pode:

* Controle no Azure AD que tem acesso à autenticação de usuário do Netskope.
* Habilite seus usuários a serem conectados automaticamente à autenticação de usuário do Netskope com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do Netskope User Authentication.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* A autenticação de usuário Netskope dá suporte a **SP** iniciado pelo

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Adicionando a autenticação de usuário Netskope da Galeria

Para configurar a integração da autenticação de usuário do Netskope ao Azure AD, você precisa adicionar a autenticação de usuário do Netskope da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite autenticação de **usuário do Netskope** na caixa de pesquisa.
1. Selecione **autenticação de usuário Netskope** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Configurar e testar o logon único do Azure AD para autenticação de usuário do Netskope

Configure e teste o SSO do Azure AD com a autenticação de usuário do Netskope usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na autenticação de usuário do Netskope.

Para configurar e testar o SSO do Azure AD com a autenticação de usuário do Netskope, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar SSO de autenticação de usuário Netskope](#configure-netskope-user-authentication-sso)** -para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste de autenticação de usuário do Netskope](#create-netskope-user-authentication-test-user)** – para ter um equivalente de B. Simon na autenticação de usuário do Netskope que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Netskope User Authentication** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<tenantname>.goskope.com/<customer entered string>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Você receberá esses valores explicados posteriormente no tutorial.

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Os valores da URL de logon não são reais. Atualize o valor da URL de logon com a URL de logon real. Contate a [equipe de suporte ao cliente do Netskope User Authentication](mailto:support@netskope.com) para obter o valor da URL de logon. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar a autenticação de usuário do Netskope** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso à autenticação de usuário do Netskope.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **autenticação de usuário do Netskope**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-netskope-user-authentication-sso"></a>Configurar SSO de autenticação de usuário Netskope

1. Abra uma nova guia no navegador e entre no site da empresa de autenticação de usuário do Netskope como administrador.

1. Clique na guia **plataforma ativa** .

    ![Configuração de autenticação de usuário Netskope](./media/netskope-user-authentication-tutorial/user1.png)

1. Role para baixo até o **proxy de encaminhamento** e selecione **SAML**.

    ![Configuração de autenticação de usuário Netskope](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Na página **configurações do SAML** , execute as seguintes etapas:

    ![Configuração de autenticação de usuário Netskope](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Copie o valor da **ID da entidade SAML** e cole-o na caixa de texto **identificador** na seção **configuração básica do SAML** no portal do Azure.

    b. Copie o valor da **URL do ACS do SAML** e cole-o na caixa de texto URL de **resposta** na seção **configuração básica do SAML** no portal do Azure.

1. Clique em **adicionar conta**.

    ![Configuração de autenticação de usuário Netskope](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. Na página **adicionar conta SAML** , execute as seguintes etapas:

    ![Configuração de autenticação de usuário Netskope](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. Na caixa de texto **nome** , forneça o nome como o Azure AD.

    b. Na caixa de texto **URL do IDP** , Cole o valor da **URL de logon** copiado do portal do Azure.

    c. Na caixa de texto **ID da entidade do IDP** , Cole o valor do identificador do **Azure ad** copiado do portal do Azure.

    d. Abra o arquivo de metadados baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado IDP** .

    e. Clique em **GUARDAR**.

### <a name="create-netskope-user-authentication-test-user"></a>Criar usuário de teste de autenticação de usuário Netskope

1. Abra uma nova guia no navegador e entre no site da empresa de autenticação de usuário do Netskope como administrador.

1. Clique na guia **configurações** no painel de navegação à esquerda.

    ![Criação de usuário de autenticação de usuário Netskope](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Clique na guia **plataforma ativa** .

    ![Criação de usuário de autenticação de usuário Netskope](./media/netskope-user-authentication-tutorial/user1.png)

1. Clique na guia **usuários** .

    ![Criação de usuário de autenticação de usuário Netskope](./media/netskope-user-authentication-tutorial/add-user.png)

1. Clique em **Adicionar usuários**.

    ![Criação de usuário de autenticação de usuário Netskope](./media/netskope-user-authentication-tutorial/user-add.png)

1. Insira o endereço de email do usuário que você deseja adicionar e clique em **Adicionar**.

    ![Criação de usuário de autenticação de usuário Netskope](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco de autenticação de usuário do Netskope no painel de acesso, você deverá ser conectado automaticamente à autenticação de usuário do Netskope para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a autenticação de usuário do Netskope com o Azure AD](https://aad.portal.azure.com/)