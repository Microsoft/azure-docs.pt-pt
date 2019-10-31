---
title: 'Tutorial: integração do Azure Active Directory com o etouches | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o etouches.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fb9bdfad1480e47eba919d6884f2042f11a2b9c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158240"
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Tutorial: integração do Azure Active Directory com o etouches

Neste tutorial, você aprenderá a integrar o etouches com o Azure Active Directory (Azure AD).
A integração do etouches ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao etouches.
* Você pode permitir que seus usuários sejam conectados automaticamente ao etouches (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao etouches, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* assinatura habilitada para logon único do etouches

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* o etouches dá suporte ao SSO iniciado por **SP**

## <a name="adding-etouches-from-the-gallery"></a>Adicionando o etouches da Galeria

Para configurar a integração do etouches ao Azure AD, você precisará adicionar o etouches da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o etouches da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **etouches**, selecione **etouches** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![etouches na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o etouches, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no etouches.

Para configurar e testar o logon único do Azure AD com o etouches, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do etouches](#configure-etouches-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do etouches](#create-etouches-test-user)** – para ter um equivalente de Brenda Simon no etouches que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o etouches, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **etouches** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![informações de logon único de domínio e URLs do etouches](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Esses valores não são reais. Você atualiza o valor com a URL de entrada e o identificador reais, que são explicados posteriormente no tutorial.
    > 

5. Seu aplicativo etouches espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para adicionar os atributos.

    ![imagem](common/edit-attribute.png)

6. Além de acima, o aplicativo etouches espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Atributo de origem|
    | ------------------- | -------------------- |
    | E-mail | User. mail | 

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**

    g. Clique em **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de metadados de **Federação** das opções especificadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

8. Na seção **Configurar etouches** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-etouches-single-sign-on"></a>Configurar o logon único do etouches

1. Para que o SSO seja configurado para seu aplicativo, execute as seguintes etapas no aplicativo etouches: 

    ![configuração do etouches](./media/etouches-tutorial/tutorial_etouches_06.png) 

    a. Entre no aplicativo **etouches** usando os direitos de administrador.
   
    b. Vá para a configuração do **SAML** .

    c. Na seção **configurações gerais** , abra o certificado baixado de portal do Azure no bloco de notas, copie o conteúdo e cole-o na caixa de texto metadados IDP. 

    d. Clique no botão **salvar & permanecer** .
  
    e. Clique no botão **atualizar metadados** na seção de metadados SAML. 

    f. Isso abre a página e executa o SSO. Depois que o SSO estiver funcionando, você poderá configurar o nome de usuário.

    g. No campo nome de usuário, selecione o **EmailAddress** , conforme mostrado na imagem abaixo. 

    h. Copie o valor da **ID da entidade SP** e cole-o na caixa de texto **identificador** , que está na seção **configuração básica do SAML** em portal do Azure.

    i. Copie o valor de **URL/ACS de SSO** e cole-o na caixa de texto **URL de logon** , que está na seção de **configuração básica do SAML** em portal do Azure.
   
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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao etouches.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **etouches**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **etouches**.

    ![O link do etouches na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na Asserção SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-etouches-test-user"></a>Criar usuário de teste do etouches

Nesta seção, você criará um usuário chamado Brenda Simon no etouches. Trabalhe com a [equipe de suporte ao cliente do etouches](https://www.etouches.com/event-software/support/customer-support/) para adicionar os usuários na plataforma do etouches.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do etouches no painel de acesso, você deverá ser conectado automaticamente ao etouches para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

