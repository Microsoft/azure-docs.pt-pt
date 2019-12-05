---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Claromentis | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Claromentis.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7980e0c5-e4d8-4678-afa2-7ec219432114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6eaf23950f83592a33709574be464f4499f0ab
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823222"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Claromentis

Neste tutorial, você aprenderá a integrar o Claromentis com o Azure Active Directory (Azure AD). Ao integrar o Claromentis ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Claromentis.
* Habilite seus usuários a serem conectados automaticamente ao Claromentis com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Claromentis.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Claromentis dá suporte ao **SP e** ao SSO iniciado pelo IDP
* O Claromentis dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-claromentis-from-the-gallery"></a>Adicionando o Claromentis da Galeria

Para configurar a integração do Claromentis ao Azure AD, você precisará adicionar o Claromentis da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Claromentis** na caixa de pesquisa.
1. Selecione **Claromentis** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Configurar e testar o logon único do Azure AD para o Claromentis

Configure e teste o SSO do Azure AD com o Claromentis usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Claromentis.

Para configurar e testar o SSO do Azure AD com o Claromentis, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Claromentis](#configure-claromentis-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Claromentis](#create-claromentis-test-user)** – para ter um equivalente de B. Simon em Claromentis que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Claromentis** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , insira o valor do identificador de acordo com o requisito da sua organização.

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:

    | | |
    |-|-|
    | `https://<customer_site_url>/login`|
    | `https://<customer_site_url>/login?no_auto=0`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais que são explicados posteriormente no turorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar Claromentis** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Claromentis.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Claromentis**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-claromentis-sso"></a>Configurar o SSO do Claromentis

1. Em uma janela de navegador diferente, entre no site do Claromentis como um administrador.

1. Clique no **ícone aplicativos** e selecione **administrador**.

    ![Configuração do Claromentis](./media/claromentis-tutorial/config1.png)

1. Selecione a guia **manipulador de logon personalizado** .

    ![Configuração do Claromentis](./media/claromentis-tutorial/config2.png)

1. Selecione **configuração do SAML**.

    ![Configuração do Claromentis](./media/claromentis-tutorial/config3.png)

1. Na guia **configuração do SAML** , role para baixo até a seção **configuração** e execute as seguintes etapas:

    ![Configuração do Claromentis](./media/claromentis-tutorial/config4.png)

    a. Na caixa de texto **nome do contato técnico** , insira o nome da pessoa de contato técnico.

    b. Na caixa de texto **email de contato técnico** , insira o endereço de email da pessoa de contato técnico.

    c. Forneça a senha na caixa de texto **senha do administrador de autenticação** .

1. Role para baixo até **fontes de autenticação** e execute as seguintes etapas:

    ![Configuração do Claromentis](./media/claromentis-tutorial/config5.png)

    a. Na caixa de texto **IDP** , insira o valor do **identificador do Azure ad** que você copiou do portal do Azure.

    b. Na caixa de texto **ID da entidade** , insira o valor da ID da entidade.

    c. Carregue o arquivo **XML de metadados de Federação** , que você baixou do portal do Azure.

    d. Clique em **Guardar**.

1. Agora, você observará que todas as URLs foram preenchidas na seção **provedor de identidade** na seção de **configuração SAML** .

    ![Configuração do Claromentis](./media/claromentis-tutorial/config6.png)

    a. Copie o valor do **identificador (ID da entidade)** , Cole esse valor na caixa de texto **identificador** na seção **configuração básica do SAML** em portal do Azure.

    b. Copie o valor da **URL de resposta** , Cole esse valor na caixa de texto **URL de resposta** na seção **configuração básica do SAML** no portal do Azure.

    c. Copie o valor da **URL de logon** , Cole esse valor na caixa de texto **URL de logon** na seção **configuração básica do SAML** no portal do Azure.

### <a name="create-claromentis-test-user"></a>Criar usuário de teste do Claromentis

Nesta seção, um usuário chamado B. Simon é criado em Claromentis. O Claromentis dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Claromentis, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Claromentis no painel de acesso, você deverá ser conectado automaticamente ao Claromentis para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Claromentis com o Azure AD](https://aad.portal.azure.com/)