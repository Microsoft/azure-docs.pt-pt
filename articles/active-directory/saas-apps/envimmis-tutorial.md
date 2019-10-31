---
title: 'Tutorial: integração do Azure Active Directory com o ENVI MMIS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ENVI MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1689517042713e9a3ce75c6ada822cb3d6ff138
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158263"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Tutorial: integração do Azure Active Directory com o ENVI MMIS

Neste tutorial, você aprenderá a integrar o ENVI MMIS ao Azure Active Directory (Azure AD).
A integração do ENVI MMIS ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao ENVI MMIS.
* Você pode permitir que seus usuários façam logon automaticamente no ENVI MMIS (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ENVI MMIS, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do ENVI MMIS

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* ENVI MMIS dá suporte ao **SP** e ao SSO iniciado pelo **IDP**

## <a name="adding-envi-mmis-from-the-gallery"></a>Adicionando o ENVI MMIS da Galeria

Para configurar a integração do ENVI MMIS ao Azure AD, você precisará adicionar o ENVI MMIS da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ENVI MMIS da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ENVI MMIS**, selecione **ENVI MMIS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ENVI MMIS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ENVI MMIS, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ENVI MMIS.

Para configurar e testar o logon único do Azure AD com o ENVI MMIS, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do ENVI MMIS](#configure-envi-mmis-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do ENVI MMIS](#create-envi-mmis-test-user)** – para ter um equivalente de Brenda Simon no ENVI MMIS que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ENVI MMIS, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ENVI MMIS** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do ENVI MMIS](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do ENVI MMIS](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do ENVI MMIS](mailto:support@ioscorp.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de metadados de **Federação** das opções especificadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

7. Na seção **Configurar ENVI MMIS** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-envi-mmis-single-sign-on"></a>Configurar o logon único do ENVI MMIS

1. Em uma janela diferente do navegador da Web, entre no site do ENVI MMIS como administrador.

2. Clique na guia **meu domínio** .

    ![Configurar botão salvar de logon único](./media/envimmis-tutorial/configure1.png)

3. Clique em **Editar**.

    ![Configurar botão salvar de logon único](./media/envimmis-tutorial/configure2.png)

4. Selecione a caixa de seleção **usar autenticação remota** e, em seguida, selecione **redirecionamento http** no menu suspenso **tipo de autenticação** .

    ![Configurar botão salvar de logon único](./media/envimmis-tutorial/configure3.png)

5. Selecione a guia **recursos** e clique em **carregar metadados**.

    ![Configurar botão salvar de logon único](./media/envimmis-tutorial/configure4.png)

6. No pop-up **carregar metadados** , execute as seguintes etapas:

    ![Configurar botão salvar de logon único](./media/envimmis-tutorial/configure5.png)

    a. Selecione a opção **arquivo** na lista suspensa **carregar de** .

    b. Carregue o arquivo de metadados baixado de portal do Azure selecionando o **ícone escolher arquivo**.

    c. Clique em **OK**.

7. Depois de carregar o arquivo de metadados baixado, os campos serão preenchidos automaticamente. Clique em **Atualizar**

    ![Configurar botão salvar de logon único](./media/envimmis-tutorial/configure6.png)

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao ENVI MMIS.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **ENVI MMIS**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ENVI MMIS**.

    ![O link ENVI MMIS na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na Asserção SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-envi-mmis-test-user"></a>Criar usuário de teste do ENVI MMIS

Para permitir que os usuários do Azure AD entrem no ENVI MMIS, eles devem ser provisionados no ENVI MMIS. No caso do ENVI MMIS, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site da empresa do ENVI MMIS como administrador.

2. Clique na guia **lista de usuários** .

    ![Adicionar funcionário](./media/envimmis-tutorial/user1.png)

3. Clique no botão **Adicionar usuário** .

    ![Adicionar funcionário](./media/envimmis-tutorial/user2.png)

4. Na seção **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar funcionário](./media/envimmis-tutorial/user3.png)

    a. Na caixa de texto **nome de usuário** , digite o nome da conta do Brenda Simon, como **brendafernandes\@contoso.com**.
    
    b. Na caixa de texto **nome** , digite o primeiro nome de brendafernandes, como **Brenda**.

    c. Na caixa de texto **sobrenome** , digite o sobrenome de Brendafernandes como **Simon**.

    d. Insira o título do usuário no **título** da caixa de texto.
    
    e. Na caixa de texto **endereço de email** , digite o endereço de email da conta Brenda Simon, como **brendafernandes\@contoso.com**.

    f. Na caixa de texto **nome de usuário do SSO** , digite o nome de usuários da conta Brenda Simon, como **brendafernandes\@contoso.com**.

    g. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco ENVI MMIS no painel de acesso, você deverá ser conectado automaticamente ao ENVI MMIS para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

