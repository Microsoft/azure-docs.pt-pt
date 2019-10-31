---
title: 'Tutorial: integração do Azure Active Directory com o Citrix Sharefile | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Citrix sharefile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d2fe97a4af07f48038ffd88de648247b56bb80
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160026"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: integração do Azure Active Directory com o Citrix Sharefile

Neste tutorial, você aprenderá a integrar o Citrix Sharefile ao Azure Active Directory (Azure AD).
A integração do Citrix Sharefile ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Citrix sharefile.
* Você pode permitir que seus usuários façam logon automaticamente no Citrix Sharefile (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Citrix Sharefile, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Citrix Sharefile

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Citrix Sharefile dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Adicionando o Citrix Sharefile da Galeria

Para configurar a integração do Citrix Sharefile ao Azure AD, você precisa adicionar o Citrix Sharefile da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Citrix Sharefile da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Citrix Sharefile**, selecione **Citrix Sharefile** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Citrix Sharefile na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Citrix Sharefile, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Citrix sharefile.

Para configurar e testar o logon único do Azure AD com o Citrix Sharefile, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Citrix Sharefile](#configure-citrix-sharefile-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Citrix Sharefile](#create-citrix-sharefile-test-user)** – para ter um equivalente de Brenda Simon no Citrix Sharefile que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Citrix Sharefile, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Citrix Sharefile** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Citrix Sharefile](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<tenant-name>.sharefile.com/saml/login`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:
    
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do Citrix Sharefile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar o Citrix Sharefile** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-citrix-sharefile-single-sign-on"></a>Configurar o logon único do Citrix Sharefile

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do **Citrix Sharefile** como administrador.

2. Na barra de ferramentas na parte superior, clique em **admin**.

3. No painel de navegação esquerdo, selecione **Configurar logon único**.
   
    ![Administração da conta](./media/sharefile-tutorial/ic773627.png "Administração da conta")

4. Na página da caixa de diálogo **configuração de logon único/SAML 2,0** em **configurações básicas**, execute as seguintes etapas:
   
    ![Início de sessão único](./media/sharefile-tutorial/ic773628.png "Início de sessão único")
   
    a. Clique em **habilitar SAML**.
    
    b. Em sua caixa de texto **ID de entidade/emissor IDP** , Cole o valor do **identificador do Azure ad** que você copiou do portal do Azure.

    c. Clique em **alterar** ao lado do campo **certificado X. 509** e, em seguida, carregue o certificado baixado do portal do Azure.
    
    d. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon** que você copiou do portal do Azure.
    
    e. Na caixa de texto **URL de logout** , Cole o valor da **URL de logout** que você copiou do portal do Azure.

5. Clique em **salvar** no portal de gerenciamento do Citrix sharefile.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Citrix sharefile.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Citrix Sharefile**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Citrix Sharefile**.

    ![O link do Citrix Sharefile na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-citrix-sharefile-test-user"></a>Criar usuário de teste do Citrix Sharefile

Para permitir que os usuários do AD do Azure façam logon no Citrix Sharefile, eles devem ser provisionados no Citrix sharefile. No caso do Citrix Sharefile, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Citrix Sharefile** .

2. Clique em **gerenciar usuários \> gerenciar usuários página inicial \> + criar funcionário**.
   
    ![Criar funcionário](./media/sharefile-tutorial/IC781050.png "Criar funcionário")

3. Na seção **informações básicas** , execute as etapas abaixo:
   
    ![Informações básicas](./media/sharefile-tutorial/IC799951.png "Informações básicas")
   
    a. Na caixa de texto **endereço de email** , digite o endereço de email de Brenda Simon como **brendafernandes\@contoso.com**.
   
    b. Na caixa de texto **nome** , digite o **nome** do usuário como **Brenda**.
   
    c. Na caixa de texto **sobrenome** , digite o **sobrenome** do usuário como **Simon**.

4. Clique em **Adicionar usuário**.
  
    >[!NOTE]
    >O titular da conta do Azure AD receberá um email e seguirá um link para confirmar sua conta antes que ela se torne ativa. Você pode usar qualquer outra ferramenta de criação de conta de usuário do Citrix Sharefile ou APIs fornecidas pelo Citrix Sharefile para provisionar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Citrix Sharefile no painel de acesso, você deverá ser conectado automaticamente ao Citrix Sharefile para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

