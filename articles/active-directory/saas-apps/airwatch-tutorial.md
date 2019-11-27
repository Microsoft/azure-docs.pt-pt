---
title: 'Tutorial: integração do Azure Active Directory com o relógio | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o relógio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 772b37816b83c275bae927d825434dc3ca76a35c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231997"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: integrar o relógio com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o relógio com o Azure Active Directory (Azure AD). Ao integrar o relógio do Microsoft Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao relógio do Watch.
* Habilite seus usuários a serem conectados automaticamente ao monitor do Watch com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para logon único (SSO) do relógio Watch.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O relógio de monitoramento dá suporte ao SSO iniciado pelo **SP** .

## <a name="adding-airwatch-from-the-gallery"></a>Adicionando o relógio da Galeria

Para configurar a integração do relógio do Watch ao Azure AD, você precisa adicionar o relógio da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **relógio** na caixa de pesquisa.
1. Selecione o **relógio do monitor** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o relógio Watch usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no relógio do Watch.

Para configurar e testar o SSO do Azure AD com o enwatch, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar SSO do relógio de monitoramento](#configure-airwatch-sso)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Criar usuário de teste do relógio de Watch](#create-airwatch-test-user)** – para ter um equivalente de Brenda Simon no relógio que esteja vinculado à representação do usuário no Azure AD.
5. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **relógio do Watch** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **configuração básica do SAML** , insira os valores para os seguintes campos:

    1. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Na caixa de texto **identificador (ID da entidade)** , digite o valor como: `AirWatch`

    > [!NOTE]
    > Esse valor não é o real. Atualize esse valor com a URL de logon real. Contate a [equipe de suporte ao cliente do enwatch](https://www.air-watch.com/company/contact-us/) para obter esse valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo do relógio de inspeção espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)

1. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome |  Atributo de origem|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**

    g. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o XML de metadados e salvá-lo no computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o relógio de inspeção** , copie as URLs apropriadas com base em seu requisito.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Configurar SSO do relógio de inspeção

1. Em uma janela diferente do navegador da Web, entre em seu site de empresa do relógio de inspeção como administrador.

1. Na página Configurações. Selecione **configurações > Enterprise Integration > serviços de diretório**.

   ![Definições](./media/airwatch-tutorial/ic791921.png "Definições")

1. Clique na guia **usuário** , na caixa de texto **DN base** , digite o nome de domínio e clique em **salvar**.

   ![Usuário](./media/airwatch-tutorial/ic791922.png "Utilizador")

1. Clique na guia **servidor** .

   ![Servidor](./media/airwatch-tutorial/ic791923.png "Servidor")

1. Execute as seguintes etapas na seção **LDAP** :

    ![Carregar](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Como **tipo de diretório**, selecione **nenhum**.

    b. Selecione **usar SAML para autenticação**.

1. Na seção **SAML 2,0** , para carregar o certificado baixado, clique em **carregar**.

    ![Carregar](./media/airwatch-tutorial/ic791932.png "Carregar")

1. Na seção **solicitação** , execute as seguintes etapas:

    ![Pedido](./media/airwatch-tutorial/ic791925.png "Pedir")  

    a. Como **tipo de associação de solicitação**, selecione **post**.

    b. Na portal do Azure, na página da caixa de diálogo **Configurar logon único no enwatch** , copie o valor da **URL de logon** e cole-o na caixa de texto URL de **logon único do provedor de identidade** .

    c. Como **formato de NameID**, selecione **endereço de email**.

    d. Como **segurança da solicitação de autenticação**, selecione **nenhuma**.

    e. Clique em **Guardar**.

1. Clique na guia **usuário** novamente.

    ![Usuário](./media/airwatch-tutorial/ic791926.png "Utilizador")

1. Na seção **atributo** , execute as seguintes etapas:

    ![Attribute](./media/airwatch-tutorial/ic791927.png "Atributo")

    a. Na caixa de texto **identificador de objeto** , digite `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Na caixa de texto **nome de usuário** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Na caixa de texto **nome de exibição** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Na caixa de texto **nome** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Na caixa de texto **sobrenome** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Na caixa de texto **email** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao relógio Watch.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Monitor de relógio**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-airwatch-test-user"></a>Criar usuário de teste do relógio de Watch

Para permitir que os usuários do Azure AD entrem no relógio Watch, eles devem ser provisionados no relógio do Watch. No caso do relógio do Watch, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do **relógio de inspeção** como administrador.

2. No painel de navegação no lado esquerdo, clique em **contas**e em **usuários**.
  
   ![Utilizadores](./media/airwatch-tutorial/ic791929.png "Utilizadores")

3. No menu **usuários** , clique em **exibição de lista**e, em seguida, clique em **Adicionar > Adicionar usuário**.
  
   ![Adicionar usuário](./media/airwatch-tutorial/ic791930.png "Adicionar Utilizador")

4. Na caixa de diálogo **Adicionar/editar usuário** , execute as seguintes etapas:

   ![Adicionar usuário](./media/airwatch-tutorial/ic791931.png "Adicionar Utilizador")

   a. Digite o nome de **usuário**, a **senha**, **confirme a senha**, o **nome**, o **sobrenome**, o **endereço de email** de uma conta de Azure Active Directory válida que você deseja provisionar nas caixas de texto relacionadas.

   b. Clique em **Guardar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário do relógio ou APIs fornecidas pelo relógio para provisionar contas de usuário do Azure AD.

### <a name="test-sso"></a>Testar SSO

Quando você seleciona o bloco do relógio no painel de acesso, você deve entrar automaticamente no relógio de monitoramento para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
