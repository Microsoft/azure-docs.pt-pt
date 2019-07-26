---
title: 'Tutorial: Integração do Azure Active Directory com a plataforma cívico | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a plataforma cívico.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d790454-143e-40ac-b3cb-5a256977b4db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ccf124c5a4160715df4e685e405dcd591c49ae7
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496827"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Tutorial: Integre a plataforma cívico com o Azure Active Directory

Neste tutorial, você aprenderá a integrar a plataforma cívico com o Azure Active Directory (Azure AD). Ao integrar a plataforma cívico ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso à plataforma cívico.
* Permita que os usuários sejam automaticamente conectados à plataforma cívico com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) da plataforma cívico.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* A plataforma cívico dá suporte ao SSO iniciado por **SP**





## <a name="adding-civic-platform-from-the-gallery"></a>Adicionando a plataforma cívico da Galeria

Para configurar a integração da plataforma cívico ao Azure AD, você precisa adicionar a plataforma cívico da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **plataforma cívico** na caixa de pesquisa.
1. Selecione **plataforma cívico** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com a plataforma cívico usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na plataforma cívico.

Para configurar e testar o SSO do Azure AD com a plataforma cívico, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO da plataforma cívico](#configure-civic-platform-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar um usuário de teste da plataforma civil](#create-civic-platform-test-user)** – para ter um equivalente de B. Simon na plataforma cívico vinculada à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos da **plataforma cívico** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<SUBDOMAIN>.accela.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL:`civicplatform.accela.com`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize esse valor com a URL de logon real. Contate a [equipe de suporte ao cliente da plataforma civil](mailto:skale@accela.com) para obter esse valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

1. Navegue até **Azure Active Directory** > **registros de aplicativo** no Azure AD, selecione seu aplicativo.

1. Copie a **ID do diretório (locatário)** e armazene-a no bloco de notas.

    ![Copie o diretório (ID do locatário) e armazene-o no código do aplicativo](media/civic-platform-tutorial/directoryid.png)

1. Copie a **ID do aplicativo** e armazene-a no bloco de notas.

   ![Copiar a ID do aplicativo (cliente)](media/civic-platform-tutorial/applicationid.png)

1. Navegue até **Azure Active Directory** > **registros de aplicativo** no Azure AD, selecione seu aplicativo. Selecione **certificados & segredos**.

1. Selecione os **segredos do cliente-> novo segredo do cliente**.

1. Forneça uma descrição do segredo e uma duração. Quando terminar, selecione **Adicionar**.

   > [!NOTE]
   > Depois de salvar o segredo do cliente, o valor do segredo do cliente é exibido. Copie esse valor porque você não poderá recuperar a chave posteriormente.

   ![Copiar o valor secreto porque você não pode recuperá-lo mais tarde](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Configurar SSO de plataforma cívico

1. Abra uma nova janela do navegador da Web e entre no site da empresa do Atlassian Cloud como um administrador.

1. Clique em **opções padrão**.

    ![O link de download de certificado](media/civic-platform-tutorial/standard-choices.png)

1. Crie uma opção **ssoconfig**padrão.

1. Procure **ssoconfig** e envie.

    ![O link de download de certificado](media/civic-platform-tutorial/sso-config.png)

1. Expanda SSOCONFIG clicando no ponto vermelho.

    ![O link de download de certificado](media/civic-platform-tutorial/sso-config01.png)

1. Forneça informações de configuração relacionadas a SSO na seguinte etapa:

    ![O link de download de certificado](media/civic-platform-tutorial/sso-config02.png)

    1. No campo **ApplicationId** , insira o valor da **ID do aplicativo** que você copiou do portal do Azure.

    1. No campo **clientSecret** , insira o valor **secreto** que você copiou do portal do Azure.

    1. No campo **directoryid** , insira o valor da **ID do diretório (locatário)** que você copiou do portal do Azure.

    1. Insira o idpName. Por exemplo: `Azure`-.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso à plataforma cívico.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **plataforma cívico**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-civic-platform-test-user"></a>Criar usuário de teste da plataforma cívico

Nesta seção, você criará um usuário chamado B. Simon na plataforma cívico. Trabalhe com a equipe de suporte da plataforma civil para adicionar os usuários à [equipe de suporte ao cliente da plataforma cívico](mailto:skale@accela.com). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco plataforma cívico no painel de acesso, você deverá ser conectado automaticamente à plataforma cívico para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

