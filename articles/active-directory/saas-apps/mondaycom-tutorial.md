---
title: 'Tutorial: Integração do Azure Active Directory com o monday.com | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 771b7a07416458fd8523223ca57d3575ae2c85ff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033655"
---
# <a name="tutorial-integrate-mondaycom-with-azure-active-directory"></a>Tutorial: Integrar o monday.com ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o monday.com com o Azure Active Directory (Azure AD). Ao integrar o monday.com ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao monday.com.
* Habilite seus usuários a serem conectados automaticamente ao monday.com com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* assinatura habilitada para SSO (logon único) do monday.com.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. o monday.com dá suporte ao **SP e** ao SSO iniciado pelo IDP e dá suporte ao provisionamento **de usuário just-in-time** .

## <a name="adding-mondaycom-from-the-gallery"></a>Adicionando o monday.com da Galeria

Para configurar a integração do monday.com ao Azure AD, você precisará adicionar o monday.com da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Monday.com** na caixa de pesquisa.
1. Selecione **Monday.com** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o monday.com usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no monday.com.

Para configurar e testar o SSO do Azure AD com o monday.com, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
2. **[Configure o Monday.com](#configure-mondaycom)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
4. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Crie um usuário de teste do Monday.com](#create-mondaycom-test-user)** para ter um equivalente de B. Simon em Monday.com que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Monday.com** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. No painel **configuração básica do SAML** , se você tiver um arquivo de metadados do provedor de serviços e desejar configurá **-** lo no modo iniciado pelo IDP, execute as seguintes etapas:

    1. Selecione **carregar arquivo de metadados**.

    1. Para selecionar o arquivo de metadados, selecione o ícone de pasta e, em seguida, selecione **carregar**.

    1. Depois que o arquivo de metadados for carregado com êxito, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente no painel de **configuração básica do SAML** :

       > [!Note]
       > Se os valores do **identificador** e da **URL de resposta** não forem preenchidos automaticamente, preencha os valores manualmente. O **identificador** e a **URL de resposta** são os mesmos e o valor está no seguinte padrão:`https://<your-domain>.monday.com/saml/saml_callback`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<your-domain>.monday.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do Monday.com](mailto:dev@food.ee) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Seu aplicativo monday.com espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![O painel atributos de usuário](common/edit-attribute.png)

1. Além de acima, o aplicativo monday.com espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | User. sobrenome |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Remova o **namespace**.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**.

    g. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **configurar Monday.com** , copie as URLs apropriadas com base em seu requisito.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-mondaycom"></a>Configurar o monday.com

1. Para automatizar a configuração no monday.com, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **configurar Monday.com** , que o direcionará para o aplicativo Monday.com. A partir daí, forneça as credenciais de administrador para entrar no monday.com. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o monday.com manualmente, abra uma nova janela do navegador da Web e entre no monday.com como administrador e execute as seguintes etapas:

1. Vá para o **perfil** no canto superior direito da página e clique em **admin**.

    ![configuração do monday.com](./media/mondaycom-tutorial/configuration01.png)

1. Selecione **segurança** e certifique-se de clicar em **abrir** ao lado de SAML.

    ![configuração do monday.com](./media/mondaycom-tutorial/configuration02.png)

1. Preencha os detalhes abaixo de seu IDP.

    ![configuração do monday.com](./media/mondaycom-tutorial/configuration03.png)

    >[!NOTE]
    >Para obter mais detalhes, consulte [este](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) artigo

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao monday.com.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Monday.com**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-mondaycom-test-user"></a>Criar usuário de teste do monday.com

Nesta seção, um usuário chamado B. Simon é criado em monday.com. o monday.com dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no monday.com, um novo será criado quando você tentar acessar o monday.com.

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco monday.com no painel de acesso, você deverá entrar automaticamente no monday.com para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
