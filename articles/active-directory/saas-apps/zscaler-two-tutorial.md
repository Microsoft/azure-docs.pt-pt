---
title: 'Tutorial: integração do Azure Active Directory com o Zscaler dois | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler dois.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 7ea4eecd5c27b6a9f14bc358c7db61da544e7530
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68825089"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Tutorial: integração de Azure Active Directory com o Zscaler dois

Neste tutorial, você aprenderá a integrar o Zscaler Two ao Azure Active Directory (Azure AD).
A integração do Zscaler Two ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Zscaler dois.
* Você pode permitir que seus usuários façam logon automaticamente no Zscaler dois (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Zscaler dois, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Zscaler duas assinaturas habilitadas para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Zscaler dois dá suporte ao SSO iniciado por **SP**

* Zscaler dois dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-zscaler-two-from-the-gallery"></a>Adicionando Zscaler dois da Galeria

Para configurar a integração do Zscaler dois ao Azure AD, você precisará adicionar o Zscaler dois da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler dois da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler dois**, selecione **Zscaler dois** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Zscaler dois na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zscaler dois, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zscaler dois.

Para configurar e testar o logon único do Azure AD com o Zscaler dois, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Zscaler dois logon único](#configure-zscaler-two-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar Zscaler dois usuários de teste](#create-zscaler-two-test-user)** – para ter um equivalente de Brenda Simon no Zscaler dois que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Zscaler dois, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Zscaler Two** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Zscaler duas](common/sp-signonurl.png)

    Na caixa de texto URL de logon, digite a URL usada pelos usuários para fazer logon no seu aplicativo ZScaler dois.

    > [!NOTE]
    > Você atualiza o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Zscaler Two](https://www.zscaler.com/company/contact) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Seu aplicativo Zscaler dois espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

6. Além de acima, Zscaler dois aplicativos esperam que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:
    
    | Nome | Atributo de origem |
    | ---------| ------------ |
    | memberOf     | User. assignedroles |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.
    
    f. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar a função no Azure AD

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

8. Na seção **Configurar Zscaler dois** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-zscaler-two-single-sign-on"></a>Configurar o logon único do Zscaler dois

1. Para automatizar a configuração no Zscaler dois, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar Zscaler dois** direcionarão você para o aplicativo Zscaler dois. A partir daí, forneça as credenciais de administrador para entrar no Zscaler dois. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configurar SSO](common/setup-sso.png)

3. Se você quiser configurar o Zscaler dois manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Zscaler dois como administrador e execute as seguintes etapas:

4. Acesse **administração > autenticação > configurações de autenticação** e execute as seguintes etapas:
   
    ![Administrar](./media/zscaler-two-tutorial/ic800206.png "Administração")

    a. Em tipo de autenticação, escolha **SAML**.

    b. Clique em **Configurar SAML**.

5. Na janela **Editar SAML** , execute as seguintes etapas: e clique em salvar.  
            
    ![Gerenciar usuários & autenticação](./media/zscaler-two-tutorial/ic800208.png "Gerenciar usuários & autenticação")
    
    a. Na caixa de texto **URL do portal do SAML** , Cole a URL de **logon** que você copiou de portal do Azure.

    b. Na caixa de texto **atributo de nome de logon** , digite **NameID**.

    c. Clique em **carregar**para carregar o certificado de autenticação SAML do Azure que você baixou de portal do Azure no **certificado SSL público**.

    d. Ativar/desativar o **provisionamento automático do SAML**.

    e. Na caixa de texto **atributo de nome de exibição do usuário** , digite **DisplayName** se desejar habilitar o provisionamento automático do SAML para atributos DisplayName.

    f. Na caixa de texto **atributo de nome do grupo** , digite **memberOf** se desejar habilitar o provisionamento automático do SAML para atributos memberOf.

    g. No **atributo nome do departamento** , insira **Departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    h. Clique em **Guardar**.

6. Na página da caixa de diálogo **configurar autenticação de usuário** , execute as seguintes etapas:

    ![Administração](./media/zscaler-two-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu de **ativação** próximo à parte inferior esquerda.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **ferramentas** para abrir a caixa de diálogo **Opções da Internet** .   
    
     ![Opções da Internet](./media/zscaler-two-tutorial/ic769492.png "Opções da Internet")

3. Clique na guia **conexões** .   
  
     ![Ligações](./media/zscaler-two-tutorial/ic769493.png "Ligações")

4. Clique em **configurações de LAN** para abrir a caixa de diálogo Configurações de **LAN** .

5. Na seção servidor proxy, execute as seguintes etapas:   
   
    ![Servidor proxy](./media/zscaler-two-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **usar um servidor proxy para sua LAN**.

    b. Na caixa de texto endereço, digite **Gateway. Zscaler Two.net**.

    c. Na caixa de texto porta, digite **80**.

    d. Selecione **ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a caixa de diálogo **configurações de rede local (LAN)** .

6. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet** .

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo-lhe acesso ao Zscaler dois.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Zscaler dois**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zscaler dois**.

    ![O link do Zscaler dois na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione o usuário como **Brenda Simon** na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

    ![imagem](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. Na caixa de diálogo **selecionar função** , escolha a função de usuário apropriada na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

    ![imagem](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. Na caixa de diálogo **Adicionar atribuição** , selecione o botão **atribuir** .

    ![imagem](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

### <a name="create-zscaler-two-test-user"></a>Criar Zscaler dois usuários de teste

Nesta seção, um usuário chamado Brenda Simon é criado no Zscaler dois. O Zscaler Two dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Zscaler dois, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Zscaler dois](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Zscaler Two no painel de acesso, você deverá entrar automaticamente no Zscaler dois para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

