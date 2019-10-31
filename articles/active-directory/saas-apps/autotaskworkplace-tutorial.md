---
title: 'Tutorial: integração do Azure Active Directory com o AutoTask Workplace | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AutoTask workplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97a68fd74449ea34438348cbc4c9d09c06d46d71
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73157803"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Tutorial: integração do Azure Active Directory com o autotarefa Workplace

Neste tutorial, você aprenderá a integrar o AutoTask Workplace ao Azure Active Directory (Azure AD).
A integração do AutoTask Workplace ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao AutoTask workplace.
* Você pode permitir que seus usuários sejam automaticamente conectados ao AutoTask Workplace (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao AutoTask workplace, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do AutoTask Workplace
* Uma assinatura habilitada para logon único do AutoTask Workplace
* Você deve ser um administrador ou um superadministrador no local de trabalho.
* Você deve ter uma conta de administrador no Azure AD.
* Os usuários que utilizarão esse recurso deverão ter contas no local de trabalho e no Azure AD, e seus endereços de email para ambos devem corresponder.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O autotarefa Workplace dá suporte a **SP** iniciado pelo

## <a name="adding-autotask-workplace-from-the-gallery"></a>Adicionando o AutoTask Workplace por meio da Galeria

Para configurar a integração do AutoTask Workplace ao Azure AD, você precisará adicionar o AutoTask Workplace da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o AutoTask Workplace por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **autotarefa Workplace**, selecione **autotarefa Workplace** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Autotarefa Workplace na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o AutoTask workplace, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do AutoTask workplace.

Para configurar e testar o logon único do Azure AD com o AutoTask workplace, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do AutoTask Workplace](#configure-autotask-workplace-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do AutoTask Workplace](#create-autotask-workplace-test-user)** – para ter um equivalente de Brenda Simon no AutoTask Workplace que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o AutoTask workplace, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **AutoTask Workplace** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do AutoTask Workplace](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do AutoTask Workplace](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do AutoTask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

7. Na seção **Configurar local de trabalho de autotarefa** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-autotask-workplace-single-sign-on"></a>Configurar o logon único do AutoTask Workplace

1. Em uma janela diferente do navegador da Web, faça logon no workplace online usando as credenciais de administrador.

    > [!Note]
    > Ao configurar o IdP, será necessário especificar um subdomínio. Para confirmar o subdomínio correto, faça logon no workplace online. Depois de conectado, anote para o subdomínio na URL. O subdomínio é a parte entre "https://" e ". awp.autotask.net/" e deve ser US, eu, CA ou au.

2. Vá para **configuração** > **logon único** e execute as seguintes etapas:

    ![Configuração de logon único de autotarefa](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    a. Selecione a opção **arquivo de metadados XML** e carregue o XML de **metadados de federação** baixado de portal do Azure.

    b. Clique em **habilitar SSO**.

    ![Configuração de aprovação de logon único do AutoTask](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Marque a caixa de seleção **confirmo que esta informação está correta e eu confio neste IDP** .

    d. Clique em **aprovar**.

> [!Note]
> Se você precisar de assistência com a configuração do AutoTask workplace, consulte [esta página](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para obter assistência com sua conta de local de trabalho.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao AutoTask workplace.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **AutoTask Workplace**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **autotarefa Workplace**.

    ![O link de autotarefa do workplace na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-autotask-workplace-test-user"></a>Criar usuário de teste do AutoTask Workplace

Nesta seção, você criará um usuário chamado Brenda Simon no AutoTask workplace. Trabalhe com a [equipe de suporte do autotarefa Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para adicionar os usuários à plataforma do autotarefa workplace.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Quando você clicar no bloco AutoTask Workplace no painel de acesso, deverá ser automaticamente conectado ao AutoTask Workplace para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
