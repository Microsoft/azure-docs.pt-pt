---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com Zscaler | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcf0341e03a5d95abbe8b1a8ce69379fef8251b7
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989054"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com Zscaler

Neste tutorial, você aprenderá a integrar o Zscaler com o Azure Active Directory (Azure AD). Ao integrar o Zscaler ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Zscaler.
* Habilite seus usuários a serem conectados automaticamente ao Zscaler com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Zscaler.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Zscaler dá suporte ao SSO iniciado por **SP**
* O Zscaler dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-zscaler-from-the-gallery"></a>Adicionando o Zscaler da Galeria

Para configurar a integração do Zscaler ao Azure AD, você precisará adicionar o Zscaler da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Zscaler** na caixa de pesquisa.
1. Selecione **Zscaler** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler"></a>Configurar e testar o logon único do Azure AD para o Zscaler

Configure e teste o SSO do Azure AD com o Zscaler usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Zscaler.

Para configurar e testar o SSO do Azure AD com o Zscaler, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Zscaler](#configure-zscaler-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Zscaler](#create-zscaler-test-user)** – para ter um equivalente de B. Simon em Zscaler que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Zscaler** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<companyname>.zscaler.net`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Zscaler](https://www.zscaler.com/company/contact) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Seu aplicativo Zscaler espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)

1. Além de acima, o aplicativo Zscaler espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Atributo de origem |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar a função no Azure AD

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar Zscaler** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Zscaler.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Zscaler**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler**.

    ![O link do Zscaler na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione o usuário como **Brenda Simon** na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_users.png)

6. Na caixa de diálogo **selecionar função** , escolha a função de usuário apropriada na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_roles.png)

7. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_assign.png)

## <a name="configure-zscaler-sso"></a>Configurar o SSO do Zscaler

1. Para automatizar a configuração no Zscaler, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Setup Zscaler** irá direcioná-lo para o aplicativo Zscaler. A partir daí, forneça as credenciais de administrador para entrar no Zscaler. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configurar SSO](common/setup-sso.png)

1. Se você quiser configurar o Zscaler manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Zscaler como administrador e execute as seguintes etapas:

1. Acesse **administração > autenticação > configurações de autenticação** e execute as seguintes etapas:

    ![Administração] do (./media/zscaler-tutorial/ic800206.png "Administração") do

    a. Em tipo de autenticação, escolha **SAML**.

    b. Clique em **Configurar SAML**.

1. Na janela **Editar SAML** , execute as seguintes etapas: e clique em salvar.  

    ![Gerenciar usuários & autenticação](./media/zscaler-tutorial/ic800208.png "Gerenciar usuários & autenticação")
    
    a. Na caixa de texto **URL do portal do SAML** , Cole a URL de **logon** que você copiou de portal do Azure.

    b. Na caixa de texto **atributo de nome de logon** , digite **NameID**.

    c. Clique em **carregar**para carregar o certificado de autenticação SAML do Azure que você baixou de portal do Azure no **certificado SSL público**.

    d. Ativar/desativar o **provisionamento automático do SAML**.

    e. Na caixa de texto **atributo de nome de exibição do usuário** , digite **DisplayName** se desejar habilitar o provisionamento automático do SAML para atributos DisplayName.

    f. Na caixa de texto **atributo de nome do grupo** , digite **memberOf** se desejar habilitar o provisionamento automático do SAML para atributos memberOf.

    g. No **atributo nome do departamento** , insira **Departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    h. Clique em **Guardar**.

1. Na página da caixa de diálogo **configurar autenticação de usuário** , execute as seguintes etapas:

    ![Administração](./media/zscaler-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu de **ativação** próximo à parte inferior esquerda.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

1. Selecione **Opções da Internet** no menu **ferramentas** para abrir a caixa de diálogo **Opções da Internet** .

    ![Opções da Internet](./media/zscaler-tutorial/ic769492.png "Opções da Internet")

1. Clique na guia **conexões** .
  
    ![Conexões] do (./media/zscaler-tutorial/ic769493.png "Conexões") do

1. Clique em **configurações de LAN** para abrir a caixa de diálogo Configurações de **LAN** .

1. Na seção servidor proxy, execute as seguintes etapas:   

    ![Servidor proxy](./media/zscaler-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **usar um servidor proxy para sua LAN**.

    b. Na caixa de texto endereço, digite **Gateway.Zscaler.net**.

    c. Na caixa de texto porta, digite **80**.

    d. Selecione **ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a caixa de diálogo **configurações de rede local (LAN)** .

1. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet** .

### <a name="create-zscaler-test-user"></a>Criar usuário de teste do Zscaler

Nesta seção, um usuário chamado Brenda Simon é criado em Zscaler. O Zscaler dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Zscaler, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Zscaler](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Zscaler no painel de acesso, você deverá ser conectado automaticamente ao Zscaler para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Zscaler com o Azure AD](https://aad.portal.azure.com/)
