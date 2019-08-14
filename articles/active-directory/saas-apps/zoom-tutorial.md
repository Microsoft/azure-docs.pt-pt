---
title: 'Tutorial: Integração do Azure Active Directory com o zoom | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e36d1bb91e70e21ee1940e189bfedaebafa4412
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975957"
---
# <a name="tutorial-integrate-zoom-with-azure-active-directory"></a>Tutorial: Integrar zoom com Azure Active Directory

Neste tutorial, você aprenderá a integrar o zoom com o Azure Active Directory (Azure AD). Ao integrar o zoom ao Azure AD, você pode:

* Controle no Azure AD que tem acesso ao zoom.
* Habilite seus usuários a entrarem automaticamente no zoom com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura com zoom habilitado para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Zoom dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-zoom-from-the-gallery"></a>Adicionando o zoom da Galeria

Para configurar a integração do zoom ao Azure AD, você precisará adicionar o zoom da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **zoom** na caixa de pesquisa.
1. Selecione **zoom** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Configurar e testar o logon único do Azure AD para zoom

Configure e teste o SSO do Azure AD com o zoom usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no zoom.

Para configurar e testar o SSO do Azure AD com o zoom, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
2. **[Configurar o SSO de zoom](#configure-zoom-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do zoom](#create-zoom-test-user)** – para ter um equivalente de B. Simon no zoom que esteja vinculado à representação do usuário no Azure AD.
3. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **zoom** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<companyname>.zoom.us`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:`<companyname>.zoom.us`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do zoom](https://support.zoom.us/hc/en-us) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. O aplicativo de zoom espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo **atributos** de usuário.

    ![image](common/edit-attribute.png)

6. Além disso, o aplicativo de zoom espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações** do usuário, na caixa de diálogo **atributos** de usuário, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo: 

    | Nome | Espaço de Nomes  |  Atributo de origem|
    | ---------------| --------------- | --------- |
    | Endereço de e-mail  | user.mail  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail` |
    | Nome próprio  | user.givenname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | Apelido  | User. sobrenome  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | Número de telefone  | User. telephoneNumber  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone` |
    | Departamento  | user.department  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department` |
    | role |    user.assignedrole |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role` |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) para saber como configurar a função no Azure AD

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Selecione origem como **atributo**.

    d. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    e. Clique em **Ok**

    f. Clique em **Guardar**.

    > [!NOTE]
    > O zoom pode esperar a declaração de grupo na carga SAML, portanto, se você tiver criado qualquer grupo, entre em contato com a [equipe de suporte ao cliente de zoom](https://support.zoom.us/hc/en-us) com as informações do grupo para que eles possam configurar essas informações de grupo no final deles também. Você também precisa fornecer a ID do objeto para [ampliar a equipe de suporte do cliente](https://support.zoom.us/hc/en-us) para que ela possa ser configurada no final. Siga o [documento](https://support.zoom.us/hc/en-us/articles/115005887566) para obter a ID de objeto.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na seção **Configurar zoom** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao zoom.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **zoom**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-zoom-sso"></a>Configurar o SSO de zoom

1. Em uma janela diferente do navegador da Web, entre no site da empresa zoom como administrador.

2. Clique na guia **logon único** .

    ![Guia logon único](./media/zoom-tutorial/ic784700.png "Logon único")

3. Clique na guia **controle de segurança** e vá para as configurações de **logon único** .

4. Na seção logon único, execute as seguintes etapas:

    ![Seção logon único](./media/zoom-tutorial/ic784701.png "Logon único")

    a. Na caixa de texto **URL da página de entrada** , Cole o valor da URL de **logon** que você copiou do portal do Azure.

    b. Para o valor da **URL da página de saída** , você precisa ir para a Portal do Azure e clicar em **Azure Active Directory** à esquerda, em seguida, navegar até **registros de aplicativo**.

    ![O botão do Azure Active Directory](./media/zoom-tutorial/appreg.png)

    c. Clique em **pontos de extremidade**

    ![O botão do ponto de extremidade](./media/zoom-tutorial/endpoint.png)

    d. Copie o **ponto de extremidade de saída do SAML-P** e cole-o na caixa **de texto URL da página de saída** .

    ![O botão Copiar ponto de extremidade](./media/zoom-tutorial/endpoint1.png)

    e. Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado do provedor de identidade** .

    f. Na caixa de texto **emissor** , Cole o valor do **identificador do Azure ad** que você copiou do portal do Azure. 

    g. Clique em **Guardar**.

    > [!NOTE]
    > Para obter mais informações, visite a documentação de zoom[https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Criar usuário de teste de zoom

Para permitir que os usuários do AD do Azure entrem no zoom, eles devem ser provisionados no zoom. No caso do zoom, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Entre no site da empresa **zoom** como administrador.

2. Clique na guia **Gerenciamento de contas** e clique em **Gerenciamento de usuários**.

3. Na seção Gerenciamento de usuário, clique em **Adicionar usuários**.

    ![Gerenciamento de usuários](./media/zoom-tutorial/ic784703.png "Gerenciamento de usuários")

4. Na página **Adicionar usuários** , execute as seguintes etapas:

    ![Adicionar usuários](./media/zoom-tutorial/ic784704.png "Adicionar usuários")

    a. Como **tipo de usuário**, selecione **básico**.

    b. Na caixa de texto emails, digite o endereço de email de uma conta válida do Azure AD que você deseja provisionar.

    c. Clique em **Adicionar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário de zoom ou APIs fornecidas pelo zoom para provisionar Azure Active Directory contas de usuário.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco zoom no painel de acesso, você deverá ser conectado automaticamente ao zoom para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

