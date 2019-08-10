---
title: 'Tutorial: Integração do Azure Active Directory com o CloudGuard Dome9 Arc | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e Check Point CloudGuard Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 240d962d56e4a2dc0758f3170c51b343d22ef98d
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944581"
---
# <a name="tutorial-integrate-check-point-cloudguard-dome9-arc-with-azure-active-directory"></a>Tutorial: Integrar o arco do Check Point CloudGuard Dome9 com Azure Active Directory

Neste tutorial, você aprenderá a integrar o Arc do Check Point CloudGuard Dome9 com o Azure Active Directory (Azure AD). Ao integrar o CloudGuard Dome9 Arc ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao CloudGuard Dome9 Arc.
* Habilite seus usuários a serem conectados automaticamente ao CloudGuard Dome9 Arc com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do CloudGuard Dome9 Arc.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O CloudGuard Dome9 Arc suporta o **SP e** o SSO iniciado pelo IDP.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Adicionando o CloudGuard Dome9 Arc do Check Point da Galeria

Para configurar a integração do CloudGuard Dome9 Arc ao Azure AD, você precisará adicionar o Check Point CloudGuard Dome9 Arc da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **CloudGuard Dome9 Arc** na caixa de pesquisa.
1. Selecione o **ponto de verificação CloudGuard Dome9 Arc** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o Arc do Check Point CloudGuard Dome9 usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no arco do Check Point CloudGuard Dome9.

Para configurar e testar o SSO do Azure AD com o CloudGuard do Check Point Dome9 Arc, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
2. **[Configure Check Point CloudGuard Dome9 Arc](#configure-check-point-cloudguard-dome9-arc)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
4. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Crie um usuário de teste do CloudGuard Dome9 Arc](#create-check-point-cloudguard-dome9-arc-test-user)** para que ele tenha um equivalente de B. Simon no ponto de verificação de CloudGuard de Dome9 que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **CloudGuard Dome9 Arc** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:`https://secure.dome9.com/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Você selecionará o valor nome da empresa no portal de administração do dome9, que é explicado posteriormente no tutorial.

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do CloudGuard Dome9 Arc Check Point](mailto:Dome9@checkpoint.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

6. O aplicativo Check Point CloudGuard Dome9 Arc espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![image](common/edit-attribute.png)

7. Além de acima, o aplicativo de arco do Check Point CloudGuard Dome9 espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo: 

    | Nome |  Atributo de origem|
    | ---------------| --------------- |
    | memberOf | user.assignedroles |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar o ponto de verificação CloudGuard Dome9 Arc** , copie as URLs apropriadas com base em seu requisito.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-check-point-cloudguard-dome9-arc"></a>Configurar o Arc do Check Point CloudGuard Dome9

1. Em outra janela do navegador da Web, entre no site da empresa do CloudGuard Arc do Check Point como administrador.

2. Clique nas **configurações de perfil** no canto superior direito e clique em **configurações de conta**. 

    ![Configuração de Arc do Check Point CloudGuard Dome9](./media/dome9arc-tutorial/configure1.png)

3. Navegue até **SSO** e clique em **habilitar**.

    ![Configuração de Arc do Check Point CloudGuard Dome9](./media/dome9arc-tutorial/configure2.png)

4. Na seção configuração de SSO, execute as seguintes etapas:

    ![Configuração de Arc do Check Point CloudGuard Dome9](./media/dome9arc-tutorial/configure3.png)

    a. Insira o nome da empresa na caixa de texto **ID da conta** . Esse valor deve ser usado na URL de resposta mencionada na seção de **configuração portal do Azure Basic SAML** .

    b. Na caixa de texto **emissor** , Cole o valor do **identificador do Azure ad**, que você copiou o formulário portal do Azure.

    c. Na caixa de texto **URL do ponto de extremidade do IDP** , Cole o valor da URL de **logon**, que você copiou o formulário portal do Azure.

    d. Abra seu certificado codificado em base64 baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado X. 509** .

    e. Clique em **Guardar**.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao CloudGuard Dome9 Arc do Check Point.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Check Point CloudGuard Dome9 Arc**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Criar usuário de teste do Check Point CloudGuard Dome9 Arc

Para permitir que os usuários do Azure AD entrem no CloudGuard Dome9 Arc, eles devem ser provisionados no aplicativo. O Check Point CloudGuard Dome9 Arc dá suporte ao provisionamento just-in-time, mas para que funcione corretamente, o usuário precisa selecionar uma **função** específica e atribuir o mesmo ao usuário.

   >[!Note]
   >Para a criação de **função** e outros detalhes, entre em contato com a [equipe de suporte ao cliente do CloudGuard Dome9 Arc Check Point](mailto:Dome9@checkpoint.com).

**Para provisionar uma conta de usuário manualmente, execute as seguintes etapas:**

1. Entre no site da empresa do CloudGuard Dome9 Arc do Check Point como administrador.

2. Clique em **usuários & funções** e, em seguida, clique em **usuários**.

    ![Adicionar funcionário](./media/dome9arc-tutorial/user1.png)

3. Clique em **Adicionar usuário**.

    ![Adicionar funcionário](./media/dome9arc-tutorial/user2.png)

4. Na seção **criar usuário** , execute as seguintes etapas:

    ![Adicionar funcionário](./media/dome9arc-tutorial/user3.png)

    a. Na caixa de texto **email** , digite o email do usuário B.Simon@contoso.comcomo.

    b. Na caixa de texto **nome** , digite o nome do usuário, como B.

    c. Na caixa de texto **sobrenome** , digite o sobrenome do usuário, como Simon.

    d. Tornar o **usuário SSO** como **ativado**.

    e. Clique em **criar**.

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco CloudGuard Dome9 Arc no painel de acesso, você deve entrar automaticamente no arco do Check Point CloudGuard Dome9 para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)