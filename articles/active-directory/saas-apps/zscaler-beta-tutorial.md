---
title: 'Tutorial: Integração do Azure Active Directory com o Zscaler beta | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07b0f8112f724c857ffb46378f7aa7ef605b9bbb
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943295"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: Integração do Azure Active Directory com o Zscaler beta

Neste tutorial, você aprenderá a integrar o Zscaler beta ao Azure Active Directory (Azure AD).
Ao integrar o Zscaler beta ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Zscaler beta.
* Permita que os usuários se conectem automaticamente ao Zscaler beta com suas contas do Azure AD. Esse controle de acesso é chamado SSO (logon único).
* Gerencie suas contas em um local central usando o portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS (software como serviço) com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Zscaler beta, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Zscaler beta que usa o logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zscaler beta dá suporte ao SSO iniciado por SP.
* O Zscaler beta dá suporte ao provisionamento de usuário just-in-time.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Adicionar o Zscaler beta do Azure Marketplace

Para configurar a integração do Zscaler beta ao Azure AD, adicione o Zscaler beta do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Zscaler beta do Azure Marketplace, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![Botão de Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **novo aplicativo** na parte superior da caixa de diálogo.

    ![Botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Zscaler beta**. Selecione **Zscaler beta** no painel de resultados e, em seguida, selecione **Adicionar**.

     ![Zscaler beta na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o Zscaler beta, com base no usuário de teste Brenda Simon.
Para que o logon único funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Zscaler beta.

Para configurar e testar o logon único do Azure AD com o Zscaler beta, conclua os seguintes blocos de construção:

- [Configure o logon único do Azure ad](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
- [Configure o logon único do Zscaler beta](#configure-zscaler-beta-single-sign-on) para definir as configurações de logon único no lado do aplicativo.
- [Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com o Brenda Simon.
- [Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user) para permitir que o Brenda Simon use o logon único do Azure AD.
- [Crie um usuário de teste do Zscaler beta](#create-a-zscaler-beta-test-user) para ter um equivalente de Brenda Simon no Zscaler beta que esteja vinculado à representação do usuário no Azure AD.
- [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Zscaler beta, siga estas etapas.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Zscaler beta** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , selecione **Editar** para abrir a caixa de diálogo **configuração básica do SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , siga esta etapa:

    ![Informações de logon único de domínio e URLs do Zscaler beta](common/sp-intiated.png)

    - Na caixa **URL de logon** , insira a URL usada pelos usuários para entrar no seu aplicativo Zscaler beta.

    > [!NOTE]
    > O valor não é real. Atualize o valor com o valor da URL de logon real. Para obter o valor, entre em contato com a [equipe de suporte ao cliente do Zscaler beta](https://www.zscaler.com/company/contact).

5. O aplicativo Zscaler beta espera que as asserções SAML estejam em um formato específico. Você deve adicionar mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Selecione **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![Caixa de diálogo atributos de usuário](common/edit-attribute.png)

6. O aplicativo Zscaler beta espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** na caixa de diálogo **atributos de usuário** , siga estas etapas para adicionar o atributo de token SAML, conforme mostrado na tabela a seguir.
    
    | Nome | Atributo de origem | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![Caixa de diálogo declarações do usuário](common/new-save-attribute.png)

    ![Caixa de diálogo Gerenciar declarações do usuário](common/new-attribute-details.png)

    b. Na caixa **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe a caixa **namespace** em branco.

    d. Para **origem**, selecione **atributo**.

    e. Na lista **atributo de origem** , insira o valor do atributo mostrado para essa linha.

    f. Selecione **OK**.

    g. Selecione **Guardar**.

    > [!NOTE]
    > Para saber como configurar funções no Azure AD, consulte [Configurar a declaração de função](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , selecione **baixar** para baixar o **certificado (Base64)** . Salve-o em seu computador.

    ![Link de download de certificado](common/certificatebase64.png)

8. Na seção **Configurar a versão beta do Zscaler** , copie as URLs necessárias para seus requisitos:

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    - URL de início de sessão
    - Identificador do Azure AD
    - URL de fim de sessão

### <a name="configure-zscaler-beta-single-sign-on"></a>Configurar o logon único do Zscaler beta

1. Para automatizar a configuração no Zscaler beta, instale a **extensão do navegador de entrada seguro de meus aplicativos** selecionando **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecionar **Configurar o Zscaler beta** direciona você para o aplicativo Zscaler beta. A partir daí, forneça as credenciais de administrador para entrar no Zscaler beta. A extensão do navegador configura automaticamente o aplicativo para você e automatiza as etapas 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Para configurar o Zscaler beta manualmente, abra uma nova janela do navegador da Web. Entre em seu site de empresa do Zscaler beta como administrador e siga estas etapas.

4. Vá para **Administração** > **autenticação** > **configurações de autenticação**e siga estas etapas.
   
    ![Administração] do (./media/zscaler-beta-tutorial/ic800206.png "Administração") do

    a. Em **tipo de autenticação**, selecione **SAML**.

    b. Selecione **Configurar SAML**.

5. Na janela **Editar SAML** , siga estas etapas: 
            
    ![Gerenciar usuários & autenticação](./media/zscaler-beta-tutorial/ic800208.png "Gerenciar usuários & autenticação")
    
    a. Na caixa **URL do portal do SAML** , Cole a **URL de logon** que você copiou do portal do Azure.

    b. Na caixa **atributo de nome de logon** ,digite NameID.

    c. Na caixa **certificado SSL público** , selecione **carregar** para carregar o certificado de autenticação SAML do Azure que você baixou do portal do Azure.

    d. Alterne **habilitar provisionamento automático de SAML**.

    e. Na caixa **atributo de nome de exibição do usuário** , digite **DisplayName** se desejar habilitar o provisionamento automático do SAML para atributos DisplayName.

    f. Na caixa **atributo do nome do grupo** , digite **memberOf** se desejar habilitar o provisionamento automático do SAML para atributos memberOf.

    g. Na caixa **atributo nome do departamento** , insira **Departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    h. Selecione **Guardar**.

6. Na página da caixa de diálogo **configurar autenticação de usuário** , siga estas etapas:

    ![Menu de ativação e botão Ativar](./media/zscaler-beta-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu de **ativação** na parte inferior esquerda.

    b. Selecione **Ativar**.

## <a name="configure-proxy-settings"></a>Definir configurações de proxy
Para definir as configurações de proxy no Internet Explorer, siga estas etapas.

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **ferramentas** para abrir a caixa de diálogo **Opções da Internet** . 
    
     ![Caixa de diálogo Opções da Internet](./media/zscaler-beta-tutorial/ic769492.png "Opções da Internet")

3. Selecione a guia **conexões** . 
  
     ![Guia conexões](./media/zscaler-beta-tutorial/ic769493.png "Conexões") do

4. Selecione **configurações de LAN** para abrir a caixa de diálogo **configurações de rede local (LAN)** .

5. Na seção **servidor proxy** , siga estas etapas: 
   
    ![Seção do servidor proxy](./media/zscaler-beta-tutorial/ic769494.png "Servidor proxy")

    a. Marque a caixa de seleção **usar um servidor proxy para sua LAN** .

    b. Na caixa **endereço** , insira **Gateway. Zscaler Beta.net**.

    c. Na caixa **porta** , digite **80**.

    d. Marque a caixa de seleção **ignorar servidor proxy para endereços locais** .

    e. Selecione **OK** para fechar a caixa de diálogo **configurações de rede local (LAN)** .

6. Selecione **OK** para fechar a caixa de diálogo **Opções da Internet** .

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

Crie um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory** > **usuários** > **todos os usuários**.

    ![Links de usuários e todos os usuários](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Na caixa de diálogo **usuário** , siga estas etapas:

    ![Caixa de diálogo usuário](common/user-properties.png)

    a. Na caixa **nome** , digite **brendafernandes**.
  
    b. Na caixa **nome de usuário** , digite `brittasimon@yourcompanydomain.extension`. Um exemplo é BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** . Anote o valor exibido na caixa **senha** .

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Habilite Brenda Simon para usar o logon único do Azure concedendo acesso ao Zscaler beta.

1. Na portal do Azure, selecione **aplicativos** > empresariais**todos os aplicativos** > **Zscaler beta**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, insira e selecione **Zscaler beta**.

    ![Link do Zscaler beta na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![Link de usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar atribuição** , selecione **usuários e grupos**.

    ![Botão Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione o usuário como **Brenda Simon** na lista. Em seguida, escolha **selecionar** na parte inferior da tela.

    ![Caixa de diálogo usuários e grupos](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Na caixa de diálogo **selecionar função** , selecione a função de usuário apropriada na lista. Em seguida, escolha **selecionar** na parte inferior da tela.

    ![Caixa de diálogo Selecionar função](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Na caixa de diálogo **Adicionar atribuição** , selecione **atribuir**.

    ![Caixa de diálogo Adicionar atribuição](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Criar um usuário de teste do Zscaler beta

Nesta seção, o usuário Brenda Simon é criado no Zscaler beta. O Zscaler beta dá suporte ao **provisionamento de usuário just-in-time**, que é habilitado por padrão. Não há nada para você fazer nesta seção. Se um usuário ainda não existir no Zscaler beta, um novo será criado após a autenticação.

>[!Note]
>Para criar um usuário manualmente, entre em contato com a [equipe de suporte do Zscaler beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Teste sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco Zscaler beta no painel de acesso, você deverá entrar automaticamente no Zscaler beta para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

