---
title: 'Tutorial: Integração de Azure Active Directory com informações adaptáveis | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e informações adaptáveis.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad059e6047dd883c0a5aab0d714d999840050584
ms.sourcegitcommit: ac29357a47cc05afdf0f84834de5277598f4d87c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70213628"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Tutorial: Integre informações adaptáveis com o Azure Active Directory

Neste tutorial, você aprenderá a integrar as informações adaptáveis com o Azure Active Directory (Azure AD). Ao integrar informações adaptáveis ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso a informações adaptáveis.
* Habilite seus usuários a serem automaticamente conectados a informações adaptáveis com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) habilitado para informações adaptáveis.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* As informações adaptáveis dão suporte ao SSO iniciado pelo **IDP**

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adicionando informações adaptáveis da Galeria

Para configurar a integração de ideias adaptáveis ao Azure AD, você precisa adicionar informações adaptáveis da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **informações adaptáveis** na caixa de pesquisa.
1. Selecione **informações adaptáveis** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com informações adaptáveis usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em informações adaptáveis.

Para configurar e testar o SSO do Azure AD com informações adaptáveis, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO](#configure-adaptive-insights-sso)** de insights adaptativos – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste do Adaptive](#create-adaptive-insights-test-user)** insights – para ter um equivalente de B. Simon em percepções adaptáveis vinculadas à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Adaptive** insights, localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Você pode obter o identificador (ID da entidade) e os valores da URL de resposta da página de **configurações de SSO do SAML das** ideias adaptáveis.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na seção **configurar informações adaptáveis** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Configurar SSO de insights adaptativos

1. Em uma janela diferente do navegador da Web, entre no site da empresa do insights Adaptive como administrador.

2. Vá para **Administração**.

    ![Administrador] do (./media/adaptivesuite-tutorial/ic805644.png "Administrador") do

3. Na seção **usuários e funções** , clique em **configurações de SSO do SAML**.

    ![Gerenciar configurações de SSO do SAML](./media/adaptivesuite-tutorial/ic805645.png "Gerenciar configurações de SSO do SAML")

4. Na página **configurações de SSO do SAML** , execute as seguintes etapas:

    ![Configurações de SSO do SAML](./media/adaptivesuite-tutorial/ic805646.png "Configurações de SSO do SAML")

    a. Na caixa de texto **nome do provedor de identidade** , digite um nome para a sua configuração.

    b. Cole o valor do **identificador do Azure ad** copiado de portal do Azure na caixa de texto **ID da entidade do provedor de identidade** .

    c. Cole o valor da **URL de logon** copiado de portal do Azure na caixa de texto URL de SSO do **provedor de identidade** .

    d. Cole o valor da **URL de logout** copiado de portal do Azure na caixa de texto **URL de logoff personalizado** .

    e. Para carregar o certificado baixado, clique em **escolher arquivo**.

    f. Selecione o seguinte, para:

     * **ID de usuário do SAML**, selecione **nome de usuário de informações adaptáveis do usuário**.

     * **Local da ID de usuário do SAML**, selecione a **ID de usuário em NameID do assunto**.

     * **Formato NameID do SAML**, selecione **endereço de email**.

     * **Habilite o SAML**, selecione **permitir SSO do SAML e logon direto**do insights.

    g. Copie a **URL de SSO de informações adaptáveis** e Cole nas caixas de entrada **identificador (ID da entidade)** e **URL de resposta** na seção **configuração básica do SAML** no portal do Azure.

    h. Clique em **Guardar**.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso a ideias adaptáveis.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **informações adaptáveis**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-adaptive-insights-test-user"></a>Criar usuário de teste do Adaptive insights

Para permitir que os usuários do Azure AD se conectem a ideias adaptáveis, eles devem ser provisionados em ideias adaptáveis. No caso de ideias adaptáveis, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do insights **Adaptive** como administrador.

2. Vá para **Administração**.

   ![Administrador] do (./media/adaptivesuite-tutorial/IC805644.png "Administrador") do

3. Na seção **usuários e funções** , clique em **usuários**.

   ![Adicionar usuário](./media/adaptivesuite-tutorial/IC805648.png "Adicionar usuário")

4. Na seção **novo usuário** , execute as seguintes etapas:

   ![Enviar](./media/adaptivesuite-tutorial/IC805649.png "Enviar")

   a. Digite o **nome**, o **nome**de usuário, o **email**e a **senha** de um usuário de Azure Active Directory válido que você deseja provisionar nas caixas de texto relacionadas.

   b. Selecione uma **função**.

   c. Clique em **Submit** (Submeter).

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário de informações adaptáveis ou APIs fornecidas por ideias adaptáveis para provisionar contas de usuário do AAD.

### <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco insights adaptativos no painel de acesso, você deverá ser conectado automaticamente às informações adaptáveis para as quais você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

