---
title: 'Tutorial: integração do Azure Active Directory com o software Cezanne HR | Microsoft Docs'
description: Saiba como configurar o logon único entre o software Azure Active Directory e o Cezanne HR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa689e6138f8d965e59f7cfa7a85e0835301086c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158709"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: integração do Azure Active Directory com o software Cezanne HR

Neste tutorial, você aprenderá a integrar o Cezanne HR software ao Azure Active Directory (Azure AD).
A integração do Cezanne HR software ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Cezanne HR software.
* Você pode permitir que seus usuários façam logon automaticamente no Cezanne HR software (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Cezanne HR software, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Cezanne HR software

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O software Cezanne HR dá suporte ao SSO iniciado por **SP**

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adicionando o software Cezanne HR da Galeria

Para configurar a integração do Cezanne HR software ao Azure AD, você precisará adicionar o Cezanne HR software da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cezanne HR software por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Cezanne HR software**, selecione **Cezanne HR software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Cezanne HR software na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Cezanne HR software, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cezanne HR software.

Para configurar e testar o logon único do Azure AD com o Cezanne HR software, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Cezanne HR software](#configure-cezanne-hr-software-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Cezanne HR software](#create-cezanne-hr-software-test-user)** – para ter um equivalente de Brenda Simon no Cezanne HR software que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Cezanne HR software, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cezanne HR software** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Cezanne HR software](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Na caixa de texto **identificador (ID da entidade)** , digite a URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e a URL de resposta reais. Contate a [equipe de suporte ao cliente do Cezanne HR software](https://cezannehr.com/services/support/) para obter esses valores.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **configurar software Cezanne HR** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Configurar o logon único do software Cezanne HR

1. Em uma janela diferente do navegador da Web, faça logon no seu locatário do Cezanne HR software como administrador.

2. No painel de navegação esquerdo, clique em **configuração do sistema**. Vá para **configurações de segurança**. Em seguida, navegue até **configuração de logon único**.

    ![Configurar o logon único no lado do aplicativo](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. Em **permitir que os usuários façam logon usando o seguinte painel de serviço SSO (logon único)** , marque a caixa **SAML 2,0** e selecione a opção **Configuração avançada** .

    ![Configurar o logon único no lado do aplicativo](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Clique em **Adicionar novo** botão.

    ![Configurar o logon único no lado do aplicativo](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Execute as etapas a seguir na seção de **provedores de identidade SAML 2,0** .

    ![Configurar o logon único no lado do aplicativo](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Insira o nome do seu provedor de identidade como o **nome de exibição**.

    b. Na caixa de texto **identificador de entidade** , Cole o valor do **identificador do Azure ad** que você copiou do portal do Azure.

    c. Altere a **Associação SAML** para ' Post '.

    d. Na caixa de texto **ponto de extremidade do serviço de token de segurança** , Cole o valor da URL de **logon** que você copiou do portal do Azure.

    e. Na caixa de texto nome do atributo de ID de usuário, insira `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    f. Clique no ícone **carregar** para carregar o certificado baixado de portal do Azure.

    g. Clique no botão **OK**.

6. Clique no botão **salvar** .

    ![Configurar o logon único no lado do aplicativo](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao software Cezanne HR.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Cezanne HR software**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Cezanne HR software**.

    ![O link do Cezanne HR software na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-cezanne-hr-software-test-user"></a>Criar usuário de teste do Cezanne HR software

Para permitir que os usuários do AD do Azure façam logon no Cezanne HR software, eles devem ser provisionados no software Cezanne HR. No caso do software Cezanne HR, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Cezanne HR software como administrador.

2. No painel de navegação esquerdo, clique em **configuração do sistema**. Vá para **gerenciar usuários**. Em seguida, navegue até **Adicionar novo usuário**.

    ![Novo usuário](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Novo usuário")

3. Na seção de **detalhes da pessoa** , execute as etapas abaixo:

    ![Novo usuário](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Novo usuário")

    a. Defina **usuário interno** como desativado.

    b. Na caixa de texto **nome** , digite o nome do usuário, como **Brenda**.  

    c. Na caixa de texto **sobrenome** , digite o sobrenome do usuário, como **Simon**.

    d. Na caixa de texto **email** , digite o endereço de email do usuário, como Brittasimon@contoso.com.

4. Na seção **informações da conta** , execute as etapas abaixo:

    ![Novo usuário](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Novo usuário")

    a. Na caixa de texto **username** , digite o email do usuário, como Brittasimon@contoso.com.

    b. Na caixa de texto **senha** , digite a senha do usuário.

    c. Selecione **HR Professional** como **função de segurança**.

    d. Clique em **OK**.

5. Navegue até a guia **logon único** e selecione **Adicionar novo** na área **identificadores SAML 2,0** .

    ![Usuário](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utilizador")

6. Escolha seu provedor de identidade para o **provedor de identidade** e, na caixa de texto do identificador de **usuário**, insira o endereço de email da conta Brenda Simon.

    ![Usuário](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utilizador")

7. Clique no botão **salvar** .

    ![Usuário](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utilizador")

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Cezanne HR software no painel de acesso, você deverá entrar automaticamente no software Cezanne HR para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
